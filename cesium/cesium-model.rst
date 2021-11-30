..
   @startuml images/cesium-model.png

    !include plantuml-styles.txt

    !define __Model__  << (M,#93a1ff) Backbone.Model >>
    !define __Collection__  << (C,#0df6e6) Backbone.Collection >>

    header <size:20>Updated 2021-11-30</size>
    title <size:30>Cesium map models</size>

  
    package metacatui {
      

      ' ================= models and collections =====================

      class Map __Model__ #f5cd3d {
        + homePosition: CameraPosition
        + layers: MapAssets
        + terrains: MapAssets
        + selectedFeatures: Features
        + showToolbar: Boolean
        + showScaleNar : Boolean
        + showInfoBox: Boolean
        + showFeatureInfo: Boolean
        + currentPosition: { longitude, latitude, elevation}
        + currentScale: { pixels, meters }
        + selectFeature()

        ' Future properties may include:
        ' + layerGroups: LayerGroups
        ' + enable3D: Boolean
        ' + startIn2D: Boolean
        ' + projections: [projectionsCollection]
        ' + pointsOfInterest: [POICollection] 
      }

      note top of Map
        currentPosition and currentScale are updated
        by the map widget (CesiumWidgetView).
        The values are used in the ScaleBarView.
      end note

      object CameraPosition {
        longitude: Number
        latitude: Number
        height: Number
        heading: Number
        pitch: Number
        roll: Number
      }

      class Features __Collection__ #f5cd3d {
        + model: Feature
        + getMapAssets()
        + getFeatureObjects()
        + getUniqueAttrs()
        + containsFeature()
      }

      class Feature __Model__ #f5cd3d {

        + properties: {}
        + featureID: String|Number
        + mapAsset: MapAsset
        + featureObject: *
        + label: String
        + isDefault()
        + setToDefault()
      }

      note as featureNote
        The Feature model provides the data to show
        in the FeatureInfoView - the panel that
        appears when you click on a vector feature
        in the map.
      end note
      featureNote .. Feature

      class LayerGroups __Collection__ #e6ebe9 {}

      class LayerGroup __Model__ #e6ebe9 {
         + label: String
         + description: String
         + icon: String
         + layers: MapAssets
         + hide()
         + show()
         + updateOpacity()
       }

      note top of LayerGroup
        LayerGroup will contain a MapAssets collection
        which points only to the Asset models that
        are in that group. The Map.layers collection
        will contain *all* of the MapAsset layer models.
      end note

      class terrains __Collection__ #65c8f0 {
        ..  <b><size:14><&info></size><size:12>MapAssets collection</size></b> ..
        ---
        + model()
        + setMapModel()
      }

      class layers __Collection__ #85ffb6 {
        ..  <b><size:14><&info></size><size:12>MapAssets collection</size></b> ..
        ---
        + model()
        + setMapModel()
      }

      ' ================= terrain models =====================

      class CesiumTerrain __Model__ #65c8f0 {
        + type: 'CesiumTerrainProvider'
        + cesiumModel: Cesium.TerrainProvider
        + cesiumOptions: {}
        + createCesiumModel()
        + setCesiumURL()
      }

      ' ================= layer models =====================

      
      class MapAsset __Model__ #85ffb6 {
        + type: String
        + label: String
        + icon: String
        + description: String
        + attribution: String
        + moreInfoLink: String
        + downloadLink: String
        + id: String
        + selected: Boolean
        + opacity: Number
        + visible: Boolean
        + colorPalette: AssetColorPalette
        + filters: VectorFilters
        + status: String
        + statusDetails: String
        + featureIsSelected()
        + updateIcon()
        + isSVG()
        + fetchIcon()
        + sanitizeIcon()
        + whenReady()
        + getColor()
        + featureIsVisible()
        + isVisible()
      }


      together {
        together {

          class CesiumVectorData __Model__ #85ffb6 {
            + type: 'GeoJsonDataSource'
            + cesiumModel: Cesium.GeoJsonDataSource
            + cesiumOptions: {}
            + createCesiumModel()
            + whenDisplayReady()
            + setListeners()
            + getCameraBoundSphere()
            + getPropertiesFromFeature()
            + updateAppearance()
            + updateFeatureVisibility()
            + getBoundingSphere()
          }

          class Cesium3DTileset __Model__ #85ffb6 {
            + type: 'Cesium3DTileset'
            + cesiumModel: Cesium.Cesium3DTileset
            + cesiumOptions: {}
            + createCesiumModel()
            + setCesiumURL()
            + setListeners()
            + updateAppearance()
            + updateFeatureVisibility()
            + getPropertiesFromFeature()
            + getColorFunction()
            + getBoundingSphere()
            
          }
          class CesiumImagery __Model__ #85ffb6 {
            + type: 'BingMapsImageryProvider'|'IonImageryProvider'
            + cesiumModel: Cesium.ImageryLayer
            + cesiumOptions: {}
            + createCesiumModel()
            + setListeners()
            + getBoundingSphere()
            + getThumbnail()
          }
          
        }

        together {

          class Geohash __Model__ #e6ebe9 {}

          class ElevationShading __Model__ #e6ebe9 {}

          class ContourLines __Model__ #e6ebe9 {}

        }

      }

      

      ' ================= vector properties =====================

      class AssetColorPalette __Model__ #f5cd3d {
        + paletteType: 'categorical'|'continuous'|'classified'
        + property: String
        + label: String
        + colors: AssetColors
        + getColor()
        + getDefaultColor()
      }

      class AssetColors __Collection__ #f5cd3d {
        + model: AssetColor
        + getDefaultColor()
      }

      class AssetColor __Model__ #f5cd3d {
        + value: String
        + label: String
        + color: { red, blue, green }
        + hexToRGB()
      }

      note left of AssetColorPalette
        The color palette is used to both shade
        vector data (3D tiles), and to create the
        legend/mini-legend (any type of layer)
      end note

      class VectorFilters __Collection__ #f5cd3d {
        + model: VectorFilter
        + featureIsVisible()

      }

      class VectorFilter __Model__ #f5cd3d {
        + filterType: 'categorical'|'numeric'
        + property: String
        + values: String[]|Number[]
        + max: Number
        + min: Number
        + featureIsVisible()
      }

      note left of VectorFilter
        VectorFilter is used to conditionally show/hide
        features of vector data on the map widget.
      end note

      ' ================= connections =====================

      Map *-up- CameraPosition : contains >
      Map *-- terrains : contains >
      Map *-- layers : contains >
      Map *-right- LayerGroups : contains >
      Map *-left- Features : contains >
      Features o-left- Feature : collectionOf >

      terrains o-- CesiumTerrain : collectionOf >
      CesiumTerrain ..|> MapAsset : extends >

      LayerGroups o-right- LayerGroup : collectionOf >
      layers o-- CesiumImagery : collectionOf >
      layers o-- Cesium3DTileset : collectionOf >
      layers o-- CesiumVectorData : collectionOf >
      layers o-- Geohash : collectionOf >
      layers o-- ElevationShading : collectionOf >
      layers o-- ContourLines : collectionOf >
      
      CesiumImagery ..|> MapAsset : extends >
      Cesium3DTileset ..|> MapAsset : extends >
      CesiumVectorData ..|> MapAsset : extends >
      Geohash ..|> MapAsset : extends >
      ElevationShading ..|> MapAsset : extends >
      ContourLines ..|> MapAsset : extends >

      MapAsset *-- AssetColorPalette : contains >
      MapAsset *-- VectorFilters : contains >

      AssetColorPalette *-- AssetColors : contains >
      AssetColors o-- AssetColor : collectionOf >
      VectorFilters o-- VectorFilter : collectionOf >


      ' ================= for hacking the layout =====================
      Cesium3DTileset -[hidden] CesiumImagery
      CesiumVectorData -[hidden] Cesium3DTileset


      ' ================= legend =====================

      
      label legend [
      {{
        legend
      
          <b>Legend</b>

          | Color | Category |
          |<#f5cd3d>| General map |
          |<#65c8f0>| Terrain assets |
          |<#85ffb6>| Layer assets |
          |<#e6ebe9>| Planned (not yet started) |

          | Color | Type |
          |<#93a1ff> <b>M</b>| Model |
          |<#0df6e6> <b>C</b>| Collection |

        end legend
      }}
      ]

      

    }
@enduml