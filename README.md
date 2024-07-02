# todolist-vue

Simple todo list with realtime subscriptions using _VueJS_ + _DynamoDB_ + _AppSync_ + _GraphQL_

## AWS Infrastructure

![AWS Infrastructure](https://github.com/mirzaakhena/todolist2-vue/blob/main/infrastructure.png)

## GraphQL Schema

```
type Todo {
  id: ID
  title: String
  completed: Boolean
}
type Query {
  getTodos: [Todo]
}
type Mutation {
  addTodo(title: String!): Todo
  updateTodo(id: ID!, completed: Boolean!): Todo
  deleteTodo(id: ID!): Todo
}
type Subscription {
  onTodoChanged: Todo
    @aws_subscribe(mutations: ["addTodo", "updateTodo", "deleteTodo"])
}
schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
}
```

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Cloud Formation Setup

0. Adjust the `cloud_formation.yml`

1. Create a Cloud Formation stack

   ```
   aws cloudformation create-stack --stack-name TodoAppStack --template-body file://cloud_formation.yml --capabilities CAPABILITY_IAM
   ```

2. Describe stack to get the _api_key_ and _graphQL url_

   ```
   aws cloudformation describe-stacks --stack-name TodoAppStack
   ```

   sample output :

   ```
   {
       "Outputs": [
           {
               "OutputKey": "ApiKey",
               "OutputValue": "da2-abcma234erfyf12345l3e39876",
               "Description": "AppSync API Key",
               "ExportName": "AppSyncApiKey"
           },
           {
               "OutputKey": "GraphQLUrl",
               "OutputValue": "https://1234rr7me5abckkrxzex377777.appsync-api.us-east-1.amazonaws.com/graphql",
               "Description": "AppSync GraphQL URL",
               "ExportName": "AppSyncGraphQLUrl"
           }
       ]
   }
   ```

3. create file `src/amplifyconfiguration.json` based on the previous output

   ```
   {
     "aws_project_region": "us-east-1",
     "aws_appsync_graphqlEndpoint": "https://1234rr7me5abckkrxzex377777.appsync-api.us-east-1.amazonaws.com/graphql",
     "aws_appsync_region": "us-east-1",
     "aws_appsync_authenticationType": "API_KEY",
     "aws_appsync_apiKey": "da2-abcma234erfyf12345l3e39876"
   }
   ```

4. Add new file then do `git commit` to update the changes. It will trigger the CI/CD automatically to build and deploy to _Amplify_

   ```
   $ git add .
   $ git commit -am "adding amplifyconfiguration json"
   ```

5. Go to [AWS Amplify Console](https://us-east-1.console.aws.amazon.com/amplify/apps) to get the application URL.


