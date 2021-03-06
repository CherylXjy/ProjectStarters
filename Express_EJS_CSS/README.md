# How to open
> node app.js  
---
## How it work?
> In terminal, type ```node app.js```, then, node will run ```app.js```, and start the express app. In express app, on get request, the targeted route will do ```res.render(<filename>)```, express will lookup the file under views folder, and render it to the browser.
>
> This app has simple get and post request set up. No Database support. There's a fake database support by using temp varaibles. But on restart the app, they are gone.
>
> go to localhost:3000 to see the app
>
## File Structure
```
views folder: when sending back html as response, express first look inside views directory. Whenever we render a file whatever it's called, express is going to look first in the views directory. Say we are res.render("home.ejs"), it will try to find home.ejs in views directory no matter if you created it or not.

.
|____app.js                 (same as server.js, where we start app)
|____package.json
|____public                 (where we put files need to be served)
| |____app.css              (linked in header.ejs)
|____README.md
|____views                  (auto served by express, rendered when get request)
| |____home.ejs             (home page, "/")
| |____love.ejs              
| |____friends.ejs            
| |____partials             (ejs partials, include in every ejs filews (some people call it "layout"))
| | |____footer.ejs
| | |____header.ejs         (contains app.css)
```
---
## Express Notes
In express, it doesn't automaticaly serve anything except the "views" directory. Thus, if we have a css file in public directory, and we try to link to that css file, we need to tell express to serve to the public directory. 

## Why EJS? 
EJS is embedded javascript. It allows you to write javascript code inside html.
```
EJS QUICK TUTORIAL (3 syntax)

1. <%= %>:  between those will be treated as javascript, the value will be returned, and render to HTML
e.g 
<p>I love eat <%= thing.toUpperCase() %></p>

2. <% %> Where we put logic like if statement which won't be rendered to html
e.g 
<h1>You are <%= thingVar %></h1>
<% if(thingVar.toLowerCase() === "rusty"){ %>
    <p>Good CHoice! Rusty is the best</p>
<% } else { %>
    <p>Bad CHoice! Rusty is the best</p>
<% } %>

<!-- for loop example -->
<% for(var i = 0; i < posts.length; i++){ %>
    <li> Post title <%= posts[i].title %> </li>
<% } %>

<!-- for each exmaple -->
<% posts.forEach(function(post){ %>
    <li> Posts title <%= post.title %> </li>
<% }) %>

3. EJS also provide partials, say we have write up a header.ejs, we want to use the same header in home.ejs, the we just need the following line

<% include ./partials/header %>
```

## Project Dependency Setup
```
npm install ejs --save
npm install express --save
npm install body-parser --save
```
 
## Notes for express and ejs communication, and post request
---
### How to pass paramters from express to ejs template? 
---
In Browser, if you type
```
localhost:3000/fallinlovewith/rusty
```
This makes a get request to router inside app.js, which look like
```
app.get("/fallinlovewith/:thing", function(req, res){
    var thing = req.params.thing;
    res.render("love.ejs", {thingVar: thing});
});
```
As you can see, once the get route is reached, express need to send sth back as response. Here, response is a file called ```love.ejs```, along with the file is a variable called ```thingVar``` with value being ```Rusty``` which is stored in ```thing``` variable.


And In love.ejs, thingVar will be replaced by the value Rusty and render to the browser
```
<h1>You are <%= thingVar %></h1>
<p>This is love.ejs file</p>
```

---
### How to make a post request
---
Simple Post Route Demo


In posts.ejs

Under input, we need to specify a name, and then, the value of name will be send in the body of the request once the user click the button. It will send as a ```POST``` request, and send to ```/addfriend``` route. And inside the body of the new request, there be a single property named ```newfirend```.

```
<form action="/addfriend" method="POST">
    <input type="text" name="newfriend" placeholder="name">
    <button>Add new Friend</button>
</form>
```

Notice: express doesn't actually create the req.body for us, we need to tell express specifially to take request.body and turn it into a javascript object for us to use called request.body.

In order to do that, we need to install a package called ```bodyParser```. Now, bodyparser is something that people use in every express app. Any time we have a form that a user enteres data into that we want to extract the data from on the server side. We need to use bodyparser.  

```
var app = express();
var bodyParser = require("body-parser");
app.use(bodyParser.urlencoded({extended: true}));
```

Now, pretend you clicked the button.

Post route is activateed, and you get newfriend from req.body (created by body-parser). and you save new friend to the array above, and ask express to re-render the page friends.
```
var friends = ["Tony", "Eric", "Lily"];

app.post("/addfriend", function(req, res){

    var newfriend = req.body.newfriend;
    friends.push(newfriend);
    res.redirect("/friends"); 

});

```

And response will redirect to render friends page. And for that, this is how we rendered it.
Thus, the updated friends array will be passed in. And friends.ejs will be displayed on website.

```
app.get("/friends", function(req, res){
    var posts = [
        {title: "lala"},
        {title: "mama"},
        {title: "tata"},
        {title: "qula"},
    ];
    
    res.render("friends.ejs", {posts: posts, friends, friends})
});
```





