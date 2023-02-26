# Spanish NLP

## Introduction

Spanish NLP is the first low code Python library for Natural Language Processing in Spanish. It provides three main modules:

* **Preprocess**: it offers several text preprocessing options to clean and prepare texts for further analysis.
* **Classify**: it allows users to quickly classify texts using different pre-trained models
* **Augmentation**: it allows generate synthetic data. It is useful for increasing labeled data and improving results in classification model training.

## Installation

Spanish NLP can be installed via pip:

```bash
pip install spanish_nlp
```

## Usage

### Preprocessing

See more information in the [Jupyter Notebook example](https://github.com/jorgeortizfuentes/spanish_nlp/blob/main/examples/Preprocess.ipynb)


To preprocess text using the preprocess module, you can import it and call the desired parameters:

```python
from spanish_nlp import preprocess
sp = preprocess.SpanishPreprocess(
        lower=False,
        remove_url=True,
        remove_hashtags=False,
        split_hashtags=True,
        normalize_breaklines=True,
        remove_emoticons=False,
        remove_emojis=False,
        convert_emoticons=False,
        convert_emojis=False,
        normalize_inclusive_language=True,
        reduce_spam=True,
        remove_vowels_accents=True,
        remove_multiple_spaces=True,
        remove_punctuation=True,
        remove_unprintable=True,
        remove_numbers=True,
        remove_stopwords=False,
        stopwords_list=None,
        lemmatize=False,
        stem=False,
        remove_html_tags=True,
)

test_text = """𝓣𝓮𝔁𝓽𝓸 𝓭𝓮 𝓹𝓻𝓾𝓮𝓫𝓪

<b>Holaaaaaaaa a todxs </b>, este es un texto de prueba :) a continuación les mostraré un poema de Roberto Bolaño llamado "Los perros románticos" 🤭👀😅

https://www.poesi.as/rb9301.htm

¡Me gustan los pingüinos! Sí, los PINGÜINOS 🐧🐧🐧 🐧 #VivanLosPinguinos #SíSeñor #PinguinosDelMundoUníos #ÑanduesDelMundoTambién

Si colaboras con este repositorio te puedes ganar $100.000 (en dinero falso). O tal vez 20 pingüinos. Mi teléfono es +561212121212"""

print(sp.transform(test_text, debug=False))
```

Output:
```bash
hola a todos este es un texto de prueba:) a continuacion los mostrare un poema de roberto bolaño llamado los perros romanticos 🤭 👀 😅 
me gustan los pinguinos si los pinguinos 🐧 🐧 🐧 🐧 vivan los pinguinos si señor pinguinos del mundo unios ñandues del mundo tambien
si colaboras con este repositorio te puedes ganar en dinero falso o tal vez pinguinos mi telefono es
```
### Classification

See more information in the [Jupyter Notebook example](https://github.com/jorgeortizfuentes/spanish_nlp/blob/main/examples/Classify.ipynb)
#### Available classifiers

* Hate Speech (hate_speech)
* Toxic Speech (toxic_speech)
* Sentiment Analysis (sentiment_analysis)
* Emotion Analysis (emotion_analysis)
* Irony Analysis (irony_analysis)
* Sexist Analysis (sexist_analysis)
* Racism Analysis (racism_analysis)

#### Classification Example

```python
from spanish_nlp import classifiers

sc = classifiers.SpanishClassifier(model_name="hate_speech", device='cpu')
# DISCLAIMER: The following message is merely an example of hate speech and does not represent the views of the author or contributors.
t1 =  "LAS MUJERES Y GAYS DEBERIAN SER EXTERMINADOS"
t2 = "El presidente convocó a una reunión a los representantes de los partidos políticos"
p1 = sc.predict(t1)
p2 = sc.predict(t2)

print("Text 1: ", t1)
print("Prediction 1: ", p1)
print("Text 2: ", t2)
print("Prediction 2: ", p2)
```

Output:

```bash
Text 1:  LAS MUJERES Y GAYS DEBERÍAN SER EXTERMINADOS
Prediction 1:  {'hate_speech': 0.7544152736663818, 'not_hate_speech': 0.24558477103710175}
Text 2:  El presidente convocó a una reunión a los representantes de los partidos políticos
Prediction 2:  {'not_hate_speech': 0.9793208837509155, 'hate_speech': 0.02067909575998783}
```

### Augmentation

See more information in the [Jupyter Notebook example](https://github.com/jorgeortizfuentes/spanish_nlp/blob/main/examples/Data%20Augmentation.ipynb)

#### Available Augmentation Models

- Spelling augmentation
  - Keyboard method
  - OCR method
  - Random method
  - Orthography method
  - All method
- Masked augmentation
  - Sustitute method
  - Insert method
- Others models under development (such as Synonyms, WordEmbeddings, GenerativeOpenSource, GenerativeOpenAI, BackTranslation, AbstractiveSummarization)


#### Augmentation Models Examples

```python
from spanish_nlp import augmentation

ocr = augmentation.Spelling(method="ocr", 
                            stopwords="default", 
                            aug_percent=0.3, 
                            tokenizer="default")

keyboard = augmentation.Spelling(method="keyboard", 
                                    stopwords="default", 
                                    aug_percent=0.3, 
                                    tokenizer="default")

orthography = augmentation.Spelling(method="orthography", 
                                    stopwords="default", 
                                    aug_percent=0.3, 
                                    tokenizer="default")

masked_sustitute = augmentation.Masked(method="sustitute", 
                                       model="dccuchile/bert-base-spanish-wwm-cased",
                                       tokenizer="default",
                                       stopwords="default",
                                       aug_percent=0.4,
                                       device="cpu",
                                       top_k=10)

masked_insert = augmentation.Masked(method="insert", 
                                    model="dccuchile/bert-base-spanish-wwm-cased",
                                    tokenizer="default",
                                    stopwords="default",
                                    aug_percent=0.4,
                                    device="cpu",
                                    top_k=10)

text = "En aquel tiempo yo tenía veinte años y estaba loco. Había perdido un país pero había ganado un sueño. Y si tenía ese sueño lo demás no importaba. Ni trabajar ni rezar ni estudiar en la madrugada junto a los perros románticos."

new_texts = [text]
new_texts.append(ocr.augment(text, num_samples=1, num_workers=1))
new_texts.append(keyboard.augment(text, num_samples=1, num_workers=1))
new_texts.append(orthography.augment(text, num_samples=1, num_workers=1))
new_texts.append(masked_sustitute.augment(text, num_samples=1))
new_texts.append(masked_insert.augment(text, num_samples=1))

for t in new_texts:
    print(t)
    print("---")
```

Output:

```bash
En aquel tiempo yo tenía veinte años y estaba loco. Había perdido un país pero había ganado un sueño. Y si tenía ese sueño lo demás no importaba. Ni trabajar ni rezar ni estudiar en la madrugada junto a los perros románticos.
---
['En a9uel tleWpo Vo t3nía velnt3 aNos y es7aba locQ. Ha8ía pePdido Vn paí8 pePQ había ganad0 un sueñQ. Y si 73nía 35e sueño lo d3más no lmRoPtaba. Hi tra8aLar nl P3zar nl 3stuOiar 3n la ma0rugada Lun7Q a los perr0s roWántiGQs.']
---
['En zquel tuempl yo henía veinte aloe y ezhavs lkdo. Havía lerwido ub oqís pfgo había gwnzxo jj susoo. Y si trníx ese sueño lp wemás no importaba. Ni tragajar ji reaaf no esguriat en la ksetuhada uunto a pke perrls dományicos.']
---
['En aquel tiempo yo tenía beinte años y estaba loco. Había perdido um país pero  abía gamado um sueño. Y si temía ese sueño lo denás no importava. Ni trabajar ni rezar ni estudiar en la madrujada junto a los perros románticos.']
---
['En aquel tiempo yo tenía 16 años y estaba. Ven roto un país pero había ganado un sueño. Y si tenía ese sueño lo demás no importaba. Ni trabajar ni rezar ni estudiar en la madrugada junto a los perros románticos.!']
---
['En aquel tiempo yo tenía veintesieteés años y estaba muy loco. Habían perdido un país pero había ganado un sueño. Y si tenía ese sueño lo demás no importaba. Ni trabajar ni rezar ni estudiar en la madrugada junto a los viejos viejos perros. románticos.']
---
```

## License

Spanish NLP is licensed under the [GNU General Public License v3.0](https://github.com/jorgeortizfuentes/spanish_nlp/blob/main/LICENSE).

## Author

This project was developed by [Jorge Ortiz-Fuentes](https://ortizfuentes.com/), Linguist and Data Scientist from Chile.

## Acknowledgements

We would like to express our gratitude to the Millennium Institute For Foundational Research and Department of Computer Science at the University of Chile for supporting the development of Spanish NLP. Special thanks to Felipe Bravo-Marquéz, Ricardo Cordova and Hernán Sarmiento for their knowledge, support and invaluable contribution to the project.

## Contributing

Contributions to Spanish NLP are welcome! Please see the [contributing guide](https://github.com/users/jorgeortizfuentes/projects/1) for more information.