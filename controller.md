```
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

        }
	}



```
