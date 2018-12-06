How to make CRUD REST API/Service with Yesod Web Framework

the last time that i read something here was just the instalations and uping the server, now we gonna start a "blog" to do our frist CRUD \O/ (FINNALY)

but before we start to code we need to understend something about the framework and his structure, like how the routes is build.

in our routes file (path: "/config/routes") we have some routes there but lets use create our own routes that we will use in the project

		/posts               PostR           POST GET 
	

ok now we have our routes lets undertend how then work, in the image bellow its easy to understend

	imagem

lets add the table in the migrations

	Post json
		name Text
		content Text
		likes Int

(as in the last article when we run the aplication it will make the migration)

the first steps we gonna need to do its adaptade somethings because we gonna make just the crud without the Auth service, to do that go to the file "src/Foundation" and add in the patterning matching this line
	
	isAuthorized _ _ = return Authorized

like in this image
	
	imagem

because without this line if we try to acces the route unAutorized, the server gonna return 500 patterning matching exausted


and we need to declare that we have a new file in handler, to do that add this line in the file Application.hs (Path: "src/Application.hs")

	import Handler.Post

now we will create a file "Post.hs" in the Handlers folder (path: "src/Handler/Post.hs") 

and paste this code

	{-# LANGUAGE OverloadedStrings #-}
	module Handler.Post where

	import Import
	import Database.Persist.Postgresql

put this under the other files on line 47

now we finnaly can start our Crud (づ￣ ³￣)づ

lets first make a register with the POST method, to do that is very easy but we need to understend how the route work (what I tried to explain some lines ago), just paste the code and that it 

	postPostR :: Handler Value
	postPostR = do
		post <- requireJsonBody :: Handler Post
		pid <- runDB $ insert post
		sendStatusJSON created201 (object ["id" .= fromSqlKey pid])

ok, i'm kirring lets explay what is happing here

	imagem

// talvez mais coisas explicando sobre as funcoes

now the GET method 

	getPostR :: Handler Value
	getPostR = do
    	dt <- runDB $ selectList [] [Asc PostId]
    	sendStatusJSON ok200 (object ["content" .= dt])

	imagem explicando o metodo get e blablabla

and POST

	postPostR :: Handler Value
	postPostR = do
		post <- requireJsonBody :: Handler Post
		pid <- runDB $ insert post
		sendStatusJSON created201 (object ["id" .= fromSqlKey pid])

lets build the project with the command 

	stack build

if compile we can run the project 

	stack exec learn-yesod

open another terminal and lets use the Curl (that we had used in the last tutorial) and make a request to the server

to register a post with the POST method

	curl -X POST localhost:3000/posts -d '{"name":"first post", "content":"this is the first post content", "likes":0}'

the response should be 201, you can see the logs in the terminal of the application

now lets get our informations with a GET

	curl -X GET localhost:3000/posts

the response should be 200 ok with the content of the table posts

ok, lets finish the crud with the PUT, PATCH and DELETE method, if you understend how to get and insert works will be easy the other methods, there is just some diferences, lets add the routes

 	/posts/#PostId       PostSpecificR   PUT DELETE
	/posts/#PostId/#Int  PostLike        PATCH

the first diference is after the route "/#PostId" this is a parameter that you will recive as a paramether in the function to get the specific

	putPostSpecificR :: PostId -> Handler Value
	putPostSpecificR  idPost = do
		_ <- runDB $ get404 idPost
		newPost <- requireJsonBody :: Handler Post
		runDB $ replace idPost newPost
		sendStatusJSON noContent204 (object [])

	imagem explicando

patchPostLike :: PostId -> Int -> Handler Value
patchPostLike idPost likes = do
    _ <- runDB $ get404 idPost
    runDB $ update idPost [PostLikes =. likes]
    sendStatusJSON noContent204 (object [])

	imagem explicando

deletePostSpecificR :: PostId -> Handler Value
deletePostSpecificR idPost = do
    _ <- runDB $ get404 idPost
    runDB $ delete idPost
    sendStatusJSON noContent204 (object [])

 	imagem explicando