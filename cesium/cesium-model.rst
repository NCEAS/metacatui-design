..
   @startuml images/cesium-model.png  

    !include plantuml-styles.txt

    !define __Model__  << (M,#93a1ff) Backbone.Model >>
    !define __Collection__  << (C,#0df6e6) Backbone.Collection >>
  
    package metacatui {

      class Map __Model__ {
        + homePosition: CameraPosition
        + terrains: Terrains
        + layers: Layers
        + layerGroups: LayerGroups
        + selectedFeatures: Features
        + showToolbar: Boolean
        + showScalebar : Boolean
        + showInfoBox: Boolean
        ' Future properties may include:
        ' + enable3D: Boolean
        ' + startIn2D: Boolean
        ' + projections: [projectionsCollection]
        ' + pointsOfInterest: [POICollection] 
      }

      object CameraPosition {
        longitude: Number
        latitude: Number
        height: Number
        heading: Number
        pitch: Number
        roll: Number
      }

      class Terrains __Collection__ #9cd7d9 {}

      class Layers __Collection__ #85ffb6 {}

      class Features __Collection__ {}

      class Feature __Model__ {}

      note top of Feature
        The feature model would hold the relevant properties
        of the selected features, to be passed to the
        FeatureInfoView, and eventually to the PlotView. 
      end note

      class LayerGroups __Collection__ #85ffb6 {}

      class LayerGroup __Model__ #85ffb6 {
        + name
        + description
        + icon
        + layers
        + hide()
        + show()
        + updateOpacity()
      }

      note right of LayerGroup
        LayerGroup will contain a Layers collection
        which points only to the Layer models that
        are in that group. The Map.Layers collection
        will contain *all* of the Layer models.
      end note


      class Terrain __Model__ #9cd7d9 {

        + type: String
        + label: String
        + description: String
        + attribution: String

      }

      class CesiumTerrain __Model__ #9cd7d9 {

        + type: "CesiumTerrainProvider"
        + ionResource: Number
        + url: String
        + requestVertexNormals: Boolean
        + requestWaterMask: Boolean
        + requestMetadata: Boolean

      }

      ' ================= layer models =====================

        
      class Layer __Model__ #85ffb6 {

        + type: String
        + label: String
        + description: String
        + icon: String
        + attribution: String
        + pid: String
        + visible: Boolean
        + opacity: Number
        + thumbnail: DataONEObject
        ' + featureInfoTemplate

      }

      together {

        class BingMapsLayer __Model__ #85ffb6 {
          + cesiumType: "BingMapsImageryProvider"
          + cesiumOptions: Object
        }

        class WMTSLayer __Model__ #85ffb6 {
          + cesiumType: "WebMapTileServiceImageryProvider"
          + cesiumOptions: Object
        }

        class 3DTilesetLayer __Model__ #85ffb6 {
          + cesiumType: "Cesium3DTileset"
          + cesiumOptions: Object
        }

        class GeoJSONLayer __Model__ #85ffb6 {
          + cesiumType: "GeoJsonDataSource"
          + cesiumOptions: Object
        }

        class CesiumLayer __Model__ #85ffb6 {
          + cesiumType: String
          + cesiumOptions: Object
        }

        note bottom of CesiumLayer
          The CesiumLayer model could encompass all of the Cesium layer
          types if they are similar enough. Then we would not have an
          extension of each type of Cesium layer as shown here.
          Alternatively, we may only need two extensions:
          CesiumImageryLayer and CesiumDataLayer.
        end note

        class GeohashLayer __Model__ #85ffb6 {
          + type: "Geohashes"
        }

        class ElevationLayer __Model__ #85ffb6 {
          + type: "ElevationLayer"
        }

        class ContourLayer __Model__ #85ffb6 {
          + type: "ContourLineLayer"
        }
      }

      Map *-up- CameraPosition : contains >
      Map *-- Terrains : contains >
      Map *-- Layers : contains >
      Map *-right- LayerGroups : contains >
      Map *-left- Features : contains >

      Features o-left- Feature : collectionOf >

      Terrains o-- CesiumTerrain : collectionOf >
      CesiumTerrain ..|> Terrain : extends >

      LayerGroups o-right- LayerGroup : collectionOf >
      Layers o-- BingMapsLayer : collectionOf >
      Layers o-- WMTSLayer : collectionOf >
      Layers o-- 3DTilesetLayer : collectionOf >
      Layers o-- GeoJSONLayer : collectionOf >
      Layers o-- GeohashLayer : collectionOf >
      Layers o-- ElevationLayer : collectionOf >
      Layers o-- ContourLayer : collectionOf >
      
      BingMapsLayer ..|> CesiumLayer : extends >
      WMTSLayer ..|> CesiumLayer : extends >
      3DTilesetLayer ..|> CesiumLayer : extends >
      GeoJSONLayer ..|> CesiumLayer : extends >
      GeohashLayer ..|> Layer : extends >
      ElevationLayer ..|> Layer : extends >
      ContourLayer ..|> Layer : extends >

      CesiumLayer ..|> Layer : extends >

      

    }

@enduml