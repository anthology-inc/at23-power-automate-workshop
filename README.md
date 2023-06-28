# Anthology Together 2023 Power Automate Workshop
This guide is designed to walk workshop participants through the various steps
of modeling a Power Automate workflow that communicates with Anthology Student
and Microsoft Teams. By the completion of the workshop, participants will have
completed a functional Power Automate workflow that can be initiated manually
or via the Anthology Student Workflow Engine. This workflow will accept parameters
which are used to query Anthology Student and initiate a approval workflow that
notifies a user via Microsoft Teams, prompts for their approval or disapproval, and then updates Anthology Student accordingly.

## Start byt create a simple flow
1. In your browser navigate to https://make.powerautomate.com
2. Login using your credentials

![Screenshot of the Power Automate Dashboard](screenshots/01.png)

3. Mouseover "New flow" and then choose "Instant Flow"
4. Name your flow AT23 Document Approval
5. Select the "When an HTTP request is received" trigger
6. Click Create

![Screenshot of the new instant flow dialog](screenshots/03.png)

7. Click "New Step"
8. Enter in "post message" in the search box
9. Select the Microsoft Teams action named "Post message in chat or channel"

![Screenshot of the choose an operation dialog](screenshots/04.png)

10. Select "Flow bot" form the Choose Post field
11. Select "Chat with Flow bot" in the Post in field
12. Enter in your email address in the Recipient field
13. Enter "Hello World" in the Message field.

![Screenshot of the post a message dialog](screenshots/05.png)

14. Open up the "When an HTTP request is received" step
15. Show advanced options and change the Method field to "GET"

![Screenshot of the when you receive an HTTP request dialog](screenshots/06.png)

16. Copy the URL
17. Save your flow
18. Test your flow by clicking on "Test," hoosing "Manually," and then clicking "Test"
19. Open a browser tab, past in the HTTP URL and hit enter.
20. Verify that you received a chat message in Teams.
21. Verify that your workflow completed successfully.

![Screenshot of the test run](screenshots/06.png)