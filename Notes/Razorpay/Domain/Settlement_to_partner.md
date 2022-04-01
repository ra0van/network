### Old Flow - Settle to Partner
- Pre-Settlement steps
	- settle_to_partner is configured at partner_config (can be configured at both partner level & sub-merchant level).
	- we consider sub-merchant's partner_config if it is present (to be confirmed).
- During Settlement Creation
	- AGGREGATE_SETTLEMENT feature flag has to enabled at partner level.
	- Merchant cannot be associated with more than one partner.

### New Flow - Settlement Service ( Aggregate settlement )
- **Settle to partner** is replaced by aggregate settlement feature.
- This feature was in monolith in old flow, which is moved to the new settlement service (NSS).
- [TODO] How does the sub-merchant level flag impact this feature?
- For all partners & sub-merchants, for whom the aggregate settlement is enabled, the tree hierarchy is maintained at the NSS & checked during settlements. 
- The partner & sub-merchant events are pushed to NSS & the feature is updated accordingly. 
