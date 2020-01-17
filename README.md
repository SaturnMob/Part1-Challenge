
----
#### __Prompt one__
----

__Company:__ Acme computers

__Version control platform(s):__ Many GitHub Enterprise instances installed throughout the company by different teams. Acme Computers is trying to standardize on GitHub Enterprise and consolidate their GitHub usage onto a single instance. The company has many instances of other Git hosting solutions installed as well. Some are fully supported applications. Other instances are on machines under people's desks.

__Customer requests:__

* __Shrink large repository:__ Acme wants GitHub to help them shrink the large repository to a more manageable size that is performant for common Git operations. The large repo is a project that is high visibility with an aggressive roadmap. They request that we help them within the month. It's a large, monolithic repository.

	The primary goal should be to identify and remove large files that are not needed in day to day operations with the repository. For example

    * database dumps
    * data files that have since been removed or are not used
    * large config files
    * large image files

	Proposed steps

	* Clone the repository with all remote branches and tags
	* Identify large files for deletion
	* Identify large files for optimization (Eg. images)
	* Clean large files that have been identified from git history
		* Use tools like git-gc and git-reflog
	* Either push the repo to a new remote repository or force push the the changed history to the existing repo.

* __Consolidate instances:__ Acme wants you to tell them best way to move all the other teams, using GitHub Enterprise or other Git solutions, onto their consolidated GitHub Enterprise instance. They have asked you to give them five or six bullet points about how you would approach that initiative, both technically and culturally.

    * Some mapping of users will probably have to occur to ensure correct history and no surprises to the developers. Integration with identity providers can be used or the built in authentication mechanism could also be used.
    * From the other GitHub Enterprise instances:
        * A detailed inventory of the repositories that will be migrated should be done. This will be used to export the repos and make a migration archive.
        * The migration archive will have everything needed to execute the import to our target instance. Each migration archive will be imported individually with it’s own “GUID” that identifies the archive.
        * Conflicts with the migration archives could occur and have to be handled in an individual basis
    * From other Git solutions:
        * Clonning the existing repositories and creating them as new repositories in the target GitHub Enterprise would be the best option for this migration.
    * I would recommend creating a series of scripts, to not only automate the process but also ensure that all of the history of the migrated repositories is ensured. This will also make the process reproducible and testable. These could also be integrated into any orchestration solution, if any are deployed, to help automate the deployment of the script and their execution.
    * I also recommend that a staging environment be used to test these migrations before committing to a production push.
    * These migrations can be done in an iterative manner as to not disrupt developer workflow and ensure better error management if any should occur.
    * For any temporary Git instances, those that are not officially supported, I would recommend cloning a temporary repository and either coping the repo with scp to the target GitHub Enterprise or physically copying them to a machine that can.

* __Migrate an SVN repo:__ The customer has one SVN repository that hasn't migrated over to a Git solution. They would like help moving this one large repository over. The team has a trunk based development pattern with this repository and is unfamiliar with Git.

	There are a number of ways and tools to help migrate SVN repositories. The tools and processes used will depend on:

	#### GitHub Enterprise instance can communicate with SVN repository
    
    * I would use the tools and steps outlined in the Importing data from third-party version control systems found in the GitHub Enterprise documentation guides. These tools are already provided with the GitHub Enterprise instance and would facilitate the migration.

	#### GitHub Enterprise instance can not communicate with SVN repository
    
    * Create the user mapping file from SVN contributors.
    * Convert the SVN repository to a Git repo using git-svn and the user mapping file we’ve created. This conversion should also include the conversion of the svn ignore properties to a .gitignore.
    * All branches and tags should also be converted.
    * Once all the conversion is done a new remote repo should be created on the GitHub Enterprise instance and added to the new local git repository.
    * We finish the process by pushing all branches and tags to our new GitHub repo.

	#### Helping developers using SVN to transition to using Git

	The end goal would be to get the development team using Git and Git tools full time. Until that goal is achieved the team can use a SVN client with the GitHub Enterprise instance by using the https remote URL.

----

### __Prompt two__

----
__Company:__ Dunder Mifflin Technologies

__Version control platform(s):__ They currently use Gerrit, out-of-the-box Git, Subversion, and Team Foundation Server.

__Customer requests:__

* __Help us modernize our practices:__ Dunder Mifflin is worried they are falling behind their industry. They have lots of legacy software and development patterns that were created 20 years ago. They have found it incredibly difficult to change any aspect of their SDLC because of their infrastructure, processes, and long-tenured team members who are resistant to change.
	
	Change is hard for any organization. Sometimes the best approach is a gradual increase in the use of new practices. For example changing from pure waterfall to an agile methodology presents challenges to developer and manager alike.
	
	A couple of ideas as to how to modernize practices in an incremental fashion. These are not intended to be implemented all at once or across all teams. The most reluctant to change might need to start with "safe" changes to their processes:
	
	1. Start by requiring code reviews to be performed by all team members.
	2. Require code reviews before merging changes.
	3. Have longer sprints, something like three to four weeks, if the goal of making them shorter and more productive.
	4. Pick a starting off test coverage percent and require projects to build up to it. The goal should be to get to whatever value your organization deems a "good high".
	5. Require all new code to have a certain amount of test coverage. This should be a value that works for you. Eg. All new code has to have a test coverage of over 70%.
	6. Add services like CircleCI to introduce the concepts of continuous integration and deployments.

* __Help us release more often:__ Dunder Mifflin releases software four times a year. They are shipping largely web-based applications. They want to increase more frequently, but they are unsure of the best first steps. What areas would you explore with the customer to help them move this goal forward?
	* Looking at their branching model. Implementing something like Gitflow or Githubflow. Implementing them or processes like them could improve the certainty of what features are being included into a release.
	* Help them improving or implement automated testing and deployments. This will greatly help the confidence of releases.
	* Taking a look at their milestone planning and project management. Helping them client get to an iterative SDLC that works for them would greatly increase their ability to release more often. I believe that this and "sensible" milestone planning could help the client achieve their goals.

* __Commit/merge/deploy permissions:__ Dunder Mifflin has expressed concern about moving away from Gerrit. They have asked how they can control repository access, merging, and deployment permissions within GitHub, and what aspects of their desired security setup can be enforced programmatically.
	
	Let's break this up to arrive at a better answer:
	
	#### Controlling repository access
	
	Access to repositories can be achieve in a number of ways:
	
	1. Granular rules and permissions can ge granted to user or teams on a per repository basis. The basic examples are Admin, write and read permissions.
	2. Access can be granted to individual users as collaborator in the repository or to complete teams.
	3. Repositories can also be logically grouped into Organizations and set to private to further limit visibility of the repository to teams and user outside of the organization.
	
	#### Branch Merging and Deploy permissions
	
	Each repository lets you identify branches that should be protected and have rules that control when changes are merged and by whom. These rules include:
	
	1. Require a Pull Request review/approval before it is able to be merged into a protected branch.
	2. Require status checks to pass before merging into a protected branch.
		1. Here you will find the status of your build process, test suite, and/or test coverage and code analysis.
	3. The ability to have all protected branches rules to also apply to repository administrators.
	
	#### Programmatic Access to these security features
	
	The Github API allows you to build tools that interact with your GitHub Enterprise instances. Combining webhooks and the use of the API it is possible to create you own status and security checks that react to events in your repositories.
----
#### __Referred Resources__

1. [https://github.com/enterprise](https://github.com/enterprise)
2.  [https://help.github.com/enterprise/2.15/admin/guides/migrations/](https://help.github.com/enterprise/2.15/admin/guides/migrations/)
3. [https://git-scm.com/book/en/v2/Git-and-Other-Systems-Migrating-to-Git](https://git-scm.com/book/en/v2/Git-and-Other-Systems-Migrating-to-Git)
4. [https://blog.axosoft.com/migrating-git-svn/](https://blog.axosoft.com/migrating-git-svn/)
5. [https://developer.github.com/v3/](https://developer.github.com/v3/)
6. [https://guides.github.com/features/mastering-markdown/](https://guides.github.com/features/mastering-markdown/)
