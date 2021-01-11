This is QGIS plugin, which resolves the issue [#40720](https://github.com/qgis/QGIS/issues/40720) by making indexing strategy IndexExtent and connecting some signals together.

Another way of getting the same result is setting this code as "Python Macros" in yours QGIS project:

```python
from qgis.core import QgsProject, Qgis
from qgis.core import *
from qgis.utils import iface

def reIndex():
    if (iface.mapCanvas().snappingUtils().indexingStrategy() !=
            iface.mapCanvas().snappingUtils().IndexingStrategy.IndexExtent):
        iface.mapCanvas().snappingUtils().setIndexingStrategy(
            iface.mapCanvas().snappingUtils().IndexingStrategy.IndexExtent)
    layer = iface.activeLayer()
    if type(layer) == QgsVectorLayer:
        if layer.isEditable():
            layer.emitDataChanged()

def openProject():
    iface.mapCanvas().snappingUtils().setIndexingStrategy(
        iface.mapCanvas().snappingUtils().IndexingStrategy.IndexExtent)
    iface.mapCanvas().mapCanvasRefreshed.connect(reIndex)
    iface.currentLayerChanged.connect(reIndex)
    iface.messageBar().pushMessage("Success", "Index Updater Start",
        level=Qgis.Success, duration=2)

def saveProject():
    pass

def closeProject():
    iface.mapCanvas().mapCanvasRefreshed.disconnect(reIndex)
    iface.currentLayerChanged.disconnect(reIndex)
    iface.mapCanvas().snappingUtils().setIndexingStrategy(
        iface.mapCanvas().snappingUtils().IndexingStrategy.IndexHybrid)
    iface.messageBar().pushMessage("Success", "Index Updater Stop",
        level=Qgis.Success, duration=2)
```
