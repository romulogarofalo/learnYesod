How to make CRUD REST API/Service with Yesod Web Framework

the last time that i read something here was just the instalations and uping the server, now we gonna start a "blog" to do our frist CRUD \O/ (FINNALY)

but before we start to code we need to understend something about the framework and his structure, like how the routes is build.

in our routes file (path: "/config/routes") we have some routes there but lets use create our own routes that we will use in the project

	/posts               PostR           POST GET 
	/posts/#PostId       PostSpecificR   PUT DELETE
	/posts/#PostId/#Int  PostLike        PATCH

ok now we have our routes lets undertend how then work, in the image bellow its easy to understend

	imagem

lets add the table in the migrations

	Post json
		name Text
		content Text
		likes Int

now we will create a file "Post.hs" in the Handlers folder (path: "/src/Handler/Post.hs") 


the first steps we gonna need to do its adaptade somethings because we gonna make just the crud without the Auth service, to do that go to the file "src/Foundation" and add in the patterning matching this line
	
	isAuthorized _ _ = return Authorized

like in this image
	
	imagem

because without this line if we try to acces the route unAutorized, the server gonna return 500 patterning matching exausted

now we need to add a table in our migrations

	Posts json
		name Text
		content Text
		likes Int



