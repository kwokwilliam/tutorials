By default the browser lays out elements in lines going down the page. Each new block element starts a new line, so all the blocks are stacked on top of each other. This is fine for a document, but there are many times when you'll want to arrange these block elements to be side-by-side, in multiple columns on the same line. This is known as a **multi-column layout**, and until recently, it's been surprisingly hard to do.

In the bad old days of the web (90s and 2000s) many web developers turned to `<table>` elements to create multi-column layouts, but as many others pointed out, this was a horrible semantic misuse of that element. Although tables happen to create a grid-like layout, they are meant for rows of data, not random content you just want to layout side-by-side. Plug-ins that helped you import data from `<table>` elements into spreadsheets like Excel would mistakenly treat all multi-column layouts as tables of data!

The other common approach was to use [floated elements](../csslayout/#secfloatedelements). Each column in the layout was floated to the left and given a fixed width. The columns that followed then floated up next to their previous sibling columns. At the end of the row the floats were cleared so that the next block element would start a new line. It sounds straightforward in theory, but it was very difficult to make it work consistently across all browsers. Clearing the floats reliably also required some [really hacky tricks](https://css-tricks.com/snippets/css/clear-fix/).

Thankfully, a new standard was published in 2012 that makes multi-column layouts easy and reliable: [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/). It's now [widely supported in all the current browsers](https://caniuse.com/#feat=flexbox), but if you still need to support IE 9 or earlier, you'll have to use the older float technique.

## The Basics

Creating a multi-column flexbox layout is super-easy. Follow along by creating a new HTML page, and adding this content to the body, or by [opening this example over in CodePen](https://codepen.io/drstearns/pen/xLjmmb).

```html
<div class="row">
    <div class="col">
        <h2>First Column</h2>
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nostrum, nihil? Fugit corporis dignissimos earum magnam pariatur quos id ducimus dolor! Laboriosam tempora repudiandae amet dolorum assumenda. Nam, animi doloremque. Inventore!</p>
    </div>
    <div class="col">
        <h2>Second Column</h2>
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nostrum, nihil? Fugit corporis dignissimos earum magnam pariatur quos id ducimus dolor! Laboriosam tempora repudiandae amet dolorum assumenda. Nam, animi doloremque. Inventore!</p>
    </div>
    <div class="col">
        <h2>Third Column</h2>                
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nostrum, nihil? Fugit corporis dignissimos earum magnam pariatur quos id ducimus dolor! Laboriosam tempora repudiandae amet dolorum assumenda. Nam, animi doloremque. Inventore!</p>
    </div>
    <div class="col">
        <h2>Fourth Column</h2>                
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nostrum, nihil? Fugit corporis dignissimos earum magnam pariatur quos id ducimus dolor! Laboriosam tempora repudiandae amet dolorum assumenda. Nam, animi doloremque. Inventore!</p>
    </div>
</div>
```

<p><a href="https://codepen.io/drstearns/pen/xLjmmb?editors=1100" class="button is-primary">Open in CodePen</a></p>

When creating elements for a multi-column flexbox layout, add one element for the entire row, and one child element for each column in your layout. Here we also add a style class name to the row and column elements so that we can select them more easily in our CSS. The style class names we are using are typical, but there's nothing magic about those names: they are just names we can refer to in our CSS selectors. You can use any class name you want, or elements selectors if you use distinct element names instead.

Open the page, and note that the columns are all stacked on top of each other by default. To make these be side-by-side, add a stylesheet to your page, and define this one rule to make it multi-column:

```css
/* select all `class="row"` elements and make them flexboxes */
.row {
	display: flex;
}
```

Setting `display: flex;` on the `.row` element tells the browser to make it a flexbox. The immediate child elements will then become columns on the same line. 

By default the browser will adjust the width of these columns based on their content. In this case, all of the columns have the same text in them, so the browser grows their width equally, and then wraps the text within the column.

## Growing Columns

But what if the columns don't have much content? What if they only need a small amount of width each? To see what happens, remove the `<p>Lorem...</p>` elements from the HTML, or open [this already-altered example in CodePen](https://codepen.io/drstearns/pen/LjmqpR).

You'll notice that the columns are now only as wide as the headings, and are scrunched up against each other, taking up only part of the row width. This is because columns only grow in width to fit their content by default. If you want them to grow to consume an equal amount of the row width, regardless of their content, add another CSS rule to your stylesheet:

```css
.col {
	flex: 1;
}
```

<p><a href="https://codepen.io/drstearns/pen/LjmqpR?editors=1100" class="button is-primary">Open in CodePen</a></p>

This tells the browser to grow all of your `<div class="col">` elements equally. The `flex` property can adjust up to three settings at once: `flex-grow`, `flex-shrink`, and `flex-basis`. It's recommended that you use this shorthand property, especially when adjusting only one or two of those values, so that the browser can reset the properties you don't mention to a sensible default.

Here we are setting `flex` to only one number, so this sets `flex-grow` to `1`. The `flex-grow` property controls how much of the remaining row width the column should get relative to other columns. If all columns have a setting of `1`, they all grow by an equal amount, and thus end up exactly the same size.

Setting `flex` to just one number also defaults `flex-shrink` to `1` and `flex-basis` to `0`. The `flex-shrink` property controls how much a column will shrink in width relative to other columns when the row isn't long enough to hold all of the column content on one line (e.g., the example above). If all columns have a `flex-shrink` of `1`, they all shrink by the same amount, and thus end up the same width.

The `flex-basis` property sets the base size of the column, and here it is being set to `0`, which tells the browser to size it according to the `flex-grow` and `flex-shrink` settings regardless of the content within the column.

The number you assign to the `flex-grow` property establishes a ratio between the columns that determines how much of the remaining row width each column should get. In the example above, the `flex-grow` property on all of the columns is set to `1`, so they all get an equal amount of the remaining width. But you can adjust this on a per-column basis to give some columns more of the remaining width than others. For example, say you had a three-column layout where you wanted the middle column to get twice as much of the remaining row width as the outside columns. You'd start with HTML like this:

```html
<div class="row">
	<div class="col-left">...</div>
	<div class="col-middle">...</div>
	<div class="col-right">...</div>
</div>
```

And use CSS like this to set `flex` differently for the middle column:

```css
.row {
	display: flex;
}
.col-left, .col-right {
	flex: 1;
}
.col-middle {
	flex: 2;
}
```

<p><a href="https://codepen.io/drstearns/pen/ayKYRY" class="button is-primary">Open In CodePen</a></p>

Flexbox also makes it easy to grow some columns while letting others size only to match their content. For example, say you want to show an icon or two on the far right, but have the content in the first column grow to consume the rest of the available row width. Change your HTML content to this (requires [Font Awesome](http://fontawesome.io/) for the icon):

```html
<div class="row">
  <div class="col">
    <h2>This Column Grows</h2>
  </div>
  <div class="col-icons">
    <h2><span class="fa fa-home"></span></h2>
  </div>
</div>
```

<p><a href="https://codepen.io/drstearns/pen/xLjMRO" class="button is-primary">Open in CodePen</a></p>

Because we use `class="col-icons"` on the second column, that element will no longer be selected by our `.col` style rule, so it won't get the `flex: 1` property setting. The other column will, so it will grow to consume the rest of the available row width. This will continue to work even if we add more icons to the right side: that column will grow only as large as it needs to be to show its content, but the first column will grow to consume the remaining row width.

## Specifying Widths Explicitly

So far we've seen how to make columns just wide enough to fit their content, or grow to consume the available row width, but what if you want to specify an exact column width, like 20% or 300 pixels? You can do this using the `flex-basis` property.

### Box Sizing

When you start setting widths explicitly, it's a good idea to also tell the browser that you want the element's padding and borders to be included in the element's width. By default, browsers consider an element's width to be only the width of the content area: padding and borders are then added on top of that. So if you set the width of an element to be `300px` but then add `10px` of padding to the left and right sides, the overall width of the element will actually be `320px` by default.

To make the browser include padding and borders when setting the widths of all elements, use a rule like this:

```css
/* select all elements in the page */
* {
	/* include padding and borders when setting element widths */
	box-sizing: border-box;
}
```

### Flex Basis

Once you specify this, you can now set explicit widths on your columns. For example, say you want a two column layout where the first column (your navigation area) is exactly 200 pixels, and the second (the main content area) grows to fill the remaining row width. Start with HTML like this:

```html
<div class="row">
	<nav>
		<h2>Navigation Area</h2>
	</nav>
	<main>
		<h2>Main Content Area</h2>
	</main>
</div>
```

Then use this CSS in your stylesheet:

```css
* {
	box-sizing: border-box;
}
.row {
	display: flex;
}
nav {
	/* set column width to exactly 200 pixels with 1rem of padding */
	flex: 0 0 200px;
	padding: 1rem;
	background-color: #CFD8DC; /* just to highlight the area */
}
main {
	/* grow the main content area to consume the rest of the row width */
	flex: 1;
	padding: 1rem;
	background-color: #BBDEFB; /* just to highlight the area */
}
```

<p><a href="https://codepen.io/drstearns/pen/wqjOPa" class="button is-primary">Open in CodePen</a></p>

## Vertical Centering

Flexbox also solves another long-standing problem: vertically-centering content. A few years ago it became trendy to make the first block of content on your page the same height as the browser's viewport, with some splashy content right in the center. This was quite tricky before flexbox, but is now almost trivial.

Start with HTML like this:

```html
<section class="fullpage">
	<div class="content">
		<h2>Some Splashy Content</h2>
		<p>blah, blah, blah</p>
	</div>
</section>
<section>
	<p>Some more content you see when you scroll down...</p>
</section>
```

Then add some CSS to make the `.fullpage` element a flexbox that stretches to fill the entire viewport, and centers the `.content` element both vertically and horizontally:

```css
* {
	box-sizing: border-box
}
body {
	/* remove the default margin on the body */
	margin: 0;
	font-family: "Helvetica Neue", Helvetica, sans-serif;
}
section {
	padding: 1rem;
}
.fullpage {
	display: flex;
	/* vertically-center the child elements of the flexbox */
	align-items: center;
	/* horizontally-center the child elements as well */
	justify-content: center;
	/* make it 100% of the viewport height */
	height: 100vh;
	/* shade the background */
	background-color: #CCC;
	/* center align all text */
	text-align: center;
}
.fullpage .content h2 {
	font-weight: 100;
	font-size: 5rem;
}
```

<p><a href="https://codepen.io/drstearns/pen/zdjXGm" class="button is-primary">Open in CodePen</a></p>

When you open this page, you see only the first section consuming the entire browser viewport, regardless of how large the browser window is. Try resizing your window and you'll notice how the first section grows to fill it. When you scroll down, you then see the second section, and any other elements you might add after it.

## Nesting

The last thing to mention about flexboxes is that they can be nested. That is, a column within a flexbox can contain another flexbox with its own columns. This can be handy when you need to sub-divide a column.

If you define a single set of style classes for rows and columns, you can reuse them at several levels:

```html
<div class="row">
	<div class="col">
		<!-- nest another flexbox with 2 columns inside this column -->
		<div class="row">
			<div class="col">...</div>
			<div class="col">...</div>
		</div>
	</div>
	<div class="col">...</div>
	<div class="col">...</div>
</div>
```

If you need to style the nested flexbox differently from the parent flexbox, just add a different style class name to the nested flexbox:

```html
<div class="row">
	<div class="col">
		<!-- nest another flexbox with 2 columns inside this column -->
		<div class="row nested">
			<div class="col">...</div>
			<div class="col">...</div>
		</div>
	</div>
	<div class="col">...</div>
	<div class="col">...</div>
</div>
```

You can then use [descendant selectors](../css/#secdescendantselectors) to target just the columns within the nested flexbox:

```css
.row {
	/* style properties that apply to all rows */
}
.row .col {
	/* style properties that apply to all columns */
}
.row.nested .col {
	/* style properties that apply only to the columns
	inside the nested flexbox */
}
```

## More Information and Practice

THe Flexbox standard defines several other properties you can use to precisely control a multi-column layout. To learn more, check out these very helpful resources:

- [The Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- Test your Flexbox skills by playing [Flexbox Froggy!](http://flexboxfroggy.com/)

