# ARcore-measurement

forked form the [legend](https://medium.com/@shibuiyusuke/measuring-distance-with-arcore-6eb15bf38a8f)

## Faced Errors

### java runtime jdr

the solution from [here](https://stackoverflow.com/questions/66449161/how-to-upgrade-an-android-project-to-java-11)

## New stuff with Android Studio

### 1. [Spinner and Array Adapter](https://www.geeksforgeeks.org/spinner-in-android-using-java-with-example/)

Spinner is a view similar to the dropdown list which is used to select one option from the list of options.
It provides an easy way to select one item from the list of items and it shows a dropdown list of all values when we click on it.
The default value of the android spinner will be the currently selected value and by using Adapter we can easily bind the items to the spinner objects.
Generally, we populate our Spinner control with a list of items by using an ArrayAdapter in our Kotlin file.

The Adapter acts as a bridge between the UI Component and the Data Source.
It converts data from the data sources into view items that can be displayed into the UI Component.
Data Source can be Arrays, HashMap, Database, etc. and UI Components can be ListView, GridView, Spinner, etc.
ArrayAdapter is the most commonly used adapter in android.
When you have a list of single type items which are stored in an array you can use ArrayAdapter.
Likewise, if you have a list of phone numbers, names, or cities. ArrayAdapter has a layout with a single TextView.

[`onItemSelectedListener`](https://developer.android.com/reference/kotlin/android/widget/AdapterView.OnItemSelectedListener)

### 2. TableLayout and TableLayout.LayoutParams
