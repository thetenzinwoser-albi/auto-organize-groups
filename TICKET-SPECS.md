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
- [ ] "Show section total" toggle available per section
- [ ] Default section order is alphabetical
- [ ] Users can drag-and-drop sections to reorder them
- [ ] Users can inline edit items in all views
- [ ] Editing an item's Group value in By Group view moves the item to the matching section
- [ ] Editing an item's Subcategory value in By Subcategory view moves the item to the matching section
- [ ] **In By Group view:** Group field is dropdown-only (no free-text). User can select from existing Groups but cannot create new Groups. To create a new Group, switch to Flat List or Custom Sections.
- [ ] **In By Subcategory view:** Subcategory field is dropdown-only (no free-text). User can select from existing Subcategories but cannot create new Subcategories. To create a new Subcategory, switch to Flat List or Custom Sections.
- [ ] If an item is moved to a Group/Subcategory that doesn't have a section in this order yet, a new section is created for it
- [ ] **New section placement:** New sections appear at the top of the section list (easy to spot, no scrolling needed). User can drag to reorder if needed.
- [ ] If an edit or deletion leaves a section empty, that section is removed (By Group and By Subcategory only - Custom Sections retains empty sections)

**Item Reordering:**
- [ ] Users can drag-and-drop items to reorder within a section (all section-based views)
- [ ] Users can drag-and-drop items to reorder in Flat List view
- [ ] Flat List order is preserved when switching between views

**Moving Items Between Sections (drag-and-drop):**
- [ ] Custom Sections: Dragging an item to a different section changes its section membership
- [ ] By Group: Dragging an item to a different section updates the item's Group value
- [ ] By Subcategory: Dragging an item to a different section updates the item's Subcategory value
- [ ] All changes are pending until the user saves the modal

**Adding/Removing Items Across Views:**
- [ ] Removing an item removes it from all views
- [ ] Removed items are still available in the Items list to be added back to the order
- [ ] All removals are pending until the user saves the modal (Cancel restores removed items)

**Deleting Sections:**
- [ ] Deleting a section removes all items in that section from the order (applies to Custom Sections, By Group, and By Subcategory)
- [ ] In By Group / By Subcategory, deleting a section is a bulk action to remove all items with that Group/Subcategory value
- [ ] Deleted items are still available in the Items list to be added back
- [ ] Section deletion is pending until save (Cancel restores the section and its items)

**Where new items appear when switching views:**

| Add item in... | Switch to Flat List | Switch to By Group / By Subcategory | Switch to Custom Sections |
|----------------|---------------------|-------------------------------------|---------------------------|
| Flat List | N/A | Item goes to its matching Group/Subcategory section | Existing behavior (do not change) |
| Custom Sections | Existing behavior (do not change) | Item goes to its matching Group/Subcategory section | N/A |
| By Group | Bottom of flat list (can be reordered) | Item goes to its matching section | Existing behavior (do not change) |
| By Subcategory | Bottom of flat list (can be reordered) | Item goes to its matching section | Existing behavior (do not change) |

**Empty State:**
- [ ] When no items exist, show "Add items to this order to see them here." placeholder
- [ ] Add Items component remains visible and functional in empty state

**Handling Missing Values:**
- [ ] **By Subcategory:** Items without a Subcategory value go into an "Other" section
- [ ] **By Group:** Items without a Group value go into an "Other" section (edge case - items should always have a Group, but fallback if somehow missing)
- [ ] "Other" section is placed at the bottom by default (not at top like named sections - it's a catch-all, not an active addition)
- [ ] "Other" section can be manually reordered like any other section

**View Mode Persistence:**
- [ ] View mode persists when saving and closing the order
- [ ] Reopening the order restores the last-used view mode

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

### Grooming Discussion: View Switching State Management

**Context:** Today's Flat List <-> Custom Sections toggle preserves state when switching between modes. The existing behavior:

- **Sections OFF -> ON:** Restores last-known section membership and section order
- **Sections ON -> OFF:** Flattens to single list but remembers section membership for future restore
- Membership only changes when sections are ON; flat reordering doesn't affect saved membership

**Ideal UX for the new modes:**

Each view mode (Flat List, Custom Sections, By Group, By Subcategory) would independently remember its state:
- By Group remembers any manual section reordering
- By Subcategory remembers any manual section reordering
- Switching between modes and back preserves your work

**Scenarios to consider:**

| From | To | Back to original | Ideal behavior |
|------|----|------------------|----------------|
| By Group (reordered) | Custom Sections | By Group | Remembers reordered section order |
| By Group (reordered) | By Subcategory | By Group | Remembers reordered section order |
| By Group (reordered) | Flat List | By Group | Remembers reordered section order |
| By Subcategory (reordered) | Any other view | By Subcategory | Remembers reordered section order |
| Custom Sections | By Group | Custom Sections | Remembers custom sections structure |

**Discussion points for grooming:**
1. What are the trade-offs of implementing independent remembered state for By Group and By Subcategory?
2. Is this feasible within the current data model, or does it require significant changes?
3. If too complex, fallback could be: switching away from a reordered auto-organized view resets to alphabetical (with a warning)

**Item order within sections:**

In addition to section order, should item order within sections be preserved when switching views?

- If user reorders items within the Kitchen section in By Group view, then switches to Flat List and back to By Group, is that item order preserved?
- Ideal: Yes, preserve item order within sections
- Fallback: Reset to default order (e.g., by date added) with a warning when switching views if items were reordered

This decision should align with the section order decision - if we preserve section order, we should also preserve item order within sections. If we reset section order, we should also reset item order.

---

## Ticket 2: Add Items By Experience

### Summary
A new component for adding items in bulk. **Only visible in By Group and By Subcategory views** (not in Flat List or Custom Sections).

### Acceptance Criteria

**UI Components:**
- [ ] "Add items" dropdown to select method: "One item at a time", "All items in Group", "All items in Subcategory"
- [ ] Default state: "One item at a time" pre-selected
- [ ] Secondary dropdown - label and contents change based on method selected:

| Method Selected | Dropdown Label | Dropdown Contents |
|-----------------|----------------|-------------------|
| One item at a time | "Select item:" | Searchable list of items not yet used in this order or any other order. Format: "{Item name} ({Group} - {Subcategory})" e.g., "Drywall (Kitchen - Labor)". Search matches item name, group, or subcategory. Shows first ~25 items by default; user types to search/filter for more (follows existing pagination behavior). |
| All items in Group | "Select group:" | List of groups that have available items, with counts (e.g., "Kitchen (7 items)"). Only shows groups with items not used in this or other orders. |
| All items in Subcategory | "Select subcategory:" | List of subcategories that have available items, with counts (e.g., "Material (8 items)"). Only shows subcategories with items not used in this or other orders. |

- [ ] Dropdown contents and counts update in real-time as items are added
- [ ] Groups/subcategories disappear from dropdown once all their items are added
- [ ] If no items available, second dropdown shows: "No items available" / "No groups available" / "No subcategories available" (based on method selected)
- [ ] Preview/status text area below the dropdowns
- [ ] "+ Add to Order" button
  - Disabled until a selection is made in the second dropdown
  - Disabled during loading state

**Add Flow (for "All items in Group" or "All items in Subcategory"):**
1. [ ] User selects add method from dropdown
2. [ ] User selects specific group/subcategory from secondary dropdown
3. [ ] **Preview state:** Text appears below dropdowns with arrow icon: "{X} {Group/Subcategory name} items will be added" (e.g., "8 Material items will be added")
4. [ ] User clicks "+ Add to Order"
5. [ ] **Loading state:** Arrow icon changes to loading spinner, text remains the same
6. [ ] Items are added and auto-organized into appropriate sections
7. [ ] **Success state:** Spinner changes to checkmark icon, text updates to "{X} {Group/Subcategory name} items added" (e.g., "8 Material items added")
8. [ ] Success state displays for 1500ms then resets, OR dismisses early on any interaction with the Add Items component
9. [ ] **After success:** Second dropdown resets to "Choose..." but first dropdown stays on current method (e.g., still "All items in Group")

**Add Flow (for "One item at a time"):**
1. [ ] User selects "One item at a time" from dropdown (or uses default)
2. [ ] User searches/selects specific item from secondary dropdown
3. [ ] **Preview state:** Text appears below dropdowns with arrow icon: "{Item name} will be added" (e.g., "Drywall will be added")
4. [ ] User clicks "+ Add to Order"
5. [ ] **Loading state:** Arrow icon changes to loading spinner, text remains the same
6. [ ] Item is added to the appropriate section based on its Group/Subcategory value
7. [ ] **Success state:** Spinner changes to checkmark icon, text updates to "{Item name} added" (e.g., "Drywall added")
8. [ ] Success state displays for 1500ms then resets, OR dismisses early on any interaction with the Add Items component
9. [ ] **After success:** Second dropdown resets to "Choose..." but first dropdown stays on "One item at a time"

**Error State:**
- [ ] If adding items fails, display inline error: "There was an error adding your items, please try again."
- [ ] Error displays in same location as preview/success text (red background, error icon)
- [ ] Error dismisses on any click inside the Add Items component or modal footer (Add to Order, Save, Cancel, dropdowns, view switcher)
- [ ] No auto-dismiss timeout - error persists until user interaction

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

## Ticket 3: Section Reordering Controls (Future - Low Priority)

### Summary
Add arrow controls and menu options for reordering sections, improving the experience when there are many sections (20+).

### Motivation
Drag-and-drop works well for 5-10 sections, but becomes tedious with large group counts. Arrow controls provide more precise positioning and better accessibility.

### Proposed UI
Each section header would include:
- **Up/Down arrows** - Move section one position up or down
- **Overflow menu (...)** - "Move to top" / "Move to bottom" options

### Scope
- Applies to all section-based views: Custom Sections, By Group, By Subcategory

### Priority
P2/P3 - Fast-follow after core auto-organize functionality proves value

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

### PDF Generation
Ensure the existing PDF generation continues to work correctly with all view modes:
- [ ] PDF reflects the correct section organization (Flat List, Custom Sections, By Group, By Subcategory)
- [ ] PDF reflects the correct section order (including any manual reordering)
- [ ] PDF reflects the correct item order within sections
- [ ] Section totals appear correctly when "Show section total" is enabled
- [ ] No changes to PDF generation logic should be needed - this is validation that existing functionality still works

---

## Design Assets

**Figma File:** [Link to be added]

**Flows Documented:**
1. Core Flow - View switching between all four modes
2. Adding Items in a Group - Bulk add by Group value
3. Adding Items in a Subcategory - Bulk add by Subcategory value
4. Empty State - By Group and By Subcategory with no items
5. Error State - Failed to add items
6. Add Items States - Preview (arrow icon), Loading (spinner), Success (checkmark)

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
