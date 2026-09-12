# Client Demo

Open `index.html` in a browser — no server or internet connection required (Bulma is vendored locally in `assets/css/vendor/`). It is a frontend-only prototype using vanilla JavaScript; it makes no backend requests and stores nothing outside the current browser session, so it's safe to email as a folder or open straight off a laptop in front of a client.

Suggested client walkthrough:

### Staff path

1. Enter as **Staff**.
2. Select **Run guided demo**.
3. Review the two sale items and partial payment, then choose **Confirm sale**.
4. Print or close the invoice.
5. Open **Customers & Due** to collect a later payment and view the updated statement.

### Owner path

1. Reset the demo and enter as **Owner**.
2. Select **Run guided demo**. It prepares two below-minimum prices and visibly records them as owner overrides — a permission only the Owner role has.
3. Confirm the sale, then select **View owner report** in the invoice.
4. Review sales, gross profit, and the override count. Add stock from **Inventory** if desired.

Use **Reset demo data** to return to the initial state between walkthroughs.

## Scope note for the client

This prototype covers the V1 core workflow from the requirements document: stock intake, negotiated-price sales, partial payment and due tracking, customer statements, and owner-only gross-profit reporting. Sales returns, VAT/tax, and multi-branch support are intentionally out of scope for this phase — say so up front if asked, rather than letting their absence look like an oversight.
