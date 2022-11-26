#### Update "Cd" attrib based on "xor" comparison of y-pos of 2 geometries.
```python
# Run in Python SOP.
# Update "Cd" attrib based on "xor" comparison of y-position of 2 geometries.
node = hou.pwd()
geo = node.geometry()

pts = geo.iterPoints()
xform1 = hou.node("../transform1")
xform2 = hou.node("../transform2")

cond1 = xform1.evalParm("ty") > 3
cond2 = xform2.evalParm("ty") > 3

xor = cond1 != cond2
color = hou.Vector3((0.2,0.2,0.6))
if xor:
    geo.addAttrib(hou.attribType.Point, "Cd", (0.2,0.2,0.6))
```
#### Create blast node for each unique value of "cat_name" prim attrib.
```python
# Create blast node for each unique value of "cat_name" prim attrib.
import hou
null = hou.node("/obj/geo1/null1")
geo = null.geometry()
prims = geo.prims()
geoObj = hou.node("/obj/geo1/")

# Create list containing unique values of required attribute.
catNames = []
for prim in prims:
    if prim.attribValue("cat_name") not in catNames:
        catNames.append(prim.attribValue("cat_name"))
        
print catNames

# Create values for blast nodes.
blastVals =  []
for i in range(0, len(catNames)):
    val = "@cat_name=" + catNames[i]
    blastVals.append(val)

print blastVals

# Create blast nodes, set param values and their inputs.
for i in range(0, len(catNames)):
    blast = geoObj.createNode("blast")
    blast.setParms({"group": blastVals[i], "negate": True})
    blast.setFirstInput(null)
    blast.moveToGoodPosition()
```
#### Write "Cd" prim attrib values into a text file.
```python
# Write "Cd" prim attrib values into a text file.
import hou

objSphere = hou.node('/obj/sphere1')
outNull = hou.node('/obj/sphere1/out_geo')
outNullGeo = outNull.geometry()

# Write file at defined disk path.
colorFile = open('/path/to/file.txt', 'w')
prims = outNullGeo.prims()

# Fetch prim attribute values and append those to empty lists.
colorAttribVals = []
pathAttribVals = []
for prim in prims:
    Cd = prim.attribValue('Cd')
    path = prim.attribValue('path')
    colorAttribVals.append(Cd)
    pathAttribVals.append(path)

# Write those values into file.
for color, path in zip(colorAttribVals, pathAttribVals):
    colorFile.write(str(color) + ' | ' + path + '\n')

colorFile.close()
```
#### Add string attribute and update value through loop.
```python
# Run in Python SOP.
# Add string attribute and update value through loop.
node = hou.pwd()
geo = node.geometry()

points = geo.iterPoints()
for point in points:
    point.setAttribValue("attributeName", "Value")

# Alternative method below to set uniform value for all points.
geo.addAttrib(hou.attribType.Point, "attributeName", "")
geo.setPointStringAttribValues("attributeName", ["hello"] * len(geo.points()))
```
#### Activate geometry at certain frames.
```python
# Run in Python SOP.
# Activate geometry at certain frames.
node = hou.pwd()
geo = node.geometry()
sFrames = node.evalParm("frames")	#custom string channel
framesList = sFrames.split()
frames = [int(f) for f in framesList]
points = geo.points()
if hou.frame() not in frames:
   geo.deletePoints(points)
```
#### Transform geometry using hmath functions.
```python
# Run in Python SOP.
# Transform geometry using hmath functions.
node = hou.pwd()
geo = node.geometry()

vec = hou.Vector3((5, -2, 4))
M = hou.hmath.buildTranslate(vec)	#build matrix4 from a vector
geo.transform(M)
print M
```
#### Compare parameter values of two nodes of same type and display modified ones.
```python
def compare_parms(node1, node2):
    parms1 = node1.parms()
    parms2 = node2.parms()
    mod_parms = []
    for parm1, parm2 in zip(parms1, parms2):
        if parm1.eval() != parm2.eval():
            mod_parms.append(parm1.description() + " " + parm1.name())
    parms = "\n".join(mod_parms)
    if not mod_parms:
        hou.ui.displayMessage("Parameters Equal")
    else:
        print parms
        hou.ui.displayMessage(parms)

try:
    if len(hou.selectedNodes()) == 2:
        node1 = hou.selectedNodes()[0]
        node2 = hou.selectedNodes()[1]
        compare_parms(node1, node2)
    else:
        hou.ui.displayMessage("Please select two nodes to compare their parameter values.", \
title="Select Nodes")
except IndexError:
    hou.ui.displayMessage("Please select two nodes to compare their parameter values.", \
title="Select Nodes")
```
#### Change node position and color using parameters of Transform node.
```python
# Change node position and color using parameters of Transform node.
transform = hou.node("/obj/geo1/transform1")
sphere = hou.node("/obj/geo1/sphere1")

translate = transform.evalParmTuple("t")
tarpos = hou.Vector2(-translate[0], translate[2])
sphere.setPosition(tarpos)

ry = transform.evalParm("ry")
color = hou.Color((1,1,1))
color.setHSV((ry, 0.6, 0.6))
sphere.setColor(color)
```
#### Make contents of ordered menu dynamic.
```python
# Create an Ordered Menu parameter first for a node.
# Paste script under Menu | Menu Script in Editor Parameter window.
geo = hou.node("/obj/geo1")
children = geo.children()
names = [node.name() for node in children]
out = [item for sublist in enumerate(names) for item in sublist]
return out
```
```python
# Add spare parameter template to a node.
import hou
subnet = hou.node("/obj/geo1/subnet1")
for i in range(2):
    value = hou.FloatParmTemplate("value"+str(i+1), "Value "+str(i+1), 1)
    weight = hou.FloatParmTemplate("weight"+str(i+1), "Weight "+str(i+1), 1)
    value.setJoinWithNext(1)
    subnet.addSpareParmTuple(value)
    subnet.addSpareParmTuple(weight)
```
```python
from random import random, choice
node = hou.pwd()
geo = node.geometry()
geo.addAttrib(hou.attribType.Prim, "Cd", (0.2,0.2,0.6))
geo.addAttrib(hou.attribType.Prim, "name", "")
prims = geo.iterPrims()
choices = [(0.2,0.2,0.6), (0.8,0.2,0.3), (0.3, 0.5, 0.5)]
for prim in prims:
    prim.setAttribValue("Cd", (choice(choices)))
    prim.setAttribValue("name", "prim" + str(prim.number()))
```
