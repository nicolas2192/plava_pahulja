# Data Project README file

The README file describes the essence of the project playing the most important role. Most visitors will simply scroll down about twice on the README and leave if they are not interested. So, the README file should provide the reason **why** to checkout your project!!!). 
Bearing that in mind, your job is to: 
- Tell them what it is (with context).
- Show them what it looks like in action.
- Show them how they use it.
- Tell them any other relevant details.

![Image](https://res.cloudinary.com/springboard-images/image/upload/q_auto,f_auto,fl_lossy/wordpress/2019/05/aiexcerpt.png)

---

## Formatting
Your readers will most likely view your README in a browser so please keep that in mind when formatting its content: 
- Use proper format when necesary (e.g.: `import pandas as pd`). 
- *Categorize content using two or three levels of header beneath.*
- Make use of **emphasis** to call out important words. 
- Link to project pages for related libraries you mention. Link to Wikipedia, Wiktionary, even Urban Dictionary definitions for words of which a reader may not be familiar. Make amusing cultural references. 
- Add links to related projects or services. 

> Here you have a markdown cheatsheet [Link](https://commonmark.org/help/) and tutorial [Link](https://commonmark.org/help/tutorial/).


## Jupyter

Run the following commands in the jupyter environment:

Jupyter Notebook:
```
conda install -c conda-forge notebook
conda install -c conda-forge nb_conda_kernels
```

Jupyter Lab
```
conda install -c conda-forge jupyterlab
conda install -c conda-forge nb_conda_kernels
```

Then run the following command in all other environments to be able to access them from jupyter interface:
```
conda install -c anaconda ipykernel
```

## Poetry
`pip install poetry` Install poetry
`poetry show` List all libraries installed according to pyproject.toml file.
`poetry add library@version` Install a library.
`poetry remove library` Remove a library.
`poetry install --no-root` Start installing libraries when there is no .

Use @ to reference a specific version, or ^ for the latest minor version.

## UV
`pip install uv` Install uv
`uv init` Initialize the git repo.
`uv run 'file.py'` Create an environment according to the .toml file and runs the script.
`uv add 'library'` Install a library.
`uv remove 'library'` Remove a library.
`uv sync` Sync the environment with what it has in the .toml file.
`uv tree'` Visual representation of what relies on what.
`uv venv new_environment` Visual representation of what relies on what, add no name for default .venv

## venv
`python3 -m venv virtualenvname` Create a new python environment.

## Suggested Structure:

### :raising_hand: Name
Self-explanatory names are best. If the name sounds too vague or unrelated, it may be a signal to move on. It also must be catchy. Images, Logo, Gif or some color is strongly recommended.

### :baby: Status
Alpha, Beta, 1.1, Ironhack Data Analytics Final Project, etc... It's OK to write a sentence, too. The goal is to let interested people know where this project is at.

### :running: One-liner
Having a one-liner that describes the pipeline/api/app is useful for getting an idea of what your code does in slightly greater detail. 

### :computer: Technology stack
Python, Pandas, Scipy, Scikit-learn, etc. Indicate the technological nature of the software, including primary programming language(s), main libraries and whether the software is intended as standalone or as a module in a framework or other ecosystem.

### :boom: Core technical concepts and inspiration
Why does it exist? Frame your project for the potential user. Compare/contrast your project with other, similar projects so the user knows how it is different from those projects. Highlight the technical concepts that your project demonstrates or supports. Keep it very brief.

## :wrench: Configuration
Install Python 3.7 and mandatory dependencies listed in the requirements file. 

If you are using the Anaconda distribution. Run the following command to create a new environment named "name environment here!"

```
conda env create -f requirements.yml
```

**Note:** Environment managers differ from one another. It's strongly recommended to check its documentation.

### :see_no_evil: Usage
Parameters, return values, known issues, thrown errors.

### :file_folder: Folder structure
```
└── project
    ├── __trash__
    ├── .gitignore
    ├── .env
    ├── requirements.txt
    ├── README.md
    ├── main_script.py
    ├── notebooks
    │   ├── notebook1.ipynb
    │   └── notebook2.ipynb
    ├── package1
    │   ├── module1.py
    │   └── module2.py
    └── data
        ├── raw
        ├── processed
        └── results
```

> Do not forget to include `__trash__` and `.env` in `.gitignore` 

### :bangbang: Alerts and Attention Notes
> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.

### :shit: ToDo
Next steps, features planned, known bugs (shortlist).

### :information_source: Further info
Credits, alternatives, references, license.

## :star: Acknowledgements
<img align="left" width="25" height="25" src="readme/ironhacklogo.png">

[IronHack](https://www.ironhack.com/us "IronHack main webpage")

## :love_letter: Contact info
Doubts? Advice?  Drop me a line! :smirk:

---

> Here you have some repo examples:
- [Mamba (OCR-Translator-Assistant)](https://github.com/YonatanRA/OCR-translator-assistant-project)
- [Art Classification](https://github.com/serguma/art_classification)
- [OSNet-IBN (width x 1.0) Lite](https://github.com/RodMech/OSNet-IBN1-Lite)
- [Movie Founder](https://github.com/Alfagu/final-project-Ironhack-0419mad)
- [Convolutional Neural Network to detect Pneumonia](https://github.com/jmolins89/final-project)
- [Brain tumor detection project](https://github.com/alonsopdani/brain-tumor-detection-project)
- [Policy-Gradient-Methods](https://github.com/cyoon1729/Policy-Gradient-Methods)

> Here you have some tools and references:
- [Make a README](https://www.makeareadme.com/)
- [Awesome README](https://github.com/matiassingers/awesome-readme)
- [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

## Emojis
Emojis complete (cheat sheet)[https://gist.github.com/rxaviers/7360908]

# Aligning images

## `left` alignment

<img align="left" width="100" height="100" src="http://www.fillmurray.com/100/100">

This is the code you need to align images to the left:
```
<img align="left" width="100" height="100" src="http://www.fillmurray.com/100/100">
```

---

## `right` alignment

<img align="right" width="100" height="100" src="http://www.fillmurray.com/100/100">

This is the code you need to align images to the right:
```
<img align="right" width="100" height="100" src="http://www.fillmurray.com/100/100">
```

---

## `center` alignment example

<p align="center">
  <img width="300" height="300" src="https://d92mrp7hetgfk.cloudfront.net/images/sites/misc/ironhack/original.jpg?1568082165">
</p>

```
<p align="center">
  <img width="300" height="300" src="https://d92mrp7hetgfk.cloudfront.net/images/sites/misc/ironhack/original.jpg?1568082165">
</p>
```

---

## Links

```
[I'm an inline-style link with title](https://www.google.com "Google's Homepage")
```
