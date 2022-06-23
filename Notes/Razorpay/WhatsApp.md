### Milestones
- Onboarding & Recovery (product, design, tech spec, implementation, testing)
- Product hub (product, design, tech spec, implementation, testing)
	- Payments
	- Refunds & Disputes
	- Support & Reports
	- Risk
	- Care
	- Analytics

Onboarding & Recovery
Open items from discussion 13 june
- Web hook payload for onboarding changes & account updates post - soln - Can we have two different endpoints to solve it for meta 
- Web hooks refresh token - Need to share the timelines/ additional effort for implementing the access token change for web hooks (stork)
	- Soln - Can we have partner service refresh the token on stork?
- Go through web hooks docs share by meta
- RZP to share the AppIds with meta to get it whitelisted(process takes a couple fo weeks)
- AI - Come up with a new possible states for Account + Activation (3 X 2) . To be added in WABA doc
- AI - To introduce a new status for pending
- AI - Need more details on when account is activation status & account status. State transition details 
- AI - Need to filter account entity details 
- Merchant VPA - WA wants the VPA as part of onboarding API. We need to check where we are creating this in current flow
- Account should be pending if VPA is not returned. Given that they are ok with this, can we move few other things to async as well?
- Open - WA account has been off boarded, Can an account be recovered via other flows by a merchant.
- Open - Delete API. Are suspended/deleted same from WA perspective?
- AI - Change phone number flow API to be added.

Payments hub 
- Check how VPA is created
- Check VPA creation latency

Tech spec discussion
- Add a matrix explaining activation status + account_status
- Also add a note internally why we are not discussing payment & settlement related status now. Thought process behind this
- Phone Number API change - We need to present API spec (bhavya is owner) - This will be a WIP doc
- Error codes - WIP. We will need to do internal review
- Reason codes - We need to identify this for account transitions. (bhavya)
Flow discussion with Jagan
- Regex validation for inputs
- Dedupe 
- BVS check
- Only after BVS check passes we make it eligible for dedupe
- For edge cases, we will go live & investigate if there are any slip-ups, we will then setup a cron job to run dedupe on already activated accounts. 
- We will identify the PAN type based on 4th letter & Store the pan accordingly. 
	- This has an implication on response - we will return pan accordingly in Promoter PAN/ Company PAN


***

June 14 Payments discussion with Meta
- To share the payments APIs with Meta (jagan)
- For payments initiated outside of WA/RZP context, WA needs webhook events 

***

June 15 
- Is Account Suspended flag to be introduced in the CreateOnboardingUrl instead of CheckAccountExist
	- AI - RZP to update the final mode
	- Suggested by meta - Have single flag, throw an error if account is suspended
- Merge both URL generation flows - This model works with multiple use cases tomorrow. 
	- Update the response to contain metadata of the action
	- All the urls are onetime. 
- Update Endpoint for CreateOnboardingURL
- Updated mobile number format - not explicit country code, it will be passed with mobile number
- What are the limits for the token length
- What is Request ID
	- Short lived
	- Not reusable
	- Unique
- If the user drops off without completing onboarding then they will always use a new onboarding flow, so the recover account flow request ID is different & we create a new account
- Check and come back on VPA creation. Meta wants to do this in realtime & activation is completed only after VPA creation is done. 
- introduce third party error code if there is a third party service outage

***
June 16th MOM with WA team
- Suggestion by WA to introduce a error code which specifies ThirdParty Issue
	- Also wanted a reason code if error is generic
- Rename Source to category in error code 
- WA want to pass Correlation ID (GUID) for every request for debugging purposes
- WA want KYC level (eg: P2PM completed, P2M completed) in account entity instead of specific account statues (ex : Activated_p2pm )
- Questions about refund : 
	- Are refunds(merchant& system triggered) enabled if PaymentsEnabled flag is false
- PAN & POI is not required in Account response
- Can expired POI be submitted in POI (Ex : DL ) - No ( jagan confirmed)
- WA Do not want special characters in mobile number field (Want it in 919xxxxxxxxx format)
- WA ask - Can we return docs/POI details submitted in a generic entity format? 
- WA ask - Last updated reason in Account entity?
- Srini to comeback on preferred Webhook structure after discussing with John

***

June 20 MOM 

Org Discussion : 
- Do we need a separate org for WhatsApp?
	- Requirements
		- No Dashboard Access
		- Support Ticketing can be done via an Org Construct
- Raja to discuss with Hemanth to understand Pros/Cons & setup efforts. 
- Live/Test mode for APIs & how does it effect things internally. 

***

June 22 Internal Tech discussion  (Attendees : Bhavya, Kartik, Raja)
 - WhatsApp service Owned Entities
	 - URL Entity
		 - Has ID, AccountId, ContactNo, ExternalID, Type.Action (Onboarding.Create/Onboarding.Resume), Expiry & Status
	 - Properties : 
		 - One time usable. Cannot be opened more the once.
		-   There will only be one active session for a merchant across any possible flow (Ex : Create/resume onboarding/ verify 2FA).  But, there can be more than one generated but unused urls.
		-   A URL generated for one action can’t be repurposed. (Ex : URL was generated for resume onboarding, FE has to ensure this used for any other action. BE will restrict it. )
	- Validation Results
		- contains artifact type, result, BVS validation id, Account ID & audit timestamps

APIs/Actions On WhatsApp Service. 
- CheckAccount (We need to build a corresponding getAPI on Monolith to do this check)
- Generate URL
- Validate Token
- GetAccountWithFilters ( We would need a GET API on Monolith for querying)
- CreateAccount/UpdateAccount (POST/PATCH API for Account on monolith, BVS Call for verification & De-dupe service call for risk & Duplication checks)
- Verify2FA (Uses GET API on Monolith)
- VerifyAadharOTP/Resend Aadhar OTP/ Refersh Aadhar Captcha - Use BVS Apis
- Suspend Account (Delete/purge API on Monolith)

Open points : 
- Where do we store POI information for Driving License, Aadhar Name & Address. In current setup, Merchant & Details table don't have columns for these. We would need to extend the details table to store this information. 
- Should we sync BVS Validation result? - As of now, we are not seeing a functional usecase for this, apart from maintaining data parity. 
- in case of Manual flow, when merchant uploads docs, how is that info synced b/w WA Service <> API. Who is the owner? (For this flow, we will get clarity in later phases)
-  Should Dedupe RISK call be triggered from API or WA service? (We decided API might be a better place to do this as the risk score/threshold are updated on API db. )

AIs for tech spec : 
- URL validation rules mentioned above have to be strictly followed for security purposes. 
- URL Type to Allowed action has to be managed in code. 
- BFF has to make sure that there is only one active session per account irrespective of the action type. 
- We don't need to persist token on our end. We should be able to consistently regenerate it based on the URL entity Id. 
  
  Tech spec update : 
  Completion percentage : 70%
  Open items : 
  - Resiliency 
  - Internal API listed above to be documented
  - DB Schema discussed above to be documented. 
  - Tech stack decisions & details to be added. 
