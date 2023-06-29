# Anthology Together 2023 Power Automate Workshop

This guide is designed to walk workshop participants through the various steps
of modeling a Power Automate workflow that communicates with Anthology Student
and Microsoft Teams. By the completion of the workshop, participants will have
completed a functional Power Automate workflow that can be initiated manually
or via the Anthology Student Workflow Engine. This workflow will accept parameters
which are used to query Anthology Student and initiate a approval workflow that
notifies a user via Microsoft Teams, prompts for their approval or disapproval, and then updates Anthology Student accordingly.

### What you will need
In order to participate in this workshop participants will need an Azure tenet  with Power Automate and Microsoft Teams. Participants will also need software (either command line or other) capable of sending HTTP POST request to URLs. We will be using [Postman](https://www.postman.com/downloads/) thoughout this guide.

---

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

---

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
8. Select the `message` parameter

![Screenshot of parameter dialog](screenshots/13.png)

9. Save the flow
10. Create a new Postman collection by pasting the URL and choosing `POST` as the method.

![Screenshot of Postman request screen](screenshots/09.png)

11. Select the `Body` tab in the Postman request and choose the `raw` content type.
12. Past in the following JSON:

```json
{
    "message": "Hello from Anthology Together 2023!"
}
```
![Screenshot of Postman body screen](screenshots/10.png)

13. Back in Power Automate, manually Test your flow
14. In Postman click `Send`
15. Verify that you received a chat message in Teams that says `Hello from Anthology Together 2023!`
16. Verify that your flow completed successfully

---

## Add an approval to the flow
Now we are going to add a simple approval to the flow. The idea is to get the basic flow in place before we complicate things with Anthology Student.

1. Click `Edit` to open the flow in the designer
2. Click `+ New step` 
3. Enter `approval` in the search box
4. Select `Start and wait for approval`

![Screenshot of the choose an operation dialog](screenshots/11.png)

5. Select `Approve/Reject - First to respond` in the `Approval type` field
6. Enter `Approval Request` in the `Title` field
7. Enter your email address in the `Assigned to` field
8. Enter `Need Approval ASAP!` in the `Details` field

![Screenshot of the approval dialog](screenshots/12.png)

9. Test your flow, but this time choose `Automatically` and select `With a recenty used trigger`
10. Choose the most recently used successful trigger
11. Check Microsoft Teams for the approval and approve

![Screenshot of the Teams approval dialog](screenshots/14.png)

---

## Add notification of approval outcome to flow
While the approval flow works as is, we're next going to add flow control to notify a user as to the outcome of the approval.

1. Click `Edit` to open the flow in the designer
2. Click `+ New step` 
3. Select `Control` and then select `Condition`

![Screenshot of the control dialog](screenshots/15.png)

4. Select `Outcome`, `is equal to` and Select `Approve` for the value

![Screenshot of the control step](screenshots/16.png)

5. Scroll down to the `Yes` condition and click `Add an action`
6. Choose Teams and then select `Post message in chat or channel` just like we did previously.

![Screenshot of the Teams step](screenshots/17.png)

7. Use the same configuration for this step as well, execpt in the `Message` field select `Response summary` and then enter in the text `APPROVAL COMMENTS` and select `Responses Comments`

![Screenshot of the Teams message](screenshots/18.png)

8. Scroll down to the `No` condition and apply the same configuration as you did for `Yes` except in the `Message` field replace `APPROVAL COMMENTS` with `REJECTION COMMENTS`

![Screenshot of the Teams message](screenshots/19.png)

9. Test your flow, but this time choose `Automatically` and select `With a recenty used trigger`
10. Choose the most recently used successful trigger
11. Check Microsoft Teams for the approval and approve/reject adding a comment
12. Verify that you received a Teams message with the correct accept/reject message

---

## Add Anthology Student Connector
We now have a basic flow with a HTTP trigger, an initial notification, an approval workflow and a notification of the outcome of that workflow. We will now begin to add API calls to Anthology Student via a `Connector`.

1. Navigation to `Data` > `Custom Connectors`
2. Click `+ New custom connector` > `Import an OpenAPI file`
3. Name the custom connector `AT23 Anthology Student` and import the the OpenAPI file from [anthology-student-connector.json](resources/anthology-student-connector.json)

![Screenshot of the new custom connector dialog](screenshots/20.png)

4. Accept the defaults on the `General information` screen and click `Security ->` at the bottom of the screen

![Screenshot of the connector general info screen](screenshots/21.png)

5. Accept the defaults on the Security screen and click `Definition ->`
6. Click the Check at the top of the screen to save the custom connection

![Screenshot of the connector final screen](screenshots/22.png)

7. Now we are prepared to use this custom connection in our Flow.

---

## Modify the input schema and test output
Having created our custom connector to Anthology Student, we can now start to add the actions to invoke Anthology Student APIs. First, however, we need to modify the JSON schema so that we can use the input parameters in our flow and test to make sure the parameters are being property posted.

1. Edit the flow, and then expand the `When a HTTP request is received` trigger
2. Remove the `message` parameter from the `JSON Schema`.
3. Add a two new properties to the `JSON Schema`: An `integer` named `studentId` and an `integer` named `documentId`.

![Screenshot of the HTTP trigger dialog](screenshots/23.png)

Your completed JSON Schema should look like this:

```json
{
    "type": "object",
    "properties": {
        "studentId": {
            "type": "integer"
        },
        "documentId": {
            "type": "integer"
        }
    }
}
```

4. Update the `Post message in chat or channel` action to remove the `message` from the `Message` field.
5. Save the flow (this is required for us to access the new parameters)
6. Update the `Message` field to include the new parameters we added to the `JSON Schema`, `studentId` and `documentId`.

![Screenshot of the Post message in chat or channel action](screenshots/24.png)

7. Save the flow
8. Since we changed the `JSON Schema` we need to go back to `Postman` and update the requst body to include those fields.

![Screenshot of the Postman body](screenshots/25.png)

9. Test the flow `Manually` (because the posted data has changed) and submit the API call via Postman to initiate the HTTP `POST`
10. Verify that the message you received in Microsoft Teams incluces the appropriate `studentId` and `documentId`
11. Verify that the approval flow works as it did previously

---

## Add Anthology Student API Calls

Now that the JSON schema has been updated and tested, we can add Anthology Student API calls which will make use of these parameters.

1. Insert a new step after the `Post message in chat or channel` action
2. Select `Custom` and then choose the `AT23 Anthology Student` connector
3. Select the `Get Student By Id` operation

![Screenshot of the custom action dialog](screenshots/26.png)

4. Select the `studentId` parameter for the `id` field

![Screenshot of the Get Student By Id action](screenshots/27.png)

5. Insert a new step after the `Get Student By Id` action
6. Select `Custom` and then choose the `AT23 Anthology Student` connector
7. Select the `Get Document` operation

![Screenshot of the custom action dialog](screenshots/28.png)

8. Select the `documentId` parameter for the `id` field

![Screenshot of the Get Document action](screenshots/29.png)

9. Save the flow
10. Verify it looks like this:

![Screenshot of the current flow](screenshots/30.png)

---

## Use data from the Anthology Student API Calls
Having added Anthology Student API calls to the flow, we now need to ensure that they work. We'll start with some simple variables which will be populated from the returned API data. We'll then use these variables in the Microsoft Teams approval workflow.

Note: You will need the ApiKey from your Anthology Student instance in order to initiate the connection. Edit your connection from the `Connections` screen. This workshop assumes your connection was initiated when you imported the the `OpenAPI` file.

1. Click `Insert a new step` after the `Get Document` step
2. Type in `var` in the search box and then select `Initialize variable`

![Screenshot of the variable dialog](screenshots/31.png)

3. Enter `URL` in the `Name` field
4. Select `string` in the `Type` field 
5. Enter in the full URL to your Anthology Student enviroment in the `Value` field

![Screenshot of the URL variable step](screenshots/32.png)

6. Repeat the above instructions to add a new `Initialize variable` step below the `URL` variable
7. Enter `Link` in the `Name` field
8. Select `string` in the `Type` field
9. Use the `URL` variable, the `studentId` parameter, and the `documentId` parameter to construct the link to the Student Document in Anthology Student.

![Screenshot of the Link variable step](screenshots/33.png)

10. Repeate the above instructions to add a new `Initialize variabel` step below the `Link` variabe
11. Enter `Name` in the `Name` field
12. Select `string` in the `Type` field
13. In the value field enter the follwing expression:

```javascript
concat(body('Get_Student_By_Id')['payload/data']['FirstName'],' ',body('Get_Student_By_Id')['payload/data']['LastName'])
```

14. Expand the `Start and wait for approval` step
15. Update the `Details` field to include the `Name` variable
16. Update the `Item link` field with the `Link` variable
17. Update the `Item link description` field with the text `Go to student document`

![Screenshot of the updated start and wait for approval step](screenshots/34.png)

18. Save the flow

19. In Postman, update the `studentId` and `documentId` to values from a record in your Anthology Student enviroment
20. `Manually` test the flow
21. Invoke the requst in Postman
22. Verify that the approval request includes the appropriate values from the Anthology Student API requests

![Screenshot of the updated approval request](screenshots/35.png)