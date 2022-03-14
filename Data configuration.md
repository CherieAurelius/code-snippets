# 1. How to set properties in a model
@ datamodels-> model , fill in properties e.g.(inherits everything from IdbsObject)
```
public class Employee : IdbsObject
	{
		public string Supervisor { get; set; }

```

# 2. How to seed data
1. Go to configuration.cs
2. Give your nseeder a name e.g. SeedEmployees
```
namespace Datamodel.Migrationsv {
private static void SeedEmployees(IDataContext context)
}

```

3. Make sure it returns
```
	if (!TableExists(context, "Employees"))
			{
				return;
			}
``


4. Create data , for example a new list of employees (read from right to left, the employeestoAdd contains a new list of employees)
```
var employeesToAdd = new List<Employee>
			{
				new Employee
				{
					Name = "Allard",
					LastName = "Struiksma",
					BirthDate = new DateTime(1994, 5, 4)
				},
				new Employee
				{
					Name = "Fauve",
					LastName = "Setrodinomo",
					BirthDate = new DateTime(2001, 3, 4)
				},
			};      
```

5. var Employees means : take the first name and last name  if this is null create a employee
```
var employee = context.Employees.FirstOrDefault(e => e.Name == employeeToAdd.Name && e.LastName == employeeToAdd.LastName);
Name = employeeToAdd.Name,
						LastName = employeeToAdd.LastName,
						BirthDate = employeeToAdd.BirthDate
					}.SetIdbsCreateAndUpdatedPropertiesAndPublish());
```



# 3. How to create commands? 
1. Create a directory for your table
2. create a command , e.g. create a list from the model Employeemodel (employeemodel.cs with properties Name and Lastname)
```
internal class GetEmployeesCommand : BaseCommand, INetsupportGenericDbCommand<DataContext, IList<EmployeeModel>>
	{

		public IList<EmployeeModel> Execute(DataContext context)
		{
			var employees = context.Employees.Published()
				.ProjectTo<EmployeeModel>(Mapper.Config).ToList();
			return employees;
		}
```

# 4. Create a service

```
public class EmployeeService
	{
		private readonly IDataContext _context;				// Create a property 

		public EmployeeService(IDataContext context) => _context = context; 		// get a property 

		public IList<EmployeeModel> Get() => _context.Execute<GetEmployeesCommand, IList<EmployeeModel>>(); *
	}
}

```
## * Function IList  < Get ()> executes the command and returns (as the the name) IList EmployeeModel


# 5. Create a controller
```
	public class EmployeeController : Controller
	{
		protected IDataContext Context => Request.GetOwinContext().Get<IDataContext>();

		public ActionResult Index()
		{
			var employeeService = new EmployeeService(Context);	// Constructor , Nieuwe instantie van employee service ,() kan anderen dingen in
			var employees = employeeService.Get();				// Je gebruikt van EmployeeService de GET functier, return list van EmployeeModel
			var model = new EmployeePageViewModel(6);       // Nieuwe instantie van EmployeePageViewModel , int number , string bleh , en die lege lijst employeesModel
			var number = model.Number;		// Get property
			model.Number = 12;              // Set property
			model.Employees = employees;
			model.Test = " test";
			return View("Index", model);

```


6. Create a view
```
@using NetsupportNet.Helpers
@model WebApplication.Viewmodels.Pages.EmployeePageViewModel

@{
	Layout = null;
}

<p>@Model.Test</p>				
<p>@Model.Number</p>
@foreach (var employee in Model.Employees) {
	<div>@employee.Name @employee.LastName</div>				// Goes to the DataModel.Datamodels.EmployeeModel.cs => EmployeeModel , easier: EmployeeModel.Name , BUT you made a var to grab EmployeeModel  so that is the reason why :
	
}
* var employee in Model.Employee means grab datamodel : EmployeeModel , name it Employee and you can grab the properties with .
e.g @employee.Name
```



```
