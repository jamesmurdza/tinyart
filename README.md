# Tiny Art

For this project we experimenting with fine-tuning LLMs (Both Llama 2 7B and GPT-3.5-Turbo) to generate small one-line text art. (For example, a cuddly bear: `ʕっ•ᴥ•ʔっ`).

## Data scraping

We scraped [cooltext.top](https://cooltext.top), using some very simple JavaScript code:

```javascript
// Get all label elements
const names = [...document.querySelectorAll(".name")].map(item => item.innerText);
// Get all art elements
const objects = [...document.querySelectorAll(".object")].map(item => item.innerText);
// Combine into a spreadsheet format
const combinedString = names.map((name, index) => `${name}\t${objects[index]}`).join("\n");
copy(combinedString)
```

The method for finding these selectors is [here](https://gist.github.com/jamesmurdza/dc4835719af03b3754aad51c37a4414c). After running this code on a page, the results can be pasted into a spreadsheet.

## Data preprocessing

Through trial-and-error, we realized the need to escape all non-ASCII characters when calling fine-tuning APIs. Therefore, we escaped all characters in the art training set:

```javascript
function stringToUnicode(input) {
  var output = "";
  for (var i = 0; i < input.length; i++) {
    var hexCode = input.charCodeAt(i).toString(16);
    while (hexCode.length < 4) {
      hexCode = "0" + hexCode;
    }
    output += "\\u" + hexCode;
  }
  return output;
}
```

## Fine-tuning LLama 2

We fine-tuned Llama 2 7B using Gradient.AI ([Docs](https://docs.gradient.ai/docs/cli-quickstart)).

The training data format was a JSONL file where each line looks like:
```json
{ "inputs": "### Instruction: Make an ASCII art about Cloud and moon\n\n### Response: \u22c6\u207a\u208a\u22c6\u0020\u263e\u0020\u22c6\u207a\u208a\u22c6\u0020\u2601\ufe0e" }
```

## Fine-tuning GPT-3.5-Turbo

We fine-tuned GPT-3.5-Turbo using the OpenAI API.
