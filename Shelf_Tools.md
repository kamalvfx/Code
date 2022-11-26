#### Viewport Capture
```python
import os

cur_desktop = hou.ui.curDesktop()
scene_viewer = hou.paneTabType.SceneViewer
scene = cur_desktop.paneTabOfType(scene_viewer)
scene.flipbookSettings().stash()
flip_book_options = scene.flipbookSettings()

# create output path
flip = hou.expandString('$JOB')+ "/flip/"
hipname = hou.expandString('$HIPNAME')
folder = flip + hipname
path = folder + "/" + hipname + ".$F4.jpeg"

if os.path.exists(folder) < 1:
    os.mkdir(folder)

# startframe and endframe
startframe = int(hou.expandString('$RFSTART'))
endframe = int(hou.expandString('$RFEND'))

# run flipbook with custom settings
flip_book_options.output(path) # Provide flipbook full path with padding.
flip_book_options.frameRange((startframe, endframe)) # Enter Frame Range Here in x & y
flip_book_options.useResolution(0)
# flip_book_options.resolution((1080, 720)) # Based on your camera resolution
scene.flipbook(scene.curViewport(), flip_book_options)
```
#### Increment Version
```python
hou.hipFile.saveAndIncrementFileName()
```
