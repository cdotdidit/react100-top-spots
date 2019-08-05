Instructions: Top Spots
Introduction
In this project, we will be building a small informational website containing a list of 30 "top places to see" in San Diego, which we will refer to as "Top Spots".

To accomplish this, we will need to:

Include CSS styling provided by Bootstrap
Create a container component to manage application state.
Create a presentational component to render a single top spot.
Render a collection of Top Spots in the container component.
Getting Started
Open a terminal
Clone your starter files from Github and open the project in VS code.
Run: npm install to install all the dependencies.
As you add code to the project, you will want to view your progress in a browser. To do this, first type npm run build to compile your project. Then, type npm start to launch your project on a local webserver. In your console window, you should see the output Server is listening on port 3000. Open a browser to the url, http://localhost:3000/ to render the project and view your progress.
The tests will confirm if you have completed the requirements. Type npm test and hit enter to run the tests. Then, write your code using the steps below. When you have written all the code to complete the project (based on the exit criteria) and the tests are passing, submit the assignment.
Step 1 - Include CSS styling provided by Bootstrap.
This project uses Bootstrap for styling. Find Bootstrap on cdnjs.com and paste the <link> tag in the correct place within /public/index.html.

Step 2 - Create a container component.
Now that Bootstrap is in place, the first file we are going to focus our efforts on is /src/app.jsx. Complete the tasks below and then move on to the next section.

Add elements to match the provided screenshot

In the render method of /src/app.jsx, go ahead and write the necessary JSX to render the following wireframe:

Fun fact: You can find some nice looking color gradients to use in your CSS (e.g. setting the background color) at UI Gradients.



Next, we need to render the 30 "top spots". Now, we could simply type up each one in JSX, but that's difficult to maintain over time. Instead - let's load the data from an external web service and use a loop to render a template for each iteration.

First, let's checkout the web service and the data it exposes:

Click on this link to have your browser do a GET request to the api: https://codepen.io/merobertsjr/pen/WayqqB.js

Note: Don't worry about he js file extention on the url, we are just using a little hack to have the api hosted easily via Code Pen.

The JSON returned from that URL contains an array of objects, where each object contains the following properties:

{
    "name": "Name of the top spot",
    "description": "Some words about the top spot",
    "location": [
        32.122843,
        -127.124844
    ]
}
The name and description fields are self descriptive, the tricky one here is the location property - which contains an array of two values representing the latitude and longitude co-ordinates (or location) of that top spot. We'll eventually use these to generate a link that'll take the user to Google Maps and place a pin at that location.

You can do a lot with lat/long values, such as plotting positions on a Google Map or calculating the most efficient route through a set of lat/long pairs.

To download this data in our React app, first we'll need to set a few things up in the App component.

In /src/app.jsx, add a constructor to the App class as follows:

export default class App extends Component {
    constructor(props) {
        super(props);

        this.state = {
            topspots: []
        };
    }
}
This sets up a function that will be among the first invoked when the App component is first instantiated for mounting to the DOM. props and super(props) are mostly there for boilerplate, the main reason we created this constructor is the assigning of an initial state object to this.state, which contains the array where we will store the data structure returned by our web service.

Now that we have setup a mechanism for storing our data - let's write the code to actually go get it.

Before we continue - we're going to need to grab a package from npm called axios. Take a few minutes to checkout their README.md to see what it does.

Essentially, axios is a promise-based HTTP client that you can program in NodeJS. We will use it to make a GET request to the URL specified earlier, then use the data returned in the response to map out that data into pretty-looking React components.

Go ahead and install axios into the project, then add a componentWillMount Lifecycle Hook to /src/app.jsx.

$ npm install axios --save
export class App extends Component {
    constructor(props) {
        super(props);

        this.state = {
            topspots: []
        }
    }

    componentWillMount() {

    }
}
You may opt to add the following code to the constructor() function as per the specifications in the latest version of the React documentation.

Next, add the following code to your componentWillMount function to tell React to make an HTTP GET request to our external web service when the component is about to be mounted to the DOM.

axios
    .get('https://origin-top-spots-api.herokuapp.com/api/topspots')
    .then(response => response.data)
    .then(topspots => this.setState({ topspots }));
Let's destruct this code a little bit to understand what it's doing.

// Here we're simply invoking the axios object, specifically.. 
axios

    // .. the .get function. Here we pass the URL of our external service.
    .get('https://origin-top-spots-api.herokuapp.com/api/topspots')

    // Here the first fulfill callback is setup.
    // This callback simply takes an HTTP response and returns the data property..
    .then(response => response.data) 

    // .. to the second fulfill callback, which uses React's this.setState
    // function to merge the provided object (which uses ES6 shorthand)
    // with the current object assigned to this.state (which you assigned 
    // in your constructor!)
    .then(topspots => this.setState({ topspots }));
To end this step, let's do a quick test to see that the data is being downloaded successfully. Add the following JSX to the render() method of /src/app.jsx.

<pre>{ JSON.stringify(this.state.topspots, null, 2) }</pre>
To see where that JSON.stringify code came from, see this Stack Overflow answer which has become a standard solution for this problem.

You should now be able to run the app by running npm run start to launch a web server to host this application and opening http://localhost:3000 in your browser.

If everything goes according to plan - you should be presented with the following webpage:



Great stuff! The data lives in our component, now we're ready to use the data to map out a more human readable version of that object array. Take some time to understand the data structure, then proceed to Step 3.

Step 3 - Create a presentational component.
Now that our container component has done it's job of managing application state by downloading data at the right time, it's time to create a quick presentational component to turn our data into something more visual.

Create a new file called topspot.jsx in /src and add the following code to get started.

import React from 'react';

export default props => (

);
This is the basis of a stateless component in React. What follows is a compiled list of resources that discuss stateless components. You should take some time to read each one to gain understanding of why the project is setup this way.

Functional Stateless Components in React
React Stateless Functional Components: Nine Wins You Might Have Overlooked
React Documentation
For this stateless component, we shall assume that the props being passed to this function are: props.name, props.description and props.location[0]/props.location[1].

Given these props, we can render the following JSX markup inside the ES6 function we created in topspot.jsx.

<div className='well'>
    <h4>{props.name}</h4>
    <p>{props.description}</p>
</div>
Step 4 - Render a collection of Top Spots in the container component.
We're now ready to step back into our container component (app.jsx) to map out the array into a collection of the presentational component we just created.

Before continuing - it will be immensely helpful to spend some time brushing up on how the .map() function works and making it stick by going over it with a classmate/instructor. While it is used heavily in React to generate a list of components, it's functionality is broad and applicable to a number of programming scenarios.

First, import your presentational component by adding the following to the beginning of app.jsx.

import TopSpot from './topspot';
Then, add this code to the render() method of app.jsx below the jumbotron you created earlier.

{ 
    this.state.topspots.map(topspot => (
        <TopSpot
            key={topspot.id}
            name={topspot.name}
            description={topspot.description}
            location={topspot.location} />
    ))
}
Once you have added that code to the correct spot in your render function - you should be able to see the following when you start the application:



All that's left is to add a button to each top spot that redirects the user to the following URL (swapping {lat} and {lon} for their real values).

https://maps.google.com/?q={lat},{lon}
You've got it from this point - use everything you've learned, utilizing the web and your classmates/instructors when you get stuck.

Don't forget - you need to make the provided unit tests pass in order to submit the project and continue to the next project.

Bonus
Add distance (in miles) from current location to each result.
Add hover effect over each row in the table that will display an image or some other interesting info about the top spot.
Project Submission
To submit this project, commit your work to Github and please deploy this website using Now.

# navigate to the project
$ cd ~/projects/{project-name}

# run "now" to deploy the site
$ now
After running that command, you should see the following output:

> Deploying ~/projects/{project-name} under your@email.com
> Ready! https://{project-name}-skckceswsd.now.sh (copied to clipboard)
Submit your Now URL using the link at the bottom to navigate to the submission page, and do it again once more for your github repository URL on the following page. Once you submit both URLs for the project, you can move onto the next assignment. An instructor will evaluate your work at a later time, giving feedback if necessary