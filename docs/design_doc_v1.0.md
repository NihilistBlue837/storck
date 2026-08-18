---

---
## MVP
### <u>What Organizations can accomplish:</u>
- Register their organization and login.
- Add, remove, edit the data of the needs/goods within the organization.
- Pull monthly records (thin; can only pull pounds in, pounds out/needs that arrived and left, where they came from and when they left - the ledger portion)
- Edit store hours for a given day, so Users have updated info on when to drop off their donated goods.
- Sort needs based on how much is in inventory, and see suggestions for these needs. Flow would follow as: Org selects 'sort by amount needed', sees an item only needs three more to fulfill donation requirement, they will see a suggestion tag next to it, stating "add x amount needed". Orgs can click this button and have the need require that x amount for it to be fulfilled. This also applies to a need being empty. Orgs can also manually set an additional amount.
- Confirm or deny receipts based off whether a user ended up dropping off the items.
- Receive an emailed receipt that will display the name of the person dropping off items, what items they are dropping off, and what day they will arrive (this receipt is not stored into the Record until Organization confirms drop off).  
### <u>What Users can accomplish</u>:
- Login via username and password.
- Search for items the organization needs.
- Add and remove needs from a 'cart'.
- Review the items they want to donate.
- Confirm a drop off day.
- Select 'Confirm Dropoff', which finalizes their cart.
- Receive an emailed receipt of time their cart was finalized, items expected to be donated, and their drop off day.  
### <u>What the application engine (backend) can accomplish</u>:
- Persistence for Organization and User information. This includes: User/Organization name and password, updated Organization items and records, items that a user has in a cart but hasn't finalized (e.g. item added to cart, user logged out, upon logging back in the item should still be there), etc.
- Handle possible conflicts with multiple items being reserved at once (concurrency issues).
- Hold 'receipts' for the organizations. A 'pre-receipt' is created once a User finalizes their cart, but database actions aren't handled until the Organization confirms the drop offs. Once the Org finalizes a drop off, a real receipt is created and logged, and the DB action happens. This is to avoid issues arising with users reserving goods and then no showing at donation time, therefore messing up counts.
-  Handle generating reports based on actions in the database for the month/receipts.
- Auto-suggest needs based on current counts. If an item reaches within a certain threshold (e.g. the org only needs three more containers of peanut butter), suggest increasing need amount.
- Auto delete a receipt if not confirmed within 14 days.
- Notify user and organization when a receipt has been deleted (via email)

## Data Models/Objects
The data models are structured as follows:

- **Item**: id, pantry_id, name, in_stock, amount (lbs, cans, containers), amount_requested, amount_needed
- **Pantry**: id, org_id, name, total_items
- **Organization**: id, name, location, hours, pantry, admin, email, password
- **User**: id, name, email, password, cart
- **Receipt**: id, actions, pantry, organization, user, timestamp, is_confirmed
- **Record**: id, pantry, organization, receipts
- **Cart**: id, userId, pantry, items

## Design Decisions
- How should specific items be handled?
	- Solution 1: User creates item with a name and count, program searches for item in the current pantries DB based on name, if name is found, warn user item is already in pantry.
	- Drawbacks: Every single item is attached to a Pantry DB. Items are not reusable.
	- Rationale: This may be a non issue. Each Pantry having its own unique item (even if the actual, real life item is the same) promotes uniqueness and ensures an operation on one item in a DB will not affect a different item. May need to revisit