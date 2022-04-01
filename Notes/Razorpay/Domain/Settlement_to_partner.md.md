### Old Flow - Settle to Partner
- Pre-Settlement steps
	- settle_to_partner is configured at partner_config (can be configured at both partner level & sub-merchant level)
	- we consider sub-merchant's partner_config if it is present (to be confirmed) 
- During Settlement Creation
	- AGGREGATE_SETTLEMENT feature flag has to enabled at partner level
	- Merchant cannot be associated with more than one partner
