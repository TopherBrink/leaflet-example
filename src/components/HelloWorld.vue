<template>
  <div class="hello">
    <l-map
      class="map"
      :zoom="zoom"
      :center="center"
      :options="{zoomControl: false}"
      ref="map"
      @ready="onInitMap()"
      >
      <l-tile-layer :url="url"></l-tile-layer>
      <l-draw-toolbar position="topright"/>
      <!-- other map components -->
      <l-marker
        :lat-lng="point.coords"
        :key="point.id"
        :icon="icon"
        >
        <l-popup>
          <div class="map-baloon">
            <span class="map-baloon__title">Балун точки {{point.id}}</span>
          </div>
        </l-popup>
      </l-marker>
      <l-marker
        :lat-lng="point2.coords"
        :key="point2.id"
        :icon="icon"
        >
        <l-popup>
          <div class="map-baloon">
            <span class="map-baloon__title">Балун точки {{point2.id}}</span>
          </div>
        </l-popup>
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import L from 'leaflet';
import { LMap,
  LTileLayer,
  LPolygon,
  LMarker,
  LPopup,
} from 'vue2-leaflet';
import { icon } from 'leaflet';
import LDrawToolbar from 'vue2-leaflet-draw-toolbar';
import drawLocales from 'leaflet-draw-locales';
import 'leaflet/dist/leaflet.css';

export default {
  name: 'HelloWorld',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LDrawToolbar,
    LPolygon,
    LPopup,
  },
  props: {
    msg: String
  },
  data() {
    return {
      url: 'https://{s}.tile.jawg.io/jawg-sunny/{z}/{x}/{y}{r}.png?access-token=QX5YJO8dEpmfvdoHcdCgJzWoXXuX2YMKMvPRWpMCN2rGrieH38wbmHSh6evSKFWd',
      map: null,
      drawnItems: null,
      center: [
        55.7522,
        37.6156,
      ],
      zoom: 13,
      settings: {
        apiKey: '',
        lang: 'ru_RU',
        coordorder: 'latlong',
        enterprise: false,
        version: '2.1',
      },
      color: '#00A0E3',
      activeColor: '#5EB761',
      icon: icon({
        iconUrl: 'data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTIiIGhlaWdodD0iMTgiIHZpZXdCb3g9IjAgMCAxMiAxOCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBjbGlwLXJ1bGU9ImV2ZW5vZGQiIGQ9Ik02LjAwMDMzIDAuNjY3OTY5QzIuNzc1MzMgMC42Njc5NjkgMC4xNjY5OTIgMy4yNzYzIDAuMTY2OTkyIDYuNTAxM0MwLjE2Njk5MiAxMC44NzYzIDYuMDAwMzMgMTcuMzM0NiA2LjAwMDMzIDE3LjMzNDZDNi4wMDAzMyAxNy4zMzQ2IDExLjgzMzcgMTAuODc2MyAxMS44MzM3IDYuNTAxM0MxMS44MzM3IDMuMjc2MyA5LjIyNTMzIDAuNjY3OTY5IDYuMDAwMzMgMC42Njc5NjlaTTEuODMzNyA2LjUwMTIyQzEuODMzNyA0LjIwMTIyIDMuNzAwMzcgMi4zMzQ1NSA2LjAwMDM3IDIuMzM0NTVDOC4zMDAzNyAyLjMzNDU1IDEwLjE2NyA0LjIwMTIyIDEwLjE2NyA2LjUwMTIyQzEwLjE2NyA4LjkwMTIyIDcuNzY3MDMgMTIuNDkyOSA2LjAwMDM3IDE0LjczNDZDNC4yNjcwMyAxMi41MDk2IDEuODMzNyA4Ljg3NjIyIDEuODMzNyA2LjUwMTIyWk0zLjkxNjk5IDYuNTAxM0MzLjkxNjk5IDUuMzUwNzEgNC44NDk3MyA0LjQxNzk3IDYuMDAwMzMgNC40MTc5N0M2Ljc0NDYzIDQuNDE3OTcgNy40MzIzOSA0LjgxNTA1IDcuODA0NTUgNS40NTk2NEM4LjE3NjcgNi4xMDQyMiA4LjE3NjcgNi44OTgzOCA3LjgwNDU1IDcuNTQyOTdDNy40MzIzOSA4LjE4NzU1IDYuNzQ0NjMgOC41ODQ2MyA2LjAwMDMzIDguNTg0NjNDNC44NDk3MyA4LjU4NDYzIDMuOTE2OTkgNy42NTE4OSAzLjkxNjk5IDYuNTAxM1oiIGZpbGw9IiM1MjlCMkYiLz4KPC9zdmc+Cg==',
        iconSize: [24, 36],
        iconAnchor: [12, 36],
      }),
      point: {
        coords: [
          55.7522,
          37.6156,
        ],
        id: 1,
      },
      point2: {
        coords: [
          55.7510,
          37.6042,
        ],
        id: 2,
      },
      counter: 1,
    };
  },
  methods: {
    onInitMap() {
      this.map = this.$refs.map.mapObject;
      drawLocales('ru');
      this.drawnItems = new L.FeatureGroup();
      this.map.addLayer(this.drawnItems);
      const drawControl = new L.Control.Draw({
        draw: {
          polyline: false,
          polygon: {
            allowIntersection: false,
            shapeOptions: {
              color: this.color,
            },
          },
          circle: {
            shapeOptions: {
              color: this.color,
            },
          },
          rectangle: false,
          marker: {
            icon: icon({
              iconUrl: 'data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTIiIGhlaWdodD0iMTgiIHZpZXdCb3g9IjAgMCAxMiAxOCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBjbGlwLXJ1bGU9ImV2ZW5vZGQiIGQ9Ik02LjAwMDMzIDAuNjY3OTY5QzIuNzc1MzMgMC42Njc5NjkgMC4xNjY5OTIgMy4yNzYzIDAuMTY2OTkyIDYuNTAxM0MwLjE2Njk5MiAxMC44NzYzIDYuMDAwMzMgMTcuMzM0NiA2LjAwMDMzIDE3LjMzNDZDNi4wMDAzMyAxNy4zMzQ2IDExLjgzMzcgMTAuODc2MyAxMS44MzM3IDYuNTAxM0MxMS44MzM3IDMuMjc2MyA5LjIyNTMzIDAuNjY3OTY5IDYuMDAwMzMgMC42Njc5NjlaTTEuODMzNyA2LjUwMTIyQzEuODMzNyA0LjIwMTIyIDMuNzAwMzcgMi4zMzQ1NSA2LjAwMDM3IDIuMzM0NTVDOC4zMDAzNyAyLjMzNDU1IDEwLjE2NyA0LjIwMTIyIDEwLjE2NyA2LjUwMTIyQzEwLjE2NyA4LjkwMTIyIDcuNzY3MDMgMTIuNDkyOSA2LjAwMDM3IDE0LjczNDZDNC4yNjcwMyAxMi41MDk2IDEuODMzNyA4Ljg3NjIyIDEuODMzNyA2LjUwMTIyWk0zLjkxNjk5IDYuNTAxM0MzLjkxNjk5IDUuMzUwNzEgNC44NDk3MyA0LjQxNzk3IDYuMDAwMzMgNC40MTc5N0M2Ljc0NDYzIDQuNDE3OTcgNy40MzIzOSA0LjgxNTA1IDcuODA0NTUgNS40NTk2NEM4LjE3NjcgNi4xMDQyMiA4LjE3NjcgNi44OTgzOCA3LjgwNDU1IDcuNTQyOTdDNy40MzIzOSA4LjE4NzU1IDYuNzQ0NjMgOC41ODQ2MyA2LjAwMDMzIDguNTg0NjNDNC44NDk3MyA4LjU4NDYzIDMuOTE2OTkgNy42NTE4OSAzLjkxNjk5IDYuNTAxM1oiIGZpbGw9IiM1MjlCMkYiLz4KPC9zdmc+Cg==',
              iconSize: [24, 36],
              iconAnchor: [12, 36],
            }),
          },
          circlemarker: false,
        },
        edit: false,
      });
      this.map.addControl(drawControl);
      this.map.on(L.Draw.Event.DRAWSTART, () => {
        // this.drawnItems.clearLayers();
      });
      this.map.on(L.Draw.Event.DRAWSTART, () => {
        this.inDrawMode = true;
      });
      this.map.on(L.Draw.Event.CREATED, (event) => {
        this.inDrawMode = false;
        const { layer, layerType } = event;
        this.drawnItems.addLayer(layer);
      });

      setInterval(() => {
        if (this.counter % 3 === 1) {
          this.point.coords = [
            55.7622,
            37.6176,
          ]
          this.counter++
        } else if (this.counter % 3 === 2) {
          this.point.coords = [
            55.7752,
            37.6076,
          ]
          this.counter++
        } else if (this.counter % 3 === 0) {
          this.point.coords = [
            55.7522,
            37.6156,
          ]
          this.counter++
        }
      }, 2000)
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.hello {
  width: calc(100vw - 20px);
  height: calc(100vh - 20px);
  margin: 10px;
}
.map-baloon {
  padding: 0;
}
.map-baloon__title {
  font-size: 14px;
  line-height: 14px;
  font-weight: 700;
}
.leaflet-marker-pane > * {
  -webkit-transition: transform 2s linear;
  -moz-transition: transform 2s linear;
  -o-transition: transform 2s linear;
  -ms-transition: transform 2s linear;
  transition: transform 2s linear;
}
</style>
