..
   @startuml images/cesium-view.png

    title Cesium views

    !include plantuml-styles.txt

    !define __View__  << (V,#f79533) Backbone.View >>
  
    package metacatui {

      class MapView __View__ #4F90F4 {
        + Model: MapModel
        + renderMapWidget(): CesiumView
        + renderToolbar(): ToolbarView
        + renderFeatureInfo()
        + renderLayerDetails()
        + showHome()
        + renderScalebar()
      }

      class CesiumView __View__ #F6BF09 {
        + model: MapModel
        + navigateTo()
        + getCurrentPosition(): CameraPosition
        + showLayer()
        + hideLayer()
        + setLayerOpacity()
      }

      note top of CesiumView
        The CesiumView contains *all*
        the functions that interact
        directly with Cesium
      end note

      class ScalebarView __View__ #FF7255 {
        + updateCoordinates()
        + updateScale()
      }

      class FeatureInfoView __View__ #93A1FF {
        + model: Feature
        ' + template: MapModel.featureInfoTemplate
        + show()
        + hide()
        + formatContent()
      }

      together {

        class ToolbarView __View__ #0DF66B {
          + Model: MapModel
          + show()
          + hide()
          + renderSections()
        }

        class ToolbarSectionView __View__ #85ffb6 {
          + Model: MapModel
          + name: String
          + icon: String
          + open()
          + close()
        }

        class LayerSectionView __View__ #85ffb6 {
          + model: MapModel
          + name: "Layers"
          + icon: "layers-icon"
          + renderLayerList()
        }

        class LayerListView __View__ #85ffb6 {
          + Collection: Layers
          + renderLayers()
        }

        class LayerItemView __View__ #85ffb6 {
          + Model: Layer
          + renderLegend()
          + show()
          + hide()
          + focus()
        }

      }

      together {

        class LayerDetailsView __View__ #0DF6E6 {
          + model: Layer
          + show()
          + hide()
          + renderSections()
        }

        class LayerInfoView __View__ #a1fff9{
          + model: Layer
          + renderInfo()
        }

        class LayerOpacityView __View__ #a1fff9{
          + model: Layer
          + updateOpacity()
        }
        
        class LayerDetailView __View__ #a1fff9{
          + model: Layer
          + collapse()
          + expand()
          + renderTitle()
        }

      }

      class CitationView __View__

      ToolbarView *-- LayerSectionView: contains >
      LayerSectionView ..|> ToolbarSectionView: extends >
      LayerSectionView *-- LayerListView: contains >
      LayerListView *-- LayerItemView: contains >

      MapView *-- ToolbarView: contains >
      MapView *-- ScalebarView: contains >
      MapView *-- LayerDetailsView: contains >
      MapView *-- FeatureInfoView: contains >
      MapView *-- CesiumView: contains >

      ' CesiumView *-- ScalebarView: contains >

      LayerDetailsView *-- LayerOpacityView: contains >
      LayerDetailsView *-- LayerInfoView: contains >
      LayerOpacityView ..|> LayerDetailView: extends >
      LayerInfoView ..|> LayerDetailView: extends >
      LayerInfoView *-- CitationView: contains >

    }

@enduml