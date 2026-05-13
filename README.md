# Robotic Process Automation — Final Project

## Bolt Socks Order-to-Cash Consulting Project

This project analyzes and automates the order-to-cash process for **Bolt Socks**, a wholesale sock distributor. As a consulting team, the objective was to identify inefficiencies driving cash flow challenges and recommend improvements to get paid faster.

The project covers three components: process mapping, process mining, and process automation.

---

## Part 1 — Process Mapping (Lucidchart)

Created a swim lane process map in Lucidchart based on an interview with Bolt Socks leadership.

**Process map includes:**
- 5 swim lanes: Customer, Sales, Warehouse, Finance, Supplier
- 8+ activities covering the full order-to-cash lifecycle
- 1+ decision points (e.g., RFQ required? Sufficient inventory?)

The interview revealed that large customers sometimes call in to negotiate pricing before placing orders, warehouse claims a 24-hour pick turnaround, and invoices are created manually after delivery confirmation.

📄 See `process_map/bolt_socks_process_map.pdf`

---

## Part 2 — Process Mining (Python / PM4Py)

Mined a SAP event log with **5,047 orders** and **33,615 total activities** to discover the actual process versus the described process.

**Key findings:**

| Metric | Value |
|---|---|
| Total orders | 5,047 |
| Total activities | 33,615 |
| Distinct activities | 12 |
| Median throughput time | 24 days |
| Orders starting with RFQ | 95 |
| Customer pickup occurrences | 600 |
| Avg. time: Sales Order → Picking Done | 4 days |

**Notable discoveries:**
- Warehouse claimed 24-hour picking turnaround; actual average is **4 days**
- 194 orders flowed through both *Customer pick-up* and *Shipment Sent* — a process anomaly suggesting partial fulfillment or double shipping
- 499 purchase orders found — Bolt Socks was buying materials to fulfill sales, introducing supplier lead time dependency never mentioned by the team
- Some cases began at *Delivery Completed*, which is logically impossible in a normal process
- Frequent *Delivery Changed* events (rework loops) never acknowledged by leadership

**Google Colab Notebook:** https://colab.research.google.com/drive/1urWP6HwasAXmc6J0hiG4Kyjeble2f5W5?usp=sharing

📄 See `data/order_to_cash_event_log.csv`

---

## Part 3 — Process Automation (Zapier)

Automated invoice generation to close the gap between delivery confirmation and invoice creation (identified as >1 day in the event log).

**Workflow:**
1. **Trigger** — New or updated row added to the Bolt Socks delivery spreadsheet in Google Sheets
2. **Action 1** — Create a new Google Doc invoice from a template, populated with order number, amount due, and invoice date
3. **Action 2** — Store the completed PDF invoice in the *Invoice Files* Google Drive subfolder

**Results:** 8 invoices successfully automated in a single run, covering orders 342533–342540 across customers including Retail by Nature ($13,500), Truby's Apparel ($21,000), and Wetzel's Fun Corner ($12,000).

📄 See `data/zapier_run_history.csv`

---

## Recommendations

1. **Fix picking throughput** — Actual average is 4 days vs. the claimed 24 hours; improve warehouse scheduling and staffing
2. **Reduce delivery changes** — Frequent rework loops delay shipping; improve delivery-date accuracy up front
3. **Automate invoicing** — Eliminate the manual >1-day gap between confirmed delivery and invoice creation (demonstrated in Part 3)
4. **Address inventory gaps** — 499 purchase orders signal reactive buying; pre-stock high-volume items to remove supplier lead time from the cycle
5. **Standardize customer pickup** — 194 orders moved through both pickup and shipment, creating confusion; enforce clear process rules for pickup vs. delivery

---

## Technologies

| Tool | Purpose |
|---|---|
| Lucidchart | Swim lane process mapping |
| Python (PM4Py, pandas) | Process mining and event log analysis |
| Google Colab | Notebook environment |
| Zapier | No-code workflow automation |
| Google Sheets / Docs / Drive | Data source, invoice template, file storage |
