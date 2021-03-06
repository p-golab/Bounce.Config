# Web.config Templates

Use Bounce.Config to template your web.configs with [bounce](https://github.com/refractalize/bounce).

Bounce.Config uses mustache to generate your web.configs from a JSON file and a web.template.config file.

Put this into a JSON file, anywhere on the internet or filesystem, let's put it into `http://internal/myproject/staging.json`:

    {
        "db": "db-1.internal",
        "greeting", "cakes are yummy, yeah?"
    }

And put this into `web.template.config` in your project directory:

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <connectionStrings>
        <add name="MyDatabase" connectionString="{{db}}" />
      </connectionStrings>
      <appSettings>
        <add key="Greeting" value="{{greeting}}" />
      </appSettings>
    </configuration>

Bounce.Config adds a `Configure` extension method to bounce's `IVisualStudioProject`, so you can generate your `web.config` by running code like this:

    using Bounce.Config;

    ...

    [Task]
    public void Configure(string env) {
        var sln = new VisualStudio().Solution("MySolution.sln");
        sln.Projects["MyProject"].Configure(env);
    }

Then

    > bounce configure /env http://internal/myproject/staging.json

And you'll see a `web.config` with all the right things in it. If you're writing an exe, you should see `bin\Debug\myprogram.exe.config` too.

# Api

Configure your project:

    var sln = new VisualStudio().Solution("MySolution.sln");
    IVisualStudioProject project = sln.Projects["MyProject"];

    project.Configure(env);

Where `env` is either a URL or a filename of a JSON file.

You can also pass a `Dictionary<string, object>` of settings to replace, all objects are converted with `.ToString()`. Bounce.Config uses [Nustache](https://github.com/jdiamond/Nustache) for templating, check it out for the lovely details.

Access to lower level functions are on the `TemplateConfigurer` object.
