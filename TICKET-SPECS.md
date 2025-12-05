# Auto-Organize Orders - Ticket Specifications

## Overview

Enable users to automatically organize work order items by Group or Subcategory, reducing 500+ manual clicks to 3 clicks when creating orders from large estimates.

---

## Ticket 1: View Toggle Component

### Summary
Replace the current flat list / custom sections toggle with a view switcher supporting four modes.

### Acceptance Criteria

**View Modes:**
| View | Description |
|------|-------------|
| Flat List | Simple table, no grouping (existing behavior) |
| Custom Sections | User-created sections with drag-and-drop (existing behavior) |
| By Group | Auto-creates sections based on item's Group field |
| By Subcategory | Auto-creates sections based on item's Subcategory field |

**Behavior for By Group / By Subcategory:**
- [ ] Automatically identifies unique values in the Group or Subcategory field
- [ ] Creates a section for each unique value
- [ ] Places items into their corresponding section
- [ ] Section names match the Group/Subcategory value
- [ ] Sections are collapsible
- [ ] "Show section total" toggle available per section

**Surface Areas (all must render the view switcher):**
- [ ] Send for Signature - order table view
- [ ] Add to Order modal (when "Create New Order" selected)
- [ ] Create Work Order (Orders tab)
- [ ] Edit Order (existing order)

### Design Reference
See Figma: **Core Flow** (Row 1) - 5 screens showing view switching progression

### Out of Scope
- Add Items By experience (Ticket 2)
- Changes to Flat List or Custom Sections behavior

---

## Ticket 2: Add Items By Experience

### Summary
A new component for adding items in bulk within the By Group and By Subcategory views.

### Acceptance Criteria

**UI Components:**
- [ ] Dropdown to select method: "One item at a time", "All items in Group", "All items in Subcategory"
- [ ] Secondary dropdown that changes based on selection:
  - One item: Shows individual items
  - All in Group: Shows list of groups with counts (e.g., "Kitchen (15 items)")
  - All in Subcategory: Shows list of subcategories with counts (e.g., "Labor (50 items)")
- [ ] Preview text showing how many items will be added
- [ ] Add button to execute
- [ ] Label updates dynamically ("Select item:" / "Select group:" / "Select subcategory:")

**Add Flow:**
1. [ ] User selects add method from dropdown
2. [ ] User selects specific value from secondary dropdown
3. [ ] Preview shows item count (e.g., "5 Kitchen items will be added")
4. [ ] User clicks Add
5. [ ] Items are added and auto-organized into appropriate sections
6. [ ] Success state displays briefly in the preview area

**Integration - Adding to Existing Order:**
- [ ] When adding items to an order organized "By Group", items trickle into sections matching their Group field
- [ ] When adding items to an order organized "By Subcategory", items trickle into sections matching their Subcategory field
- [ ] New sections are created automatically if item's Group/Subcategory doesn't match existing sections

### Design Reference
See Figma:
- **Adding Items in a Group** (Row 2) - 5 screens showing group-based add flow
- **Adding Items in a Subcategory** (Row 3) - 6 screens showing subcategory-based add flow

### Dependencies
- Ticket 1 (View Toggle Component) must be complete

---

## Post-Release Validation Checklist

### Viewing an Order (Side Drawer)
- [ ] Section names render correctly based on organization mode
- [ ] Sections display in correct order
- [ ] Group or Subcategory names properly reflected if auto-sectioned

### Adding to Existing Order
- [ ] Items trickle into correct sections based on order's organization mode
- [ ] New sections auto-created when needed

---

## Design Assets

**Figma File:** [Link to be added]

**Flows Documented:**
1. Core Flow - View switching between all four modes
2. Adding Items in a Group - Bulk add by Group value
3. Adding Items in a Subcategory - Bulk add by Subcategory value

**Interactive Prototype:** https://auto-organize-groups.vercel.app/albi-orders/wo_po_prototype/orders.html

---

## Context

### Problem
When creating work orders from estimates with 500+ line items, users must:
1. Add items one-by-one
2. Create custom sections manually
3. Find items belonging to each section
4. Drag items into sections individually

This results in hundreds of clicks to organize a single work order.

### Primary User
Guardian Restoration Partners uses Groups to identify rooms (Kitchen, Bathroom, Bedroom) to communicate scope to subcontractors on large fire loss projects with 30-50 work orders.

### Success Metrics
- Reduction in time to create organized work orders
- Increase in work order adoption
- Guardian satisfaction with the workflow
