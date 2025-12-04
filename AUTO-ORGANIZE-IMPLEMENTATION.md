# Auto-Organize Orders Implementation

## Problem Statement

When creating work orders from estimates with 500+ line items, users currently face a cumbersome process:

1. Add items to an order one-by-one
2. Create custom sections manually (e.g., "Bathroom", "Kitchen")
3. Find items belonging to each section
4. Drag items into the appropriate sections individually

This results in **hundreds of clicks** to organize a single work order, especially when trying to communicate which room each piece of work is relevant to for subcontractors.

## Solution: Auto-Organization by Group/Subcategory

Instead of manual drag-and-drop organization, users can automatically group items by:
- **Group** (typically represents the room: Kitchen, Bathroom, Basement, etc.)
- **Subcategory** (typically Labor vs Material)

This reduces **500 clicks to 3 clicks** for organizing a work order.

## Implementation Scope

### Ticket 1: View Toggle Component

Replace the current flat list / custom sections toggle with a new view switcher supporting four modes:

| View | Description |
|------|-------------|
| **Flat List** | Simple table, no grouping (existing) |
| **Custom Sections** | User-created sections with drag-and-drop (existing) |
| **By Group** | Auto-creates sections based on item's Group field |
| **By Subcategory** | Auto-creates sections based on item's Subcategory field |

**Behavior for By Group / By Subcategory:**
- Automatically identifies unique values in the Group or Subcategory field
- Creates a section for each unique value
- Places items into their corresponding section
- Section names match the Group/Subcategory value

### Ticket 2: Add Items By Experience

A new component for adding items in bulk within the By Group and By Subcategory views:

**UI Components:**
- Dropdown to select method: "One item at a time", "All items in Group", "All items in Subcategory"
- Secondary dropdown that changes based on selection:
  - One item: Shows individual items
  - All in Group: Shows list of groups (e.g., "Kitchen (15 items)")
  - All in Subcategory: Shows list of subcategories (e.g., "Labor (50 items)")
- Preview showing how many items will be added
- Add button to execute

**Flow:**
1. User selects add method
2. User selects specific group/subcategory
3. System shows preview of items to be added
4. User confirms
5. Items are added and auto-organized into appropriate sections

## Integration Points

### Add to Order (from Items Table)
When adding items to an existing order that has a view mode set:
- Items should be added and automatically placed into the correct section based on the order's view mode
- If view is "By Group", items go into sections matching their Group field
- If view is "By Subcategory", items go into sections matching their Subcategory field

### View Experience
When viewing an order:
- Section names should accurately reflect the Group/Subcategory
- Section order should be maintained as configured

## Future Considerations

### Estimates
The same auto-organization functionality would be valuable for estimates. Implementation would be similar since estimates share the same table section component.

**Recommendation:** Validate value in orders first, then fast-follow with estimates.

### Table Editing Experience
Feedback indicates the current syncfusion grid editing experience is problematic:
- Slow with 500+ items
- Inline editing is finicky
- Drop-downs and popups behave inconsistently
- Scrolling/navigation is cumbersome

**Recommendation:** Spike to evaluate:
- Pain points across all workbook tables (estimates, orders, items)
- Whether syncfusion can be configured better
- Alternative approaches (side panel editing like Airtable)

## Primary User: Guardian Restoration Partners

Guardian uses Groups to identify rooms (Kitchen, Bathroom, Bedroom, etc.) so they can communicate to subcontractors exactly which room each piece of work belongs to. This is critical for:
- Large fire loss projects with 30-50 work orders
- Subcontractor clarity on scope
- Organizing 500+ line item estimates efficiently

## Success Metrics

- Reduction in time to create organized work orders
- Increase in work order adoption
- Guardian satisfaction with the workflow
