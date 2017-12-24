title: Customize Redmine Plugin Dmsf
date: 2015-12-26 17:35:49
categories: Techniques
---

## Add a Hook in the Redmine Source

This official redmine source doesn't provide a hook if I what to do something just after a project created. So I'm gonna write a hook on my own.

In the file /path/to/redmine/app/controllers/projects_controller.rb, we just add a one-line code like this:

``` ruby
def create
    @issue_custom_fields = IssueCustomField.sorted.to_a
    @trackers = Tracker.sorted.to_a
    @project = Project.new
    @project.safe_attributes = params[:project]

    if @project.save
      unless User.current.admin?
        @project.add_default_member(User.current)
      end
      call_hook(:controller_project_new_after_save, {:params => params, :project => @project})
```

The call_hook line is what the functionality takes place. Notice that we should add the line inside the create function not new function.

Next, we can actually do something nice by adding a file /path/to/redmine/plugins/redmine_dmsf/lib/redmine_dmsf/hooks/controller_project_new_after_save.rb

``` ruby
module RedmineDmsf
    module Hooks
        include Redmine::Hook

        class ControllerProjectNewAfterSaveHook < Redmine::Hook::ViewListener

            def controller_project_new_after_save(context={ })

                params = context[:params]
                project = context[:project]

                # then you can add code whatever you like.
```

Finally, we should add the file path to the redmine_dmsf.rb file under /path/to/redmine/plugins/redmine_dmsf/lib.

``` ruby
require 'redmine_dmsf/hooks/controller_project_new_after_save_hook'
```

## Update Database

If we want to update the database by just one command, you can add a ruby file called seeds.rb under the folder /path/to/redmine/db/, then execute the command in the redmine command line environment.

``` bash
rake db:seed RAILS_ENV=production
```
