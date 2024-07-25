This is a demo of HIFI's features.

In this demo, you will create a user and list users. This is a live demo using HIFI's sandbox.

<style>
    .step {
        background-color: rgba(135, 206, 235, 0.5);
        padding: 10px;
        border-radius: 2px;
        margin-bottom: 20px;
    }
</style>

<script>
    console.log('kelly')
    window.onload = function() {
        const usersButton = document.getElementById("users")
        const userForm = document.getElementById("user-form")

        usersButton.onclick = async function() {
            const apiInput = document.getElementById("api-key").value;
            const usersRequest = await fetch('https://try.readme.io/https://sandbox.hifibridge.com/user/all', {
                headers: {
                    "authorization": `Bearer ${apiInput}`
                }
            });
            const users = (await usersRequest.json())['users'];
            let usersHTML = "<ul>";
            console.log(users);
            users.forEach((user) => {
                usersHTML += `<li>Name: ${user['name']}, ID: ${user['userId']}</li>`
            })
            usersHTML += "</ul>";
            document.getElementById('users-list').innerHTML = usersHTML;
        }
        
        userForm.onsubmit = async function(event) {
            event.preventDefault();
            const apiInput = document.getElementById("api-key").value;
            document.getElementById('message').innerText = "Loading";
            const request = await fetch('https://try.readme.io/https://sandbox.hifibridge.com/user/create', {
                method: "POST",
                headers: {
                    "accept": "application/json",
                    "content-type": "application/json",
                    "authorization": `Bearer ${apiInput}`
                },
                body: JSON.stringify({
                    "userType": "individual",
                    "legalFirstName": document.getElementById('first-name').value,
                    "legalLastName": document.getElementById('last-name').value,
                    "complianceEmail": "[[email protected]](/cdn-cgi/l/email-protection)",
                    "compliancePhone": "+17025055658",
                    "dateOfBirth": "1964-05-13",
                    "taxIdentificationNumber": "431931235",
                    "addressLine1": "2921 S Cooper St",
                    "city": "Arlington",
                    "stateProvinceRegion": "TX",
                    "postalCode": "76015",
                    "country": "USA",
                    "signedAgreementId": "b5a913fd-7cfc-4609-ae27-3a0a46298037",
                    "ipAddress": "31.222.254.178",
                    "govIdCountry": "USA"
                })
            });

            if (request.status === 200) {
                document.getElementById('message').innerText = "Successfully created a user";
            } else {
                document.getElementById('message').innerText = "Got an error creating a user";
            }
        }
    }

    function test(event) {
        event.preventDefault();
        // console.log(document.getElementById('name').value)
    }
</script>


<div class="step">
    First put your API key here: <input type="text" id="api-key" value="zpka_9187d9e5b941435f8d934ad788f93254_3d7efa93">
</div>
<div class="step">
    Next, create a user. This calls HIFI's [create user](/pi/user/create_post) endpoint.

    <form id="user-form">
        <input id="first-name" placeholder="First name" required>
        <input id="last-name" placeholder="Last name" required>
        <button id="create-user">Create</button>
    </form>

    <div id="message"></div>
</div>

<div class="step">
    Next, load users. This calls HIFI's [get users](/pi/user/all_get) endpoint. <button id="users">Load users</button>

    <div id="users-list"></div>
</div>
