+++
aliases = [ ]
authors = [ "callum"]
categories = [ "Eto" ]
description = "Some miscellaneous examples"
keywords = [ "rhino", "developer", "eto", "ui", "ux" ]
languages = [ "C#", "Python" ]
sdk = "eto"
title = "Eto Examples"
type = "guides"

[admin]
TODO = ""
origin = ""
picky_sisters = ""
state = ""

[included_in]
platforms = [ "Windows", "Mac" ]

+++


## Three Pronged Drop Down

A UI with 3 drop-downs, changing the choice in any drop down will change the available options to the right of it.

![Your First Data](/images/eto/examples/three-prong-drop-down.png)

<div class="codetab">
  <button class="tablinks7" onclick="openCodeTab(event, 'cs7')" id="defaultOpen7">C#</button>
  <button class="tablinks7" onclick="openCodeTab(event, 'py7')">Python</button>
</div>

<div class="tab-content">
  <div class="codetab-content7" id="cs7">

``` cs
using Rhino.UI;

using Eto.Drawing;
using Eto.Forms;

using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;

 
var dialog = new Dialog()
{
  Width = 300,
  Padding = 8
};

// One data source for simplicity. Dictionaries are always good.
var data = new Dictionary<int, int[]>()
{
  { 100, new int[] {110, 120, 130 } },

  { 110, new int[] { 111, 112, 113} },
  { 120, new int[] { 121, 122, 123} },
  { 130, new int[] { 131, 132, 133} },

  { 200, new int[] { 210, 220, 230} },

  { 210, new int[] { 211, 212, 213} },
  { 220, new int[] { 221, 222, 223} },
  { 230, new int[] { 231, 232, 233} },

  { 300, new int[] { 310, 320, 330} },

  { 310, new int[] { 311, 312, 313} },
  { 320, new int[] { 321, 322, 323} },
  { 330, new int[] { 331, 332, 333} },
};

// ObservableCollection will notify the UI of changes for us
var dd1_data = new ObservableCollection<object>() {
  100, 200, 300
};
var dd2_data = new ObservableCollection<object>();
var dd3_data = new ObservableCollection<object>();

var dd1 = new DropDown();
dd1.DataStore = dd1_data;

var dd2 = new DropDown();
dd2.DataStore = dd2_data;

var dd3 = new DropDown();
dd3.DataStore = dd3_data;

// Cache indexes to avoid unnecessary state updates which can be a bit crashy
int dd1Index = -1;
int dd2Index = -1;

bool busy = false;

void UpdateState(object sender, EventArgs args) 
{
  // Super safe busy check, prevents recursion!
  if (busy)
    return;
  
  busy = true;
  
  // Prevents crashing if there is no data
  if (dd1.SelectedIndex == -1)
    return;

  if (dd1.SelectedIndex != dd1Index)
  {
    var items = data[(int)dd1.SelectedValue];

    // Note we don't set a NEW value, we use the same collection
    dd2_data.Clear();
    foreach (var i in items)
      dd2_data.Add(i);

    dd2.SelectedIndex = 0;
    dd3.SelectedIndex = 0;
    dd2Index = 0;
  }

  // This should run on 1 or 2 changing index
  if (dd2.SelectedIndex != dd2Index || dd1.SelectedIndex != dd1Index)
  {
    var items = data[(int)dd2.SelectedValue];

    // Note we don't set a NEW value, we use the same collection
    dd3_data.Clear();
    foreach (var i in items)
      dd3_data.Add(i);

    dd3.SelectedIndex = 0;

  dd2Index = dd2.SelectedIndex;
  dd1Index = dd1.SelectedIndex;
  
  // We are no longer busy!
  busy = false;
  }
}


dd1.SelectedIndexChanged += UpdateState;
dd2.SelectedIndexChanged += UpdateState;
dd1.SelectedIndex = 0;
 
// Nice way to set up a UI with equal spacing
var dynamicLayout = new DynamicLayout();
dynamicLayout.Spacing = new Size(4, 4); // Bit cramped otherwise
dynamicLayout.BeginHorizontal();
dynamicLayout.Add(dd1, true);
dynamicLayout.Add(dd2, true);
dynamicLayout.Add(dd3, true);
dynamicLayout.EndHorizontal();

dialog.Content = dynamicLayout;

var parent = RhinoEtoApp.MainWindowForDocument(__rhino_doc__);
dialog.ShowModal(parent);
```

</div>

  <div class="codetab-content7" id="py7">

``` py
# Imports
import scriptcontext as sc
 
import Rhino
from Rhino.UI import RhinoEtoApp, EtoExtensions
 
import Eto.Drawing as ed
import Eto.Forms as ef

from System.Collections.ObjectModel import ObservableCollection

 
dialog = ef.Dialog()
dialog.Width = 300
dialog.Padding = ed.Padding(8)

# One data source for simplicity. Dictionaries are always good.
data = {
  100: [110, 120, 130],

  110: [111, 112, 113],
  120: [121, 122, 123],
  130: [131, 132, 133],

  200: [210, 220, 230],

  210: [211, 212, 213],
  220: [221, 222, 223],
  230: [231, 232, 233],

  300: [310, 320, 330],

  310: [311, 312, 313],
  320: [321, 322, 323],
  330: [331, 332, 333],
}

# ObservableCollection will notify the UI of changes for us
dd1_data = ObservableCollection[object]()
dd1_data.Add(100)
dd1_data.Add(200)
dd1_data.Add(300)
dd2_data = ObservableCollection[object]()
dd3_data = ObservableCollection[object]()

dd1 = ef.DropDown()
dd1.DataStore = dd1_data

dd2 = ef.DropDown()
dd2.DataStore = dd2_data

dd3 = ef.DropDown()
dd3.DataStore = dd3_data

# Cache indexes to avoid unnecessary state updates which can be a bit crashy
dialog.dd1_index = -1
dialog.dd2_index = -1

dialog.busy = False
def update_state(sender, args):
  # Super safe busy check, prevents recursion!
  if (dialog.busy):
    return
  
  dialog.busy = True
  
  # Prevents crashing if there is no data
  if dd1.SelectedIndex == -1:
    return

  if (dd1.SelectedIndex != dialog.dd1_index):

    items = data[dd1.SelectedValue]

    # Note we don't set a NEW value, we use the same collection
    dd2_data.Clear()
    for i in items:
      dd2_data.Add(i)
    dd2.SelectedIndex = 0
    dd3.SelectedIndex = 0
    dialog.dd2_index = 0

  # This should run on 1 or 2 changing index
  if (dd2.SelectedIndex != dialog.dd2_index or
    dd1.SelectedIndex != dialog.dd1_index):
    items = data[dd2.SelectedValue]

    # Note we don't set a NEW value, we use the same collection
    dd3_data.Clear()
    for i in items:
      dd3_data.Add(i)
    dd3.SelectedIndex = 0

  dialog.dd2_index = dd2.SelectedIndex
  dialog.dd1_index = dd1.SelectedIndex
  
  # We are no longer busy!
  dialog.busy = False

dd1.SelectedIndexChanged += update_state
dd2.SelectedIndexChanged += update_state
dd1.SelectedIndex = 0
 
# Nice way to set up a UI with equal spacing
dynamic = ef.DynamicLayout()
dynamic.Spacing = ed.Size(4, 4) # Bit cramped otherwise
dynamic.BeginHorizontal()
dynamic.Add(dd1, True)
dynamic.Add(dd2, True)
dynamic.Add(dd3, True)
dynamic.EndHorizontal()

dialog.Content = dynamic

parent = RhinoEtoApp.MainWindowForDocument(sc.doc)
dialog.ShowModal(parent)
```

  </div>
</div>

## Convert nested list to Eto Table

![List to Table](/images/eto/examples/list-to-eto-table.png)

<div class="codetab">
  <button class="tablinks2" onclick="openCodeTab(event, 'cs2')" id="defaultOpen2">C#</button>
  <button class="tablinks2" onclick="openCodeTab(event, 'py2')">Python</button>
</div>

<div class="tab-content">
  <div class="codetab-content2" id="cs2">

``` cs
using System;
using System.Collections.Generic;

using Eto.Forms;
using Eto.Drawing;
 
public class EtoTable
{
  public static TableLayout ConvertToTable(List<List<string>> nestedList, bool ExpandTable = true)
  {
    var table = new TableLayout
    {
      Spacing = new Size(1, 1),
    };

    bool first = true;
    bool odd = true;
    foreach (var row in nestedList)
    {
      var tableRow = new TableRow();

      foreach (var cell in row)
      {
        var label = new Label
        {
          Text = cell,
          VerticalAlignment = VerticalAlignment.Center,
          TextColor = Colors.Black
        };

        var container = new Panel()
        {
          Padding = new Padding(5,2),
          BackgroundColor = first? Colors.BlanchedAlmond: odd? Colors.White: Colors.FloralWhite,
          Content = label,
        };

        tableRow.Cells.Add(new TableCell(container, ExpandTable? true: false));
      }
      
      first = false;
      odd = !odd;

      if (!ExpandTable)
        tableRow.Cells.Add(null);

      table.Rows.Add(tableRow);
    }

    table.Rows.Add(null);

    return table;
  }
}
 
var nestedList = new List<List<string>>
{
    new List<string> { "ID", "Name",   "Age" },
    new List<string> { "1",  "Alice",  "25" },
    new List<string> { "2",  "Bob",    "30" },
    new List<string> { "3",  "Charlie", "22" },
    new List<string> { "4",  "Dan",     "44" },
    new List<string> { "5",  "Edward",  "65" },
    new List<string> { "6",  "Frederic", "9" },
    new List<string> { "7",  "Gina",    "17" },
};

var dialog = new Dialog()
{
    Padding = 10,
    Title = "Eto Table Example",
    Content = EtoTable.ConvertToTable(nestedList, true)
};

dialog.ShowModal();
```

  </div>
  <div class="codetab-content2" id="py2">

``` py
import Rhino
import Eto.Forms as forms
import Eto.Drawing as drawing

class EtoTable(forms.FloatingForm):
  def __init__(self):
    super(EtoTable, self).__init__()
    self.Title = "Eto Table Example"
    self.Padding = drawing.Padding(10)
    
    nested_list = [
      ["ID", "Name",   "Age"],
      ["1",  "Alice",   "25"],
      ["2",  "Bob",     "30"],
      ["3",  "Charlie",  "22"],
      ["4",  "Dan",     "44"],
      ["5",  "Edward",  "65"],
      ["6",  "Frederic", "9"],
      ["7",  "Gina",    "17"],
    ]
    
    self.Content = self.convert_to_table(nested_list, True)

  def convert_to_table(self, nested_list, expand_table=True):
    table = forms.TableLayout()
    table.Spacing = drawing.Size(1, 1)
    
    first = True
    odd = True
    
    for row in nested_list:
      table_row = forms.TableRow()
      for cell in row:
        label = forms.Label()
        label.Text = cell
        label.VerticalAlignment = forms.VerticalAlignment.Center
        
        container = forms.Panel()
        container.Padding = drawing.Padding(5, 2)
        
        if first:
          container.BackgroundColor = drawing.Colors.BlanchedAlmond
        elif odd:
          container.BackgroundColor = drawing.Colors.White
        else:
          container.BackgroundColor = drawing.Colors.FloralWhite
        
        container.Content = label
        table_row.Cells.Add(forms.TableCell(container, expand_table))
      
      first = False
      odd = not odd
      
      if not expand_table:
        table_row.Cells.Add(None)
      
      table.Rows.Add(table_row)
    
    table.Rows.Add(None)
    return table

form = EtoTable()
form.Show()
```

</div>
</div>
