# Question and Answer

Use a pre-trained model to answer questions based on the content of a given passage.

### How it works
The model can be used to build a system that can answer users’ questions in natural language. It was created using a pre-trained BERT model fine-tuned on SQuAD 1.1 dataset.

(BERT)[https://github.com/google-research/bert], or Bidirectional Encoder Representations from Transformers, is a method of pre-training language representations which obtains state-of-the-art results on a wide array of Natural Language Processing tasks.

This app uses a compressed version of BERT, MobileBERT, that runs 4x faster and has 4x smaller model size.

(SQuAD)[https://rajpurkar.github.io/SQuAD-explorer/], or Stanford Question Answering Dataset, is a reading comprehension dataset consisting of articles from Wikipedia and a set of question-answer pairs for each article.

The model takes a passage and a question as input, then returns a segment of the passage that most likely answers the question. It requires semi-complex pre-processing including tokenization and post-processing steps that are described in the BERT (paper)[https://arxiv.org/abs/1810.04805] and implemented in the sample app.

## Usage

There are two main ways to get this model in your JavaScript project: via script tags or by installing it from NPM and using a build tool like Parcel, WebPack, or Rollup.

### via Script Tag

```html
<!-- Load TensorFlow.js. This is required to use qna model. -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"> </script>
<!-- Load the qna model. -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/qna"> </script>

<!-- Place your code in the script tag below. You can also use an external .js file -->
<script>
  // Notice there is no 'import' statement. 'qna' and 'tf' is
  // available on the index-page because of the script tag above.

  // Load the model.
  qna.load().then(model => {
    // detect objects in the image.
    model.findAnswers(question, passage).then(answers => {
      console.log('Answers: ', answers);
    });
  });
</script>
```

### via NPM

```js
// Note: you do not need to import @tensorflow/tfjs here.

import * as qna from '@tensorflow-models/qna';

// Load the model.
const model = await qna.load();

// Classify the image.
const answers = await model.findAnswers(question, passage);

console.log('Answers: ');
console.log(answers);
```

You can also take a look at the [demo app](./demo).

## API

#### Loading the model
`qna` is the module name, which is automatically included when you use the `<script src>` method. When using ES6 imports, `qna` is the module.

```js
// you can load the model without providing the config object.
model = await qna.load();
// or you can specify the model url.
config = {modelUrl: 'https://yourown-server/qna/model.json'};
customModel = await qna.load(config);
```

Args:
**config** Model Config structure with following attributes:
 - **modelUrl:** An optional string that specifies custom url of the model. This is useful for area/countries that don't have access to the model hosted on GCP.

Returns a `model` object.

#### Find the answers

You can find the answers give question and associated passage with the model without needing to create a Tensor.
`model.findAnswers` takes two inputs (question and passage) and returns an array of answers ranked by their scores.

This method exists on the model that is loaded from `qna.load`.

```js
model.findAnswers(
  question: string, passage: string
)
```

Args:

- **question:** The question string.
- **passage:** The content to extract answers from.

Returns an Promise of array of answers that look like following:

```js
[{
  text: "Sundar Pichai",
  score: 0.8380282521247864
}]
```

In which the text is of string type and represents the answer body, and score is a number, indicates the confident level.

Here is an example run of the QnA model:
```js
const passage = "Google LLC is an American multinational technology company that specializes in Internet-related services and products, which include online advertising technologies, search engine, cloud computing, software, and hardware. It is considered one of the Big Four technology companies, alongside Amazon, Apple, and Facebook. Google was founded in September 1998 by Larry Page and Sergey Brin while they were Ph.D. students at Stanford University in California. Together they own about 14 percent of its shares and control 56 percent of the stockholder voting power through supervoting stock. They incorporated Google as a California privately held company on September 4, 1998, in California. Google was then reincorporated in Delaware on October 22, 2002. An initial public offering (IPO) took place on August 19, 2004, and Google moved to its headquarters in Mountain View, California, nicknamed the Googleplex. In August 2015, Google announced plans to reorganize its various interests as a conglomerate called Alphabet Inc. Google is Alphabet's leading subsidiary and will continue to be the umbrella company for Alphabet's Internet interests. Sundar Pichai was appointed CEO of Google, replacing Larry Page who became the CEO of Alphabet."
const question = "Who is the CEO of Google?"

const answers = await model.findAnswers(question, passage);
console.log(answers);
/**
[{
  text: "Sundar Pichai",
  score: 0.8380282521247864
}]
**/
```