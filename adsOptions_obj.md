### adsOptions defining

For setting custom styles to text ad elements, can one pass following params:

* `headline` object sets styles for Headline text.
* `description` object sets styles for Description text.
* `brandName` object sets styles for Brand Name (domain name) text.
* `actionText` object sets styles for Action Text button.
* `background` field sets basic background for ad block.
* `secondBackground` field sets background for `brandName` and `actionText` blocks.

#### Example:

``` javascript
{
    type: 'text', // mandatory field
    width: 320, // optional for text ad
    height: 128, // optional for text ad
    headline: { // optional
        fontSize: 16,
        lineHeight: 20,
        textTransform: 'uppercase',
        textDecoration: 'underline',
        color: '#000'
    },
    description: { // optional
        fontSize: 13,
        lineHeight: 20,
        textTransform: 'lowercase',
        textDecoration: 'underline',
        color: '#999'
    },
    brandName: { // optional
        fontSize: 13,
        textTransform: 'uppercase',
        textDecoration: 'underline',
        color: '#999'
    },
    actionText: { // optional
        fontSize: 13,
        textTransform: 'uppercase',
        textDecoration: 'underline',
        color: '#fff',
        background: '#336699',
        border: '1px solid #fff',
        borderRadius: 16,
        padding: 16
    },
    background: '#eee',
    secondBackground: '#ccc'
}
```

`background` options accepts strings, so it can take values like:

* `#212121`
* `rgb(33,33,18)`
* `rgba(33,33,18,0.3)`
* `url(URL of some remote image or assets)`
