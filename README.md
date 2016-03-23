[![Build Status](https://travis-ci.org/alex-pirozhenko/sklearn-pmml.svg)](https://travis-ci.org/alex-pirozhenko/sklearn-pmml)
[![Join the chat at https://gitter.im/alex-pirozhenko/sklearn-pmml](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/alex-pirozhenko/sklearn-pmml?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# sklearn-pmml

A library that allows serialization of SciKit-Learn estimators into PMML

# Installation
The easiest way is to use pip:
```
pip install sklearn-pmml
```

# Supported models
- DecisionTreeClassifier
- DecisionTreeRegressor
- GradientBoostingClassifier
- RandomForestClassifier

# PMML output

## Classification
Classifier converters can only operate with categorical outputs, and for each categorical output variable ```varname```
the PMML output contains the following outputs:
- categorical ```varname``` for the predicted label of the instance
- double ```varname.label``` for the probability for a given label

## Regression
Regression model PMML outputs the numeric response variable named as the output variable

# Example
```python
import sklearn_pmml
from sklearn import tree
from sklearn_pmml.convert.features import *
from sklearn_pmml.convert.tree import DecisionTreeConverter
from sklearn_pmml.convert import TransformationContext, pmml_row, ModelMode, Schema

# Train a Decision Treee
X = [[0, 0], [1, 1], [1,0],[1,1]]
Y = [0, 1, 1, 1]
est = tree.DecisionTreeClassifier(max_depth=2)
est = est.fit(X, Y)
est.predict([[2., 2.]]) # array([1])

# Define a context
ctx = TransformationContext({
    Schema.INPUT: [
        IntegerNumericFeature('x1'),
        StringCategoricalFeature('x2', ['zero', 'one'])
    ],
    Schema.MODEL: [
        IntegerNumericFeature('x1'),
        StringCategoricalFeature('x2', ['zero', 'one'])
    ],
    Schema.DERIVED: [],
    Schema.OUTPUT: [
        IntegerNumericFeature('output')
    ]
})

# Generate PMML
mode = ModelMode.CLASSIFICATION
converter = DecisionTreeConverter(estimator=est, context=ctx, mode=mode)
xml = converter.pmml().toDOM().toprettyxml()

# Print the PMML
print(xml)
```

# Development
Clone this repository and install the requirements
```sh
git clone 'https://github.com/alex-pirozhenko/sklearn-pmml'
cd sklearn-pmml
python setup.py install
```
Installation on Mac OSX may require you to update xcode
```sh
xcode-select --install
```

# Testing
Testing is done with the python `setuptools` library. Test SKLearn objects are stored in the
```sh
python setup.py test
```

