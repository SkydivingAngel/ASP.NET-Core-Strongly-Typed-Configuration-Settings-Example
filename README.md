ASP.NET Core 2 Strongly Typed Configuration Settings Example

1) Create a Class based on "appsettings.json":

Example of appsettings.json:

	{
	  "AppSettings": {
		"Jwt": {
		  "Key": "xz$4@ah%wga&4&u$7e^4w@v47o9(1hkv0e7&@*3dt2lci=xeeaijzx",
		  "TokenExpirationInMinutes": "30",
		  "Subject": "WebService Core 2",
		  "Issuer": "WebServiceCore2",
		  "Audience": "WebServiceCore2"
		},
		"Credentials": {
		  "Username": "user",
		  "Password": "pwd"
		},
		"IpsAllowed": [
		  "88.120.78.11",
		  "88.120.78.12",
		  "88.120.78.13"
		]
	  }
	}

Example of Class containing "appsettings":

	public class AppSettings
	{
		public Jwt Jwt { get; set; }
		public Credentials Credentials { get; set; }
		public List<string> IpsAllowed { get; set; }
	}

	public class Jwt
	{
		public string Key { get; set; }
		public int TokenExpirationInMinutes { get; set; }
		public string Subject { get; set; }
		public string Issuer { get; set; }
		public string Audience { get; set; }
	}

	public class Credentials
	{
		public string Username { get; set; }
		public string Password { get; set; }
	}

2)In Startup.cs add the following line:

	public void ConfigureServices(IServiceCollection services)
	{
		//this will deserialize "appsettings.json" to "AppSettings" Class
		//and will make the class available for Dependency injection
		services.Configure<AppSettings>(Configuration.GetSection("AppSettings"));
				
	
3)Use Dependency Injection to pass the Class "AppSettings":

Example of DI in controller:

	public class MainController : Controller
	{
		private readonly AppSettings appSettings;
		
		public MainController(IOptionsSnapshot<AppSettings> appSettings)
		{
			this.appSettings = appSettings.Value;
		}

Prefer **"IOptionsSnapshot"** over "IOptions" in order to have always the
latest values from ""appsettings.json".

PS: if you need to get the latest values from a **Middleware** use the following sintax:

        public async Task Invoke(HttpContext httpContext)
        {
            IOptionsSnapshot<AppSettings> appSettingsCurrent = httpContext.RequestServices.GetRequiredService<IOptionsSnapshot<AppSettings>>();
            AppSettings appSettings = appSettingsCurrent.Value;
