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
 