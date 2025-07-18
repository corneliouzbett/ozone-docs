# Configuring Apps

Now that you have successfully created your Ozone distribution, the next step is to customize and configure it to meet your specific needs. This process involves three main aspects: configuring the apps themselves, configuring integrations between the apps and enabling or disabling apps.

This page focuses specifically on configuring the apps available in the Ozone ecosystem.

The Ozone ecosystem strongly advocates for configuring your server through configuration files rather than alternative methods such as database archives, SQL queries, or manual UI configuration. This approach ensures that you can consistently replicate your production (or soon-to-be production) instance at any time.
By adopting this practice, you gain the flexibility to create multiple environments for various purposes, including development, testing, demonstration, and training, without relying on or anonymizing a production database. Furthermore, this method empowers implementers to maintain full control over the configuration, enhancing overall system management and reproducibility.

Within the skeleton project generated by the Ozone Maven Archetype, you'll find a `configs/` directory. This directory is organized into subfolders, each corresponding to a component of the Ozone HIS distribution.

By default, the `configs/` directory contains only the `openmrs/` sub-folder as an example. However, as you progressively add more configurations, it will gradually expand to include one folder for each enabled app in your Ozone distribution. For example:

```bash
 configs/
    ├── erpnext
    ├── openmrs
    ├── senaite
    └── superset
    ...
```

Each app-specific subfolder can be used to provide the configuration files for that app, thereby overriding the default configuration of the app.

Without providing any particular files your distro will run off of the default apps configurations. Ozone itself is mostly not overriding any configuration, but uses the apps "as is".


!!! warning ""

    Each app's default configuration is the responsibility of its own open-source community.

    Ozone HIS provides these configurations "as is", they are not modified or maintained by the Ozone development team.

## Overriding the OpenMRS 3 Config

This section assumes knowledge of:

- OpenMRS module Initializer
- OpenMRS 3 frontend configuration & architecture

To assist implementers, the OpenMRS 3 config in Ozone is already structured with multiple subfolders that outline the essential configurations:
```bash
   ├── openmrs
         ├── frontend_assembly
         ├── frontend_config
         └── initializer_config
```

### The `frontend_assembly/` folder

The `frontend_assembly/` folder is meant to contain only one file: `spa-assemble-config.json`. In this file, one can specify frontend modules to exclude from the inherited OpenMRS Reference Application, new ones to add or versions override. This configuration is optional and no file can be provided if you do not want or need to override the frontend modules.

Eg:
```json
{
    "frontendModules": {
        "@kenyaemr/esm-care-panel-app": "5.1.1-pre.157"
    },
    "__comment": "",
    "frontendModuleExcludes": [
        "@openmrs/esm-service-queues-app"
    ]
}
```

_Technical note:_ The `spa-assemble-config.json` is merged with the default [assembly file](https://github.com/openmrs/openmrs-distro-referenceapplication/blob/main/frontend/spa-assemble-config.json) from OpenMRS Reference Application at build time by a custom build script provided in Ozone, [build-openmrs-frontend.groovy](https://github.com/ozone-his/ozone/blob/main/scripts/openmrs/frontend_assembly/build-openmrs-frontend.groovy)


### The `frontend_config/` folder

The `frontend_config/` folder is where to specify OpenMRS 3 frontend configurations. This could be one or multiple JSON files.
By default, a child Ozone distribution will come with 2 config files, directly inherited from the Ozone parent distribution.
```
frontend_config/
├── ozone-frontend-config-sso.json
└── ozone-frontend-config.json
```

Those can be overridden by simply creating a file of the same name in your own distribution.
Or you can create a new file to bring distro-specific configurations on top of the existing ones. Doing so will require to set a particular environment variable so to inform OpenMRS frontend of this newly introduced configuration file. For example, `config.json`.

The variable to override is named `SPA_CONFIG_URLS`. It consists of a comma separated list of file paths.
Create a new `.env` file in the `scripts/` folder of your distribution with any name. For example, `override.env`.
Then set the value of the variable there. For example:

```properties
SPA_CONFIG_URLS=/openmrs/spa/configs/ozone-frontend-config.json,/openmrs/spa/configs/config.json
```
The above example tells OpenMRS to load the default Ozone file `ozone-frontend-config.json`, as well your custom `config.json` file.
They will be loaded in the order provided.

### The `initializer_config/` folder

The third folder in the OpenMRS configuration is `initializer_config/`. This folder is designated to contain all configuration files for the OpenMRS backend. The OpenMRS Initializer module reads from this directory and applies the specified configurations to the system. These configurations encompass various elements such as Visit Types, Concepts, Roles, Privileges, and Clinical forms, among others.

For comprehensive information on the Initializer's functionality and usage, refer to the detailed documentation available in the OpenMRS Initializer GitHub repository.

👉 [GitHub: OpenMRS Initializer](https://github.com/mekomsolutions/openmrs-module-initializer/)


## Overriding the SENAITE Config

### The `initializer_config/` folder

In its standalone form, SENAITE does not include pre-configured settings or a built-in mechanism for loading configurations. Implementers typically need to manually configure the system through the user interface and retain this database for the initial production deployment, or alternatively, develop a custom add-on containing the desired configurations.

However, SENAITE's underlying framework, Plone, offers functionality for loading configuration archives. To leverage this capability within the Ozone ecosystem, we've developed a Plone Initializer plugin. This plugin enables SENAITE to utilize configuration loading features when integrated with Ozone.

The configuration process still requires initial setup via the user interface, followed by exporting these settings. Once exported, the configurations will be automatically applied during subsequent deployments, streamlining the setup process for multiple instances.

:construction: TODO: Guide for exporting the configuration archive.

## Overriding the Odoo Config

Like SENAITE, Odoo lacks a implementer-friendly mechanism for loading configurations by default. Implementers typically resort to creating custom add-ons (requiring coding skills) or manual UI configuration, which poses challenges for environment reproducibility.

However, Odoo's underlying framework does support file imports. To capitalize on this feature, Ozone utilizes the Odoo Initializer add-on. This tool enables the loading of CSV or XML files from a designated configuration folder to Odoo.

For in-depth information on the Odoo Initializer's functionality and usage, consult the documentation available in its GitHub repository.

👉 [GitHub: Odoo Initializer](https://github.com/mekomsolutions/odoo-initializer/)


## Overriding the Superset Config

:construction:

## Overriding the ERPNext Config

:construction:

