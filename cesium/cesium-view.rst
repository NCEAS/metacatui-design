..
   @startuml images/cesium-view.png

    !include plantuml-styles.txt

    !define __View__  << (V,#f79533) Backbone.View >>

    header <size:20>Updated 2021-11-30</size>
    title <size:30>Cesium map views</size>
  
    package metacatui {

      class MapView __View__ #4F90F4 {
        + model: Map
        + renderMapWidget(): CesiumWidgetView
        + renderToolbar(): ToolbarView
        + renderFeatureInfo(): FeatureInfoView
        + renderLayerDetails(): LayerDetailsView
        + renderScaleBar(): ScaleBarView
      }

      class CesiumWidgetView __View__ #F6BF09 {
        + model: Map
        + mapAssetRenderFunctions: {}[]
        + highlightBorderColor: Cesium.Color
        + requestRender()
        + postRender()
        + updateDataSourceDisplay()
        + initializePicking()
        + highlightSelectedFeatures()
        + flyTo()
        + completeFlight()
        + flyHome()
        + getCameraPosition()
        + setMouseMoveListeners()
        + updateCurrentScale()
        + pixelToMeters()
        + addAsset()
        + updateTerrain()
        + add3DTileset()
        + addVectorData()
        + addImagery()
      }

      note top of CesiumWidgetView
        The CesiumWidgetView contains all
        the functions that interact
        directly with Cesium
      end note

      class ScalebarView __View__ #FF7255 {
        + distances: [0.1,0.5,1,2,3,5,10,20...]
        + updateCoordinates()
        + updateScale()
        + prettifyScaleValues()
      }

      class FeatureInfoView __View__ #93A1FF {
        + model: Feature
        + isOpen: Boolean
        + renderContent()
        + showLayerDetails()
        + open()
        + close()
        + update()
      }

      together {

        class ToolbarView __View__ #0DF66B {
          + model: Map
          + sections: [{ label, icon, view, viewOptions }, ...]
          + isOpen: Boolean
          + handleLinkClick()
          + renderSectionLink()
          + createIcon()
          + renderSectionContent()
          + open()
          + close()
          + activateSection()
          + inactivateSection()
          + inactivateAllSections()
        }

        class LayerListView __View__ #85ffb6 {
          + collection: MapAssets
        }

        class LayerItemView __View__ #85ffb6 {
          + model: MapAsset
          + errorMessage: String
          + insertIcon()
          + toggleSelected()
          + toggleVisibility()
          + showSelection()
          + showVisibility()
          + showStatus()
          + removeStatuses()
          + showError()
          + showLoading()
        }

      }

      class LegendView __View__ #85ffb6 {
        + mode: 'preview'|'full'
        + previewSvgDimensions: { width, heights, squareSpacing }
        + renderImagePreviewLegend()
        + renderCategoricalPreviewLegend()
        + createSVG()
      }

      together {

        class LayerDetailsView __View__ #0DF6E6 {
          + model: MapAsset
          + sections: [ {label, view } ... ]
          + show()
          + hide()
          + renderSections()
        }

        class LayerInfoView __View__ #a1fff9{
          + model: MapAsset
        }

        class LayerOpacityView __View__ #a1fff9{
          + model: MapAsset
          + handleSliderEvent()
          + updateSlider()
          + updateModel()
          + updateLabel()
          + onClose()
        }

        class LayerNavigationView __View__ #a1fff9{
          + model: MapAsset
          + flyToExtent()
        }
        
        class LayerDetailView __View__ #a1fff9{
          + model: MapAsset
          + toggle()
          + onClose()
        }

      }

      class CitationView __View__

      ' ToolbarView *-- LayerSectionView: contains >
      ' LayerSectionView ..|> ToolbarSectionView: extends >

      ToolbarView *-- LayerListView: contains >
      LayerListView *-- LayerItemView: contains >
      LayerItemView *-- LegendView: contains >
      
      MapView *-- ToolbarView: contains >
      MapView *-- ScalebarView: contains >
      MapView *-- LayerDetailsView: contains >
      MapView *-- FeatureInfoView: contains >
      MapView *-- CesiumWidgetView: contains >

      LayerDetailsView *-- LayerOpacityView: contains >
      LayerDetailsView *-- LayerNavigationView: contains > 
      LayerDetailsView *-- LayerInfoView: contains >
      LayerNavigationView ..|> LayerDetailView: extends >
      LayerOpacityView ..|> LayerDetailView: extends >
      LayerInfoView ..|> LayerDetailView: extends >
      LayerInfoView *-- CitationView: contains >

    }

@enduml