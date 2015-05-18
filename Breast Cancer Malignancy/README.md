# Decision Tree for Breast Cancer Malignancy

Using data from the Wisconsin Breast Cancer Database as a training sample, I construct a
decision tree to identify benign (class=2) versus malignant (class=4) cells. 

## Constructing a Decision Tree in Excel (Using VBA)
For each instance of an attribute (i.e. clump_thickness=1), I calculate the entropy of the
malignancy TRUE/FALSE (malignant/benign) split using the VBA function:
```
Function Entropy(valOfInterest As Integer, values As Range, inClass As Range) As Double
    Dim countIn As Double, countOut As Double
    Dim i As Integer
    countIn = 0
    countOut = 0
    For i = 1 To values.Rows.Count
        If values.Rows(i).Value = valOfInterest Then
            If inClass.Rows(i).Value Then
                countIn = countIn + 1
            Else
                countOut = countOut + 1
            End If
        End If
    Next i
    Dim propIn As Double, propOut As Double
    If countIn + countOut = 0 Then
        Entropy = 0
        Exit Function
    End If
    propIn = countIn / (countIn + countOut)
    propOut = countOut / (countIn + countOut)
    Dim ans As Double
    ans = 0
    If propIn <> 0 Then
        ans = ans - propIn * Log(propIn) / Log(2)
    End If
    If propOut <> 0 Then
    ans = ans - propOut * Log(propOut) / Log(2)
    End If
    Entropy = ans
End Function
```
where 
* `valOfInterest` is the value being considered (i.e. for clump thickness=1, valOfInterest=1)
* `values` is the range of instances for that attribute (i.e. range for clump thickness=1 to 10)
* `inClass` is the range for TRUE/FALSE malignancy

I then calculate the proportion of instances that demonstrate a particular value of an 
attribute (i.e. the proportion of instances with cell thickness = 1) using:
```
Function Proportion(valu As Integer, rang As Range) As Double
    Dim numerator As Double
    Dim denominator As Double
    numerator = WorksheetFunction.CountIf(rang, valu)
    denominator = WorksheetFunction.CountA(rang)
    Proportion = numerator / denominator
End Function
```

Using these values, I calculate **information gain** of each attribute at a particular
node using: 
```
Function infoGain(rangeOfInt As Range, inClassRang As Range) As Double
    Dim i As Integer
   For i = 1 To 10
        Dim info As Double
        info = Proportion(i, rangeOfInt) * Entropy(i, rangeOfInt, inClassRang)
        infoSum = infoSum + info
   Next i
   Dim pvalue As Double
   pvalue = WorksheetFunction.CountIf(inClassRang, "=TRUE") / WorksheetFunction.CountA(inClassRang)
   Dim overallEntropy As Double
   overallEntropy = -pvalue * WorksheetFunction.Log(pvalue, 2) - (1 - pvalue) * WorksheetFunction.Log((1 - pvalue), 2)
   infoGain = overallEntropy - infoSum
End Function
```
where
* `rangeOfInt` is the range of values for the attribute, and
* `inClassRang` is the range of TRUE/FALSE split for these values

The attribute with the highest information gain provides the best attribute by which
to split the decision tree at a particular node. Using this method, I found the 
attribute **uniformity_of_cell_size** to provide the highest information gain and therefore
used this attribute as the root node of the decision tree. See the *Excel* document
for further calculations and full identification of nodes, and a partial visual 
representation of the decision tree.

## Constructing a Decision Tree Using R

Ensure `bc_data.csv` is in your Working Directory, and 
`source("decisionTree.R")`
This will output a JPEG image of a constructed decision tree for the data.