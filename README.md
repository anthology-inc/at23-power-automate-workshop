# Anthology Together 2023 Power Automate Workshop

This guide is designed to walk workshop participants through the various steps
of modeling a Power Automate workflow that communicates with Anthology Student
and Microsoft Teams. By the completion of the workshop, participants will have
completed a functional Power Automate workflow that can be initiated manually
or via the Anthology Student Workflow Engine. This workflow will accept parameters
which are used to query Anthology Student and initiate a approval workflow that
notifies a user via Microsoft Teams, prompts for their approval or disapproval, and then updates Anthology Student accordingly.

## Start by create a simple flow and testing it

We'll start by creating a very simple flow with an HTTP trigger and a single action which will post a message to Microsoft Teams. We are going to keep this flow purposely simple. Don't worry because we'll be adding complexity incrementally throughout the workshop.

1. In your browser navigate to https://make.powerautomate.com
2. Login using your credentials

![Screenshot of the Power Automate Dashboard](screenshots/01.png)

3. Mouseover `New flow` and then choose `Instant Flow`
4. Name your flow AT23 Document Approval
5. Select the `When an HTTP request is received` trigger
6. Click `Create`

![Screenshot of the new instant flow dialog](screenshots/03.png)

7. Click `New Step`
8. Enter in `post message` in the search box
9. Select the Microsoft Teams action named `Post message in chat or channel`

![Screenshot of the choose an operation dialog](screenshots/04.png)

10. Select `Flow bot` form the `Choose Post` field
11. Select `Chat with Flow bot` in the `Post in` field
12. Enter in your email address in the `Recipient` field
13. Enter `Hello World!` in the `Message` field.

![Screenshot of the post a message dialog](screenshots/05.png)

14. Open up the `When an HTTP request is received` step
15. Show `advanced options` and change the `Method` field to `GET`

![Screenshot of the when you receive an HTTP request dialog](screenshots/06.png)

16. Copy the URL
17. Save your flow
18. Test your flow by clicking on `Test`, hoosing `Manually`, and then clicking `Test`
19. Open a browser tab, paste in the HTTP URL and hit enter
20. Verify that you received a chat message in Teams
21. Verify that your flow completed successfully

![Screenshot of the test run](screenshots/07.png)

## Enhance the simple flow to add parameters

Now we're going to make a simple change to our flow. Testing it will be a bit more complicated because we'll be making an HTTP Post instead of a simple GET. Thus, well need a tool like [Postman](https://www.postman.com/downloads/) to test it. If you don't have Postman installed, you'll want to do that first.

1. Click `Edit` to open up the flow.
2. Expand the `When an HTTP request is received` trigger
3. Go to the `advanced options` and change the `method` field to POST.
4. In the `Request Body JSON Schema` field enter in the following:

```json
{
    "type": "object",
    "properties": {
        "message": {
            "type": "string"
        }
    }
}
```
5. Expand the `Post a message in chat or channel` action
6. Remove `Hello World!` from the field
7. Choose `Select parameter`
8. Select the `message` parameter

![Screenshot of selecting parameters](screenshots/08.png)

9. Enter in `Hello World` and `Hello World` when prompted to give a format example
10. Click `Get expression` and click `Apply`
11. Save the flow
12. Create a new Postman collection by pasting the URL and choosing `POST` as the method.

![Screenshot of Postman request screen](screenshots/09.png)

13. Select the `Body` tab in the Postman request and choose the `raw` content type.
14. Past in the following JSON:

```json
{
    "message": "Hello from Anthology Together 2023!"
}
```
![Screenshot of Postman body screen](screenshots/10.png)

15. Back in Power Automate, manually Test your flow
16. In Postman click `Send`
17. Verify that you received a chat message in Teams that says `Hello from Anthology Together 2023!`
18. Verify that your flow completed successfully