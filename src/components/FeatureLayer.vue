<script>
import { onMounted, watch } from "vue-function-api";

export default {
  name: "FeatureLayer",
  props: {
    mapContext: {
      type: Object,
      required: true
    },
    sourceId: String,
    mapId: String,
    layerType: {
      type: String,
      required: true
    },
    layout: {
      type: Object,
      required: false,
      default: () => ({})
    },
    paint: {
      type: Object,
      required: false,
      default: () => ({})
    },
    focused: String
  },
  setup(props, context) {
    watch(
      () => props.focused,
      async val => {
        if (props.mapContext.getLayer(props.mapId)) {
          props.mapContext.setFilter(props.mapId, ["==", "name", val]);
        } else {
          // props.mapContext.setLayoutProperty(props.mapId, 'visibility', 'none')
        }
      }
    );
    onMounted(() => {
      if (props.img) {
        const image = props.img;
        props.mapContext.loadImage(image, (err, img) => {
          props.mapContext.addImage(props.imgName, img);
          props.mapContext.addLayer({
            id: props.mapId,
            source: props.sourceId,
            type: props.layerType,
            layout: {
              "icon-size": Number(props.imgSize),
              "icon-image": props.imgName
            }
          });
        });
      } else {
        props.mapContext.addLayer({
          id: props.mapId,
          source: props.sourceId,
          type: props.layerType,
          layout: props.layout,
          paint: props.paint
        });
      }
      props.mapContext.on("mouseenter", props.mapId, () => {
        props.mapContext.getCanvas().style.cursor = "pointer";
      });
      props.mapContext.on("mouseleave", props.mapId, () => {
        props.mapContext.getCanvas().style.cursor = "";
      });
      props.mapContext.on("click", props.mapId, event => {
        context.emit("layer-clicked", event);
      });
    });
  },
  render() {
    return null;
  }
};
</script>

<style lang="scss" scoped></style>
