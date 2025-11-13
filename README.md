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
## 🛠️ Tech Notes

- Built with the standard `qlik` and `enigmaModel` APIs.  
- No external dependencies.  
- Compatible with Qlik Sense Desktop and Qlik Sense Enterprise.  
- Tested with Qlik Sense June 2025 release.

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

## 📜 Full JavaScript Source (`field-toggle-dynamic.js`)

```javascript
define([
  'qlik',
  'jquery'
], function (qlik, $) {
  'use strict';

  return {
    definition: {
      type: "items",
      component: "accordion",
      items: {
        fieldsGroup: {
          label: "Dynamic Πεδία / Στήλες",
          type: "items",
          items: {
            fieldsCsv: {
              ref: "props.fieldsCsv",
              label: "Λίστα πεδίων (Label|Variable, comma-separated)",
              type: "string",
              defaultValue:
                "ΚΑΤΑΣΤΗΜΑ|vShowField_KATASTHMA, " +
                "ΟΜΑΔΑ|vShowField_OMADA, " +
                "ΥΠΟΟΜΑΔΑ|vShowField_YPOOMADA, " +
                "ΕΙΔΟΣ|vShowField_EIDOS, " +
                "ΠΡΟΜΗΘΕΥΤΗΣ|vShowField_PROMHTHEFTHS, " +
                "ΑΛΥΣΙΔΑ|vShowField_ALYSIDA"
            }
          }
        }
      }
    },

    paint: function ($element, layout) {
      var app = qlik.currApp(this);

      var raw = (layout.props.fieldsCsv || "").trim();
      var fieldsConfig = [];

      if (raw.length > 0) {
        raw.split(",").forEach(function (item) {
          var token = item.trim();
          if (!token) return;

          var parts = token.split("|");
          var label = parts[0].trim();
          var varName = (parts[1] ? parts[1] : parts[0]).trim();

          if (label && varName) {
            fieldsConfig.push({ label: label, varRef: varName });
          }
        });
      }

      if (fieldsConfig.length === 0) {
        $element.html("<div style='padding:6px;font-size:12px;'>Δεν έχουν οριστεί πεδία.</div>");
        return qlik.Promise.resolve();
      }

      var html = `
        <style>
          .ftd-row {
            display: flex;
            flex-direction: row;
            align-items: center;
            gap: 16px;
            flex-wrap: wrap;
            padding: 6px;
          }
          .ftd-item {
            display: flex;
            flex-direction: row;
            align-items: center;
            font-size: 14px;
            white-space: nowrap;
            margin-top: -10px;
          }
          .ftd-item input {
            margin-right: 4px;
            margin-top: -4px;
          }
        </style>
        <div class="ftd-row">
      `;

      fieldsConfig.forEach(function (f) {
        html += `
          <div class="ftd-item">
            <input type="checkbox" class="ftd-chk" data-var="${f.varRef}">
            ${f.label}
          </div>
        `;
      });

      html += `</div>`;

      $element.html(html);

      $element.find(".ftd-chk").off("change");

      fieldsConfig.forEach(function (f) {
        app.variable.getByName(f.varRef).then(function (v) {
          v.getLayout().then(function (vlayout) {
            var currentValue = (vlayout.qText || "").trim();
            if (currentValue === "") currentValue = "1";

            $element
              .find('input.ftd-chk[data-var="' + f.varRef + '"]')
              .prop("checked", currentValue === "1");
          });
        });
      });

      $element.find(".ftd-chk").on("change", function () {
        var varName = $(this).data("var");
        var val = $(this).is(":checked") ? "1" : "0";
        if (varName) app.variable.setStringValue(varName, val);
      });

      return qlik.Promise.resolve();
    }
  };
});

```

## 🖼️ Example UI

```
[✓] Store   [✓] Group   [ ] Subgroup   [✓] Item   [ ] Supplier   [✓] Chain
```

