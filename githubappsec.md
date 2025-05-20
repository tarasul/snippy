https://github.com/github-samples/securing-your-code 
Securing your code with GitHub
1 Hr 11 Min Remaining 
Lab 1 - GitHub Advanced Security Feature Introduction
Welcome! In this lab, you will be introduced to GitHub Advanced Security (GHAS) and its features. You will use the Juice Shop sample repository to enable the GHAS features, manage alerts, and learn how GitHub Advanced Security can keep vulnerabilities out of your code in the first place.

Please note that the screenshots provided are intended to serve as general examples. While we strive to keep all information current, some screenshots may not reflect the most recent updates, as security data is continually evolving and expanding. As a result, the content and figures you observe may differ slightly from what is shown here.

Creating the repository
In this exercise, you will create a repository with code from which you can work to test the GHAS capabilities.

For your convenience we have already filled in your username and password in the instructions, but you can always see them on the resources tab.

Navigate to https://github.com/enterprises/skillable-events/sso
Click on the green Continue button when the Single sign-on to Skillable Events page appears
Enter the your username User1-51492889@LODSPRODMCA.onmicrosoft.com
Enter your password BBd10#h!eD
Navigate to https://github.com/github-samples/securing-your-code in your browser.
Click the green Use this template button in the upper right corner of the page.
Create a new repository in our organization by choosing Microsoft-Build-2025 in the owner dropdown.
Give the repository a unique name lab301-51492889.
Make sure you switch it to visibility Private so your work does not conflict with other attendees! Now click Create repository.
The page will refresh after a few seconds where you can now see the code we will be working with.
Enabling the security settings
In the following exercises, you will be guided through the process of enabling the remaining GHAS features. Then you will be shown how to use the features to secure your code.

Exercise 1: Enable Dependabot
Although Dependabot isn't part of the GitHub Advanced Security product suite, it is still an important tool to discuss from an overall security posture.

Dependabot and Dependency Graph should already be turned on for your repository. If not, follow the steps below.

We first want to turn on the security settings for the repository. Navigate to the Settings tab (the icon of the gear) in the repo.
Click on the Advanced Security section.
Ensure the Dependency Graph is enabled. This will be indicated by a red Disable button. If there is a black Enable button, click it to enable the Dependency Graph setting. To enable Dependabot, we first have to enable the Dependency Graph. This allows Dependabot to ingest your package manifest files.
Ensure the Dependabot alerts are enabled. This will be indicated by a red Disable button. If there is a black Enable button, click it to enable the Dependabot alerts setting. This feature will create alerts for vulnerable dependencies found in your repository.
Click the Enable button next to the Dependabot security updates setting.
This will automatically create pull requests to update your vulnerable dependencies (if there is a non-vulnerable version to upgrade to).
Note: there is a maximum number of pull requests that this feature will create (10).
Details
Once you are done turning on Dependabot features, the next thing we will need to do is turn on GitHub Advanced Security features.

Exercise 2: Enable Code Security
Scroll down to the GitHub Advanced Security section and make sure it's enabled. If there is a black Enable button, click it to enable the GitHub Advanced Security setting.
Click on the Enable GitHub Advanced Security for this repository on the confirmation Enable GitHub Advanced Security for this repository? dialog window.
Next, let's enable Code Scanning with CodeQL. These settings are also under the Advanced Security settings page.
Underneath the Code Scanning | Tools heading, click the Set up button in the CodeQL analysis row.
If you do not see the Code scanning heading on the Code Scanning page after enabling Code Security - you have likely not created your repo in the proper Organization. Go back to the beginning of this lab and ensure you choose Microsoft-Build-2025 value from the dropdown as the new repository Owner when you choose Use this template.

There are two options: Default and Advanced. Select the Default option and review the settings.
For this lab, we will use the Default setup, which creates a managed Actions workflow (i.e. you will not see a codeql.yaml file committed to the repo). The Advanced option can be used to manage your code scanning workflow as a GitHub Actions workflow YAML file committed to the repo. The Default option is a great option to get started quickly to enable code scanning in a repository without needing to commit any additional code.
By default, it will scan the JavaScript code, use the default CodeQL queries (for highest precision), and scan the default branch on push, pull request, and on a weekly schedule.
Details
Click the Enable CodeQL button to save the settings and enable Code Scanning.
Details
Ensure that Copilot Autofix slider is enabled as On | (in the Code Security --> Tools section).
Details
You do not need a Copilot license in order to use the Copilot features with GitHub Advanced Security. However, Copilot can certainly be helpful in resolving issues in your IDE by using Copilot chat to explain the vulnerability and how to fix it.

Optionally, configure the Check runs failure threshold (in Protection Rules section) - by default, a pull request will be blocked if there are any high or higher security alerts once you configure a branch ruleset.
Exercise 3: Enable Secret Protection
Ensure that Secret Protection is enabled. This will be indicated by a red Disable button. If there is a black Enable button, click it to enable Secret Scanning.
Click the Enable button next to the Validity checks setting. This feature checks if the secret is still valid for specific partners, such as Azure, AWS, and, of course, GitHub. As an example, you can use this feature to check if a GitHub personal access token found in the repo is still valid and needs to be revoked.
Click the Enable button next to the Non-provider patterns setting. This scans for patterns that do not correspond to partners but still have a common syntax, such as a MySQL or MongoDB connection string.
Click the Enable button next to the Scan for generic passwords. This feature uses AI to find secrets/passwords that may be in your code that do not correspond to a known provider pattern.
Click the Enable button next to the Push protection setting. This feature will block pushes that contain high-precision secrets. You can use this chart to determine which types of secrets would be blocked with secret scanning push protection enabled.
Optionally, configure Who can bypass push protection for secret scanning.
By default, as to not interrupt developers' workflows, anyone with write access to the repository can manually bypass a blocked push that contains secrets (administrators will be notified of this, and it is also captured in the audit logs).
In Private and internal repositories in organizations using GitHub Enterprise Cloud with GitHub Advanced Security enabled, you can change this to only allow select roles/teams (or no one) to bypass secret scanning push protection.
Note that you can define your own Custom patterns from this page to scan for secrets that do not correspond to a known provider pattern.
Details
Summary
Congrats! You have successfully enabled all of the security settings on your repository. In the next lab, we will review the alerts that have been created and how to manage them.

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.
Securing your code with GitHub
1 Hr 19 Min Remaining 
Lab 2 - Reviewing and Managing Security Alerts
Now that we have all of the security feature enabled, let's review the security alerts that have been created and how to manage them.

Exercise 1: Reviewing Dependabot alerts
Click the Security tab in the repo. This is where you can see and manage all of the security alerts for the repo.
Click Dependabot under the Vulnerability alerts heading.
Details
You should see a number of Dependabot alerts with various severities. Click on one of the alerts to see more information about it.
Details
Click on an alert. When reviewing a Dependabot alert, you can see the following information (see if you can locate this information in the alert you opened):
The severity of the alert
The package name and version that is vulnerable
The severity of the vulnerability
The CVE number
The affected versions of the package
The fixed version (if there is one - sometimes there isn't a non-vulnerable version to upgrade to)
The dependency path (what file contains the vulnerable dependency)
Is it a runtime or development dependency?
Is it a direct or transitive dependency
The alert description
The alert recommendation (to upgrade or use a different package)
The alert timeline (for example, it should show that Dependabot just recently opened the alert. Once you close an alert, it will show who and when closed it here too).
Details
You can manually close an alert by clicking on the Dismiss alert button in the upper right hand corner. It's not recommended to close alerts manually, but there may be times where this is helpful (for example, the code that contains the alert is not used).
If you resolve an alert by upgrading to a non-vulnerable version, Dependabot will automatically close the alert!
Go back a page and return to the list of Dependabot alerts.
Click on the Command Injection in marsdb alert. You will note that this alert does not have a non-vulnerable version to upgrade to.
If this were a real application, you would need to evaluate your risk to keeping this package in place, migrate to a different package, or write the functionality in-house.
Go back a page and return to the list of Dependabot alerts.
Click on the Verification Bypass in jsonwebtoken alert. This alert does have a non-vulnerable version to upgrade to.
If Dependabot has finished running, you should see a Review security update button attempt. If you see it, click it.
If you don't see it, skip to the next step. You can return to this step later after Dependabot finishes its initial run.
Review the PR that Dependabot created by clicking on the Review security update.
In the Files changed tab, you should see it upgrading the package.json file.
Back on the Conversation tab, you can see that the compatibility score is pretty low - this is an indication that you would likely going to have to make code changes to accept this PR.
Dependabot security updates is a great feature because assuming your build and tests pass, you can merge the PR and automatically close the alert.
Navigate back to the Dependabot alerts page and let's take a look at the list of Dependabot alerts one final time.
We can filter by Package, Ecosystem, Manifest, and Severity. For example, sometimes upgrading just one package can resolve multiple security alerts, so this can be a great way to prioritize fixes.
Note that if you click the package dropdown, one package has 6 alerts associated with it (all linked to the same PR).
This is a great place to start if you are looking to start prioritizing which alerts to fix first.
Reviewing the Critical and High security alerts is another great place to start when prioritizing.
Details
If you put your cursor in the search box (should have is:open by default), there are additional filter options. Some of the common filter options are scope (runtime or development) and has (for example, has:patch).
Update the search query to is:open has:patch. This will filter out all of the alerts that don't have a patch available and only show alerts where there is a non-vulnerable version to upgrade to.
Details
Auto-triage your alerts allows you control over how Dependabot opens pull requests, ignores false positives and snoozes alerts. Navigate to the Settings tab (the icon of the gear) in the repo and then Advanced Security left sidecar, scroll to Dependabot, then find Dependabot rules underneath Dependabot alerts.

Add a rule to snooze any alerts that do not have a fix available. Choose the "gear" icon and select the New rule button. Name the rule Snooze when no patch available, add a target metadata for all npm packages: ecosystem:npm and ensure the Dismiss Alerts - Until patch is available is selected. Next, select Create rule.

Details
Navigating back to the Security tab / Dependabot under the Vulnerability alerts heading. You will see 1 Closed heading. Select this to find your alert Command Injection in marsdb without any fix has now been Dismissed as auto-dismissed. The audit log will note Repository rule created and Snooze when no patch available was applied
Details
Exercise 2: Reviewing Code Scanning alerts
Under the Security tab in the repo, click on Code scanning to review the code scanning alerts.
We should have a number of alerts. If you don't see any alerts yet, skip ahead to the next exercise and come back to this one. More than likely, the code scanning workflow hasn't finished yet (it takes between 2-5 minutes to run).
Details
If there are code scanning alerts, spend a few moments reviewing them. We can filter/sort by severity, tool, language, and a few other options, just like with Dependabot alerts.
A common search/filter to use is Autofilter to filter out the alerts with a test tag (code scanning violations found in test files). This can help you focus on the alerts that are more likely to be real vulnerabilities.
To use the autofilter filter, paste this into the search box: is:open branch:main autofilter:true (or type/select it by hand).
Details
Scroll down and let's click on one of the SQL injection alerts. These can be found by searching for the title "Database query built from user-controlled sources".
After clicking into one of the alerts, we should notice a few things:
The severity of the alert
The CWE(s) of the alert with a hyperlink
The tool that found the alert (CodeQL)
The alert title
The alert description
More information on the alert, such as a recommendation and example of the vulnerability (and often additional resources to reference)
The alert file path, line number, and code snippet
The affected branch
Details
Click on the Show paths link to review the vulnerability from the source to the sink.
CodeQL works by scanning the sources and sinks; as an example, the source is the user input and the sink is the database query
This can help you understand the vulnerability and track it throughout the application to better understand how to fix it.
Click on the Show more expandable section near the bottom of the page.
This will show more information about the type of code vulnerability you're working with, how to avoid it, and how to fix it.
Right below the alert title on the right hand side, there is a Generate fix button. Click it!
This uses the AI power of Copilot to generate a fix for the vulnerability.
This is a great way to learn how to fix the vulnerability and to see how to fix it in the context of your code.
It will take a little time (30-60s) to generate a suggestion. Wait for it to finish.
If you're happy with the suggestion, click the Commit to a new branch button.
Accept the defaults and click Commit change.
This will create a draft pull request with the fix for the vulnerability. In a real world example, assuming your build and tests pass, you would move the PR out of a draft state, having someone on your team review it, and then merge the change.
The nice thing with code scanning alerts (just like Dependabot alerts) is that once you merge the code and is scanned again that resolves an alert, the alert will be automatically closed. This is because the alert is no longer present in the code.
Details
We will merge this change. But first, we have to wait for the CodeQL workflow to finish running to ensure we aren't introducing any new vulnerabilities into the codebase. The workflow run will take 2-5 minutes (watch the running workflow in the Checks section to check when it's completed (circle changes from yellow to green and says "All Checks have passed")).
Once the workflow finishes, click Ready for review. This moves the pull request out of the draft state.
Afterwards, click Merge pull request and then Confirm merge.
After merging the code into the default branch, a code scan will run and once it finishes, the alert will be closed.
You can test this by merging the pull request we just created! It will take a few minutes for the code scanning to run and close the alert.
Now that our new code has been merged, let's go watch the workflow scan run. Click on the Actions tab at the top of the page.
Select CodeQL on the left side of the page. This shows you all the default runs for the CodeQL workflow. You should see a workflow running right now for our push to main
Click the running workflow to see the details of the run. You can review the logs to see what CodeQL is doing behind the scenes here if you want!
Once the workflow completes successfully, return to the Security tab and check back on the list of code scanning alerts. You should see one (1) Closed alert listed - yay! 🎉
You don't need a Copilot license in order to use the Copilot features with GitHub Advanced Security. However, Copilot can certainly be helpful in resolving issues in your IDE and Copilot chat can explain the vulnerability and how to fix it.

Exercise 3: Reviewing Secret Scanning alerts
Under the Security tab in the repo, click on the Secret scanning --> Default option. This will show all of the default secret scanning alerts.
You should see a number of alerts. For example, there should be a GitHub Personal Access token alert. Click it.
This page shows where in the code the secret was discovered (if there were multiple locations, it would list them all).
If a secret is found in the code, we would want to revoke manually in the designated service.
It's recommended to revoke the secret instead of rewriting history because the secret was exposed and you don't know who may have seen it.
If you re-write history, the secret will still be valid and could be used by an attacker. Also, re-writing history modifies commit hashes and can make traceability more difficult.
Details
You can click on Verify secret. It will say it's not currently valid on github.com, but that doesn't mean it doesn't come from another GitHub instance (such as GitHub Enterprise Server). (clicking may not be there, since we enabled automatic visibility checked it may have been checked already, or it may even say the secret has been Publicly leaked).
Go back to list of secret scanning alerts. Click on the Google API key alert.
Click on Verify secret again. This time, it should say secret inactive (or Publicly leaked inactive secret). This is a good candidate to Close as --> Revoked (click the Close as button in the upper right to do so), select Revoked and click on Close Alert.
Unlike Dependabot alerts and Code Scanning alerts, secret scanning alerts are not automatically closed when the secret is removed from the code - whether by a new commit or by re-writing history. This is because the secret was exposed and you don't know who may have seen it. So, you have to manually close the alert once you revoke the token.
Navigate back to the Default secret alerts list.
We can click 1 Closed to see the alert we just closed.
Details
Click on the Generic secret scanning alerts option. This will show all of the alerts that are not high confidence, such as generic passwords, keys, and things such as HTTP bearer authentication header tokens found in the code.
Let's click into one of the found Generic alerts.
Details
Just like high confidence secret scanning alerts, it shows where the secret was found in the code. The secret was found with AI, so it may or may not be a real secret or password. If it's not, we can close it manually and mark it as a false positive.
Summary
Well done! You've learned how to review and triage alerts from Dependabot, code scanning, and secret scanning. And you even saw how you can use Copilot to automatically fix a code scanning alert. In the next lab, we are going to to get hands-on with code scanning, repository rulesets, and Copilot, to see how we can both prevent and fix vulnerabilities in our code base.

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.
Securing your code with GitHub
1 Hr 14 Min Remaining 
Lab 3 - Hands-on with Code Scanning
With Code Scanning enabled, we want to block vulnerable code from entering the codebase. We can define a repository ruleset to enforce this.

Exercise 1: Introduce a code scanning security vulnerability
Navigate to the routes/login.ts file in your repository (make sure the Code tab is selected).
Click the Pencil icon on the top right of the file view to edit the file.
Find lines 36-46 and delete them
diff
- models.sequelize.query(
-       'SELECT * FROM Users WHERE email = :email AND password = :password AND deletedAt IS NULL',
-       {
-         replacements: {
-           email: req.body.email || '',
-           password: security.hash(req.body.password || '')
-         },
-         model: UserModel,
-         plain: true
-       }
-     )
At line 36, add the following code:
javascript
models.sequelize.query(`SELECT * FROM Users WHERE email = '${req.body.email || ''}' AND password = '${security.hash(req.body.password || '')}' AND deletedAt IS NULL`, { model: UserModel, plain: true })
Let's push our new branch with the vulnerability up to GitHub.
Click the green Commit Changes button on the top right of the file view.
Keep the commit message the same, but feel free to add a description.
Select the Create a new branch for this commit and start a pull request radio button.
Keep the branch name at the default, which should be your GitHub handle followed by -patch-1.
Click the Propose changes button.
Click the Create pull request button at the bottom of the text field in the next view.
The page will redirect to the pull request that was just created. Do not merge the pull request yet, as we want to see the code scanning results first.
After the pull request is created, the code scanning job will have been initiated. You can see the status of the job in the pull request checks. It will take a few minutes to run.
Details
CodeQL should find the vulnerability, so the check will fail. Also, we should see Copilot create us an autofix on the PR as a code suggestion change that we can review (and commit)
It might take Copilot a few moments to create the autofix.
Review the autofix - we can prevent a vulnerability from entering the repository now with a click of a button! 🎉 ⚠️⚠️ But don't commit the suggestion yet. ⚠️⚠️
Details
Exercise 2: Creating a code scanning ruleset
Without a ruleset (GitHub's new version of branch protections), even though CodeQL found the vulnerability, a developer could still merge the code mistakenly, or merge the code before the CodeQL checks finish. Let's prevent this!

We want to wait for the PR check to finish entirely (with a pass or fail) before creating the ruleset!

Let's go into the Settings tab of the repository (we will be adding a branch ruleset).
On the left hand list of options, click on Rules --> Rulesets.
Details
Click on New ruleset ▾ --> New branch ruleset
Create the ruleset:
Give the ruleset a name (any name is fine)
Change the enforcement status to Active.
Under target branches, click Add target and select Include default branch.
Scroll down and check the Require code scanning results box
The CodeQL tool should already be there - there's nothing to change (you can change the security alerts and alerts. But please don't).
Scroll down and click the Create button.
Details
With the ruleset created, both the JavaScript scan has to finish and no vulnerabilities found with CodeQL in order to merge the code.
Navigate back to our open PR. The Merge pull request button should now be grayed out (there also a big red icon and the text Merging is blocked with the list of blocking reasons underneath), preventing us from merging vulnerable code.
Details
Review the Copilot Autofix suggestion.
Click on the commit suggestion button and commit changes.
After another CodeQL scan, the PR should pass and the Merge pull request button should be enabled, allowing you to merge the change without the vulnerability.
➡️ For the purposes of this lab, you don't have to actually merge the PR, so you don't have to wait for the CodeQL scan to finish before moving on.
Celebrate 🎉! We just prevented a security vulnerability from entering our codebase!
Summary
Excellent! In this lab we saw how GitHub code scanning can find bugs in the pull request and suggest solutions for them. And we learned how to use repository rulesets to block a pull request from merging until the checks are resolved.

In the next lab, we are going to learn about Dependency Review, and how it can help us stop bad dependencies from making it to the default branch.

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.



Securing your code with GitHub
1 Hr 11 Min Remaining 
Lab 4 - Hands-on with Dependency Review
With Dependency Review enabled and configured, we want to block vulnerable packages from entering the codebase. We can define a repository ruleset to enforce this as well!

Exercise 1: Add the Dependency Review Action
First, let's add the dependency review action workflow.

In the repository, click on the Actions tab.
Click on the New workflow button in the upper left.
Search for "dependency" and click enter on your keyboard.
Under the Dependency Review workflow, click Configure.
Details
Review the action actions/dependency-review-action and its inputs after with:…. The options are going to be commented out so default values will be used. For example this action can also block specific open source license types or you can configure the severity of the vulnerabilities to be flagged.
In the upper right, click on Commit changes…
Since we have a ruleset, we have to create a branch and merge this to main via pull request. Create a branch and commit (Propose changes) the changes.
Details
On the next screen, enter an optional pull request description (and feel free to change the title).
Details
Click Create pull request.
Wait for the code scanning job to finish. It will take a few minutes to run.
You will notice that the Dependency Review workflow ran against this PR and didn't report any issues.
Details
Merge the PR once the code scanning completes.
Navigate to Settings of the repo.
Navigate to Rules --> Rulesets.
Click on the name of the ruleset you created in lab 3 to modify it.
Enable the check box for Require status checks to pass (scroll down or use search)
Click on Add checks.
Search for dependency-review and add it (it should show up under suggestions).
Details
Save the changes to the ruleset.
Exercise 2: Introduce a dependency vulnerability
Now, let's attempt to add a vulnerable dependency to the codebase and test out the dependency review feature.

Navigate back to the Code tab in the repo.
Click the package.json file in the root of the repository to open it.
Click the pencil ✏️ icon at the top right of the file to go into edit mode.
Go to the end of line 181 and hit Enter to create a blank line for line 182.
Add the following code to line 182, making sure to include the comma at the end of the line:
"tar": "2.2.2",
Details
Click the Commit changes button.
Change the branch name to lab4/dependency-vulnerability and click Propose changes to start a pull request.
Enter an optional pull request description (and feel free to change the title).
Click the Create pull request button.
Wait for the dependency review job to finish.
It should make a comment to the pull request with a note that it found a vulnerable package dependency. In fact, adding this one package would introduce 3 new vulnerabilities to our codebase.
Details
Also, the required status check will be marked as failed, preventing the pull request from being merged since we have made dependency review a required check.
Summary
Celebrate 🎉! We just prevented a security vulnerability from entering our codebase! Without dependency review, if a pull request from this branch was opened to main, nothing would prevent the vulnerability from being introduced.

In the next lab, we are going to go hands-on with secret protection, and see how we can use push protection to stop secrets from being pushed to GitHub.

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.


Securing your code with GitHub
1 Hr 9 Min Remaining 
Lab 4 - Hands-on with Dependency Review
With Dependency Review enabled and configured, we want to block vulnerable packages from entering the codebase. We can define a repository ruleset to enforce this as well!

Exercise 1: Add the Dependency Review Action
First, let's add the dependency review action workflow.

In the repository, click on the Actions tab.
Click on the New workflow button in the upper left.
Search for "dependency" and click enter on your keyboard.
Under the Dependency Review workflow, click Configure.
Details
Review the action actions/dependency-review-action and its inputs after with:…. The options are going to be commented out so default values will be used. For example this action can also block specific open source license types or you can configure the severity of the vulnerabilities to be flagged.
In the upper right, click on Commit changes…
Since we have a ruleset, we have to create a branch and merge this to main via pull request. Create a branch and commit (Propose changes) the changes.
Details
On the next screen, enter an optional pull request description (and feel free to change the title).
Details
Click Create pull request.
Wait for the code scanning job to finish. It will take a few minutes to run.
You will notice that the Dependency Review workflow ran against this PR and didn't report any issues.
Details
Merge the PR once the code scanning completes.
Navigate to Settings of the repo.
Navigate to Rules --> Rulesets.
Click on the name of the ruleset you created in lab 3 to modify it.
Enable the check box for Require status checks to pass (scroll down or use search)
Click on Add checks.
Search for dependency-review and add it (it should show up under suggestions).
Details
Save the changes to the ruleset.
Exercise 2: Introduce a dependency vulnerability
Now, let's attempt to add a vulnerable dependency to the codebase and test out the dependency review feature.

Navigate back to the Code tab in the repo.
Click the package.json file in the root of the repository to open it.
Click the pencil ✏️ icon at the top right of the file to go into edit mode.
Go to the end of line 181 and hit Enter to create a blank line for line 182.
Add the following code to line 182, making sure to include the comma at the end of the line:
"tar": "2.2.2",
Details
Click the Commit changes button.
Change the branch name to lab4/dependency-vulnerability and click Propose changes to start a pull request.
Enter an optional pull request description (and feel free to change the title).
Click the Create pull request button.
Wait for the dependency review job to finish.
It should make a comment to the pull request with a note that it found a vulnerable package dependency. In fact, adding this one package would introduce 3 new vulnerabilities to our codebase.
Details
Also, the required status check will be marked as failed, preventing the pull request from being merged since we have made dependency review a required check.
Summary
Celebrate 🎉! We just prevented a security vulnerability from entering our codebase! Without dependency review, if a pull request from this branch was opened to main, nothing would prevent the vulnerability from being introduced.

In the next lab, we are going to go hands-on with secret protection, and see how we can use push protection to stop secrets from being pushed to GitHub.

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.

Securing your code with GitHub
1 Hr 9 Min Remaining 
Lab 5 - Hands-on with Secret Scanning
Let's use Secret Scanning with push protections to prevent secrets from entering the codebase!

Exercise 1: Attempt to commit a secret
Let's try to commit a secret to the repository to test out the secret scanning push protection feature.

But first, we need a secret to commit! The easiest is to generate a GitHub personal access token (with limited scopes) and attempt to commit it.

In a new browser tab, navigate to github.com and click on your user profile picture in the upper right and click on Settings.

In the lower left of the list of options, click on Developer settings.

Click on Personal Access Tokens to expand and click on Fine-Grained Tokens.

Generate a new token.

Don't give the token any permissions - just give it a name and scroll down to the bottom and Generate token.

Copy the value of the token to the clipboard.

Note: If you leave this page, you will not be able to copy the token again. If you lose the token from the clipboard, either regenerate the token or create a new one.
Now, let's attempt to commit the token to the repository.

Any file would work, but for example, we can open up the routes/login.ts file we edited earlier.

As an example, on line 18 you can add const secret = "<YOUR TOKEN>";, replacing with the token you just generated - it should start with github_pat_.

Commit the file and then push the file to the repo, by clicking on the Commit changes button and then Propose Changes.

Push protection should detect the GitHub personal access token and block the push - great!

Click on Cancel since GitHub Secret Protection did it's job and prevented us from pushing the secret to the repository.

Details
Depending on how the settings are configured, we could bypass the push protection and push the secret to the repository. But, we don't want to do that! 🙅‍♂️ Repository admins and organization owners would receive an email notification if we did.
Summary
Celebrate 🎉! We just prevented a secret from entering our codebase!

And there you have it. You should now have a good grasp on what GitHub Advanced Security is, how it works, and how to implement it. So get out there and keep your company secured!

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.

Securing your code with GitHub
1 Hr 9 Min Remaining 
Lab 6 - Hands-on with Security Overview
We've covered how to review alerts in a single repository, but how is your org or team doing? Next, we'll check out the Security Overview at the organizational level to see how we can get a high-level view of the security posture of our organization.

This lab covers parts of the following exam domains:

Domain 6: Describe GitHub Advanced Security best practices
Exercise 1: Navigating to Security Overview
The Security Overview can be used by anyone inside of an organization; it shows repositories that you have access to. If you are an org owner or a security manager, you would see all alerts. If you are a regular org member, you would only see alerts for repositories by default that you have write access to.

Security alerts for a repository are visible to people with write, maintain, or admin access to the repository and, when the repository is owned by an organization, organization owners. You can give additional teams and people access to the alerts.

Navigate to the Microsoft-Build-2025 organization. You can do so by clicking on the Microsoft-Build-2025 in the repository breadcrumbs in the upper left hand corner.
You can also navigate to your orgs by clicking on your profile picture and "Your organizations"
Click on the Security tab.
Review (and click on!) the different views on the left-hand side:
Overview: visualize trends in Detection, Remediation, and Prevention of security alerts (docs)
Risk: explore the risk from security alerts of all types or focus on a single alert type and identify your risk from specific vulnerable dependencies, code weaknesses, or leaked secrets (docs)
Coverage: assess the adoption of code security features across repositories in the organization (docs)
Enablement trends: see how quickly different teams are adopting security features
CodeQL pull request alerts: assess the impact of running CodeQL on pull requests and how development teams are resolving code scanning alerts (docs)
Secret scanning: find out which types of secret are blocked by push protection and which teams are bypassing push protection (docs)
You can export a CSV of nearly from most of these views using the Export CSV button in the upper right.

Under the Overview view, navigate the sub-views, specifically Detection and Remediation.
Note the trends - this is useful information to evaluate the security posture of your organization. Are we getting better over time?
Being secure requires "constant vigilance"
Navigate to the Risk view.
On the right-hand side, click the Teams ▾ button/dropdown.
Click on the members team - this is going to be empty since the team has no repositories, so note how the total alerts changes.
This can be really useful for a manager, architect, or developer to see which repositories assigned to the teams have security features enabled and how many alerts they are generating. In our case it's not useful at all.
At the bottom of the options on the left, you will see Campaigns.
Security campaigns are a new feature designed to help administrators and security managers create targeted campaigns and track remediation progress effectively.
Click on the existing campaign here (SQL injection (CWE-89)) to check it out!
How are we doing on our goal?
If your If your company is using GitHub Enterprise or GitHub Teams plan and is not yet using GitHub Secrets Protection, you can run a secret risk assessment at no cost to understand exposure to data leaks and to get an overview of your organization's secret leak footprint.

Summary
That's the security overview! Use these views to monitor and manage your security posture effectively. By leveraging the detailed insights provided in each section, you can identify potential threats, take proactive measures, and ensure your systems remain secure.

If you want to learn more about the security overview or about what a particular view shows, check out the docs!

Congrats, you have finished all of the main labs! 🎉 If you have time or are up for a challenge, try out the extra credit labs!

Congratulations!
You have successfully completed this exercise. Click Next to advance to the next Lab.
