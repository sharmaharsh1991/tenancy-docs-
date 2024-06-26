---
title: Tenant-Aware Commands
description: Tenant-Aware Commands
extends: _layouts.documentation
section: content
---

# Tenant-Aware Commands {#tenant-aware-commands}

Even though [`tenants:run`]({{ $page->link('console-commands#run') }}) lets you run arbitrary artisan commands for tenants, you may want to have strictly tenant commands.

To make a command tenant-aware, utilize the `TenantAwareCommand` trait:
```php
class MyCommand extends Command
{
    use TenantAwareCommand;
}
```

However, this trait requires you to implement a `getTenants()` method that returns an array of `Tenant` instances.

If you don't want to implement the options/arguments yourself, you may use one of these two traits:
- `HasATenantsOption` - accepts multiple tenant ids, optional -- by default the command is executed for all tenants
- `HasATenantArgument` - accepts a single tenant id, required argument

These traits implement the `getTenants()` method needed by `TenantAwareCommand`.

> Note: If you're using a custom constructor for your command, you need to add `$this->specifyParameters()` at the end for the option/argument traits to take effect.

So if you use these traits in combination with `TenantAwareCommand`, you won't have to change a thing in your command:

```php
class FooCommand extends Command
{
    use TenantAwareCommand, HasATenantsOption;

    public function handle()
    {
        //
    }
}

class BarCommand extends Command
{
    use TenantAwareCommand, HasATenantArgument;

    public function handle()
    {
        //
    }
}
```

### Custom implementation

If you want more control, you may implement this functionality yourself by simply accepting a `tenant_id` argument and then inside `handle()` doing something like this:
```php
tenancy()->find($this->argument('tenant_id'))->run(function () {
    // your actual command code
});
```
