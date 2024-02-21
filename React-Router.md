# Welcome to React  Router

To implement React router, package needed is **react-router-dom**

## Steps to Routing:
1. Define a list of  routes in the application and which component to be loaded for those individual routes
2. Activate the router and load the route definition
3. Create the means of navigation

### Implementation
1. **createBrowserRouter** function creates the  definition of routes which takes in an array of object having item path(the path that will be used to route) and element(containing the jsx which will be dispalyed)
2. **RouterProvider** component surrounds the application which is covered by the routes
Example:
```
const router = createBrowserRouter([{
path: "/",
element: <Layout/>,
errorElement:<ErrorPage/>,
children: [
{ path: "/", element: <HomePage  /> },
{ path: "/products",element: <ProductPage  />,},
{ path: "/products/:productId", element:<ProductDetailPage/>}
],},]);

function App() {return  <RouterProvider  router={router}/>;}
```
3. Now here we create the Navigation Link by import **Link** from react-router-dom
```
<p>Go to <Link  to="/products">This list of products</Link></p>
```
4. **Outlet** marks the place where the children will be displayed
5. To support currently active link react-router-dom has the alternative of Link as **NavLink**. It has the property className which takes in a function as 
```
<NavLink to="/products" className={({ isActive }) => isActive ? classes.active : undefined} end>
Products </NavLink>
```
6. **useNavigate** hook returns a navigate function which can be used to navigate programmatically for example navigation after timeout.
```
const navigate=useNavigate();
function navigateHandler(){
	navigate("/products");
}
<button onClick={navigateHandler}>Navigate</button>
```
7. Dynamic Routes: **useParams** hook returns the object with all the dynamic route identifiers  
```
{path:"/products/:productId",element:<ProductDetailPage/>}
function ProductDetailsPage(){
	const params= useParams();
	return <p>{params.productId}</p>
}
```
8. To append dynamic properties to existing route:
```
<Link to={`/products/${prod.id}`}>{prod.title}</Link>
```
9. Path starting with "/" is an **absolute path** which appends directly after the domain name, and path which does **not** start with "/" are **relative path**, means this path will be appended after the parent route path
10.**Link** provides an option to set the relative of a current path. It just moves back to 1 step in the current path. 
```
<Link to=".." relative="path">Back</Link> 
``` 
10. Index Route- If we have a wrapping layout and have 1 child path probably the first one, that is same as the parent one or is the default route it can be marked as "index:true" instead of "path:"/"

11. Loader is a function that executed when we are about to navigate a path and hence its triggered before the JSX of that component is rendered. It returns the data to the current path and also whoever needs it.
```
{index: true,element: <EventsPage/>,loader: eventsLoader},
```
this `eventsLoader` function will be written in the `EventsPage` component
the **useLoaderData** returns the closest loader data in the current component by `const data = useLoaderData();` Data will be available in the child components or same level but not on parent level.

Example Loader function
```
export  async  function loader() {
	const response = await fetch("http://localhost:8080/events");
	if (!response.ok) {
	throw json({ message: "Something went wrong" }, { status: 500 });
	} else {
	const resData = await response.json();
	return resData;
}
```
To use dynamic parameters in the loader function, we can use it in the following way, loader accepts 2 parameters request and param. This param is similar to useParam containing all the dynamic route properties

```
export  async  function loader({ request, params }) {
	const id = params.eventId;
	const response = await fetch("http://localhost:8080/events/" + id);
	if (!response.ok) {
	throw json({ message: "Could not fetch event details" }, { status: 500 });
	} else 
	return response;
}
```

12. **useNavigation** hooks provide details about if there is a current navigation going on or not its state property return value like idle, completed or loading. It should be added to a page which is already rendered and not on the current page that we are about to navigate. It can be used to handle the state when loader has not yet returned data but is in progress.

13. **useRouteLoaderData** helps in fetching the loader data from another route using the `id` which is mentioned in the route description storing the loader data as `const data = useRouteLoaderData("event-detail");`

14. Similar to loader which loads data in a component, action is a function which submits the data. It is another async function that can send response to the backend. To handle this the form should be replaced with **Form** provided by react-router-dom
```
 export  async  function action({ request, params }) {
	const data = await request.formData();
	const method = request.method;
	const eventData = {
		title: data.get("title"),
		image: data.get("image"),
		description: data.get("description"),
		date: data.get("date"),
	};
	let url = "http://localhost:8080/events";
	if (method === "PATCH") {
	const eventId = params.eventId;
	url = "http://localhost:8080/events/" + eventId;
	}
	const response = fetch(url, {
	method: method,
	headers: {
	"Content-Type": "application/json",
	},
	body: JSON.stringify(eventData),
	});

	if (response.status === 422)  return response;
	if (!response.ok) throw json({ message: "Could not save event" }, { status: 500 });

	return redirect("/events");
}
```
15. **useSubmit** hook allows to call the action function apart from creating the Form. It helps in calling an action programmatically. this hook returns a function
16. **useActionData** returns the data returned by the action
17. **useFetcher** should be used when we do not want to navigate to the component that has the action or the loader which we want to execute