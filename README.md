# Field Toggle Dynamic (Qlik Sense Extension)

Field Toggle Dynamic is a Qlik Sense visualization extension that creates **dynamic checkbox controls** for showing/hiding fields (columns) in Straight or Pivot tables using Qlik variables.

The extension accepts a **comma-separated configuration** directly from the Edit Sheet panel, where each entry can define:

```
Label|Variable
```

or simply:

```
Label
```

—in which case the label is also used as the variable name.

---

## ✨ Features

- Dynamic field list (comma-separated syntax)
- Automatic variable binding (1 = show, 0 = hide)
- Works with both Straight and Pivot tables
- Horizontal checkbox layout (flexbox)
- Default-on behavior for empty variables
- Lightweight and compatible with all Qlik Sense versions
- Clean, unobtrusive UI

---

## 📁 Folder Structure

```
field-toggle-dynamic/
   ├── field-toggle-dynamic.qext
   ├── field-toggle-dynamic.js
   └── preview.png   (optional)
```

---

## 🧩 Configuration

In the extension’s property panel you will find:

**Dynamic Fields / Columns**

Enter a comma-separated list:

```
Store|vShowField_Store, Group|vShowField_Group, Subgroup|vShowField_Subgroup
```

Or simple mode:

```
Store, Group, Subgroup
```

where variable = label.

### Example (full configuration)

```
Store|vShowField_Store,
Group|vShowField_Group,
Subgroup|vShowField_Subgroup,
Item|vShowField_Item,
Supplier|vShowField_Supplier,
Chain|vShowField_Chain
```

---

## 🧠 Show/Hide Columns in Straight or Pivot Tables

Set each column’s **Show Condition** to:

```
=$(vShowField_Store)
```

and so on.

### Switching between two tables (Straight / Pivot)

Use a variable, e.g. `vTableType`:

Straight table → **Show Condition**

```
=$(vTableType)
```

Pivot table → **Show Condition**

```
=Not $(vTableType)
```

Then toggle `vTableType` via this extension (if added as a field).

---

## 🖼️ Example UI

```
[✓] Store   [✓] Group   [ ] Subgroup   [✓] Item   [ ] Supplier   [✓] Chain
```

