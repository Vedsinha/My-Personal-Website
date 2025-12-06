<template>
  <div class="aqi-ui" :class="{ active: visible }">
    <div class="relative w-full h-screen overflow-hidden bg-slate-900 font-sans">
      <div ref="containerRef" class="w-full h-full cursor-grab active:cursor-grabbing">
        <svg ref="svgRef" class="w-full h-full block touch-none"></svg>
      </div>
      
      <!-- Loading Spinner -->
      <div v-if="loading" class="absolute inset-0 z-50 flex items-center justify-center bg-white/80 backdrop-blur-sm">
        <div class="flex flex-col items-center justify-center h-full space-y-4">
          <div class="w-12 h-12 border-4 border-blue-500 rounded-full border-t-transparent animate-spin"></div>
          <p class="text-slate-600 font-medium animate-pulse">Loading Full Map Data...</p>
        </div>
      </div>
      
      <!-- AQI Card -->
      <div 
        v-if="currentInfo.visible && currentInfo.data"
        class="absolute top-6 left-6 w-72 backdrop-blur-xl p-6 rounded-2xl shadow-2xl transform transition-all duration-200 ease-out z-20"
        :class="aqiCardClasses"
      >
        <div class="flex justify-between items-start mb-2">
          <h2 class="text-2xl font-bold text-slate-800">{{ currentInfo.name }}</h2>
        </div>
        <div class="flex items-baseline space-x-2 mt-2">
          <span class="text-5xl font-extrabold" :class="aqiValueColor">
            {{ currentInfo.data.value }}
          </span>
          <span class="text-sm font-medium text-slate-500">AQI</span>
        </div>
        <div 
          class="inline-flex items-center px-3 py-1 rounded-full text-xs font-bold uppercase tracking-wide mt-3"
          :class="aqiBadgeClasses"
        >
          {{ currentInfo.data.status }}
        </div>
      </div>
      
      <!-- Header -->
      <div v-if="!loading" class="absolute top-6 right-6 z-10 text-right">
        <h1 class="text-2xl font-black text-slate-800 tracking-tight drop-shadow-sm bg-white/50 px-4 py-2 rounded-xl backdrop-blur-sm">
          India AQI Watch
        </h1>
        <div class="flex items-center justify-end space-x-2 mt-1 px-4">
          <p class="text-sm font-semibold text-slate-600 uppercase tracking-widest opacity-80">
            {{ statusMessage }}
          </p>
          <div class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></div>
        </div>
      </div>
      
      <!-- Legend -->
      <div v-if="!loading" class="absolute bottom-6 right-6 bg-white/90 backdrop-blur-md p-4 rounded-xl shadow-lg border border-slate-200 z-10 hidden sm:block">
        <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-3">AQI Index</h3>
        <div class="space-y-2">
          <div class="flex items-center space-x-3">
            <div class="w-3 h-3 rounded-full bg-red-500 shadow-[0_0_8px_rgba(239,68,68,0.8)]"></div>
            <span class="text-sm font-medium text-slate-700">&gt; 300 (Severe)</span>
          </div>
          <div class="flex items-center space-x-3">
            <div class="w-3 h-3 rounded-full bg-yellow-500 shadow-[0_0_8px_rgba(234,179,8,0.8)]"></div>
            <span class="text-sm font-medium text-slate-700">100 - 300 (Moderate/Poor)</span>
          </div>
          <div class="flex items-center space-x-3">
            <div class="w-3 h-3 rounded-full bg-green-500 shadow-[0_0_8px_rgba(34,197,94,0.8)]"></div>
            <span class="text-sm font-medium text-slate-700">&lt; 100 (Good)</span>
          </div>
        </div>
      </div>
      
      <!-- Vignette Overlay -->
      <div class="absolute inset-0 pointer-events-none bg-[radial-gradient(circle_at_center,transparent_0%,rgba(15,23,42,0.3)_100%)]"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch, onUnmounted } from 'vue'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  startAnimation: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['animation-complete'])

const AQI_DATA = {
  "Delhi": { value: 355, status: "Severe" },
  "Uttar Pradesh": { value: 315, status: "Severe" },
  "Haryana": { value: 290, status: "Poor" },
  "Punjab": { value: 240, status: "Poor" },
  "Maharashtra": { value: 165, status: "Moderate" },
  "West Bengal": { value: 280, status: "Poor" },
  "Karnataka": { value: 65, status: "Satisfactory" },
  "Kerala": { value: 45, status: "Good" },
  "Tamil Nadu": { value: 110, status: "Moderate" },
  "Gujarat": { value: 150, status: "Moderate" },
  "Rajasthan": { value: 140, status: "Moderate" },
  "Telangana": { value: 180, status: "Moderate" },
  "Andhra Pradesh": { value: 100, status: "Satisfactory" },
  "Madhya Pradesh": { value: 200, status: "Poor" },
  "Bihar": { value: 250, status: "Poor" },
  "Assam": { value: 85, status: "Satisfactory" },
  "Odisha": { value: 210, status: "Poor" },
  "Jammu and Kashmir": { value: 90, status: "Satisfactory" },
  "Ladakh": { value: 90, status: "Satisfactory" },
  "Himachal Pradesh": { value: 95, status: "Satisfactory" }
}

const COLORS = {
  ocean: "#cce3f3",
  land: "#e6e6d5",
  aqiRed: "#ef4444",
  aqiYellow: "#eab308",
  aqiGreen: "#22c55e",
  glowRed: "rgba(239, 68, 68, 0.6)",
  glowYellow: "rgba(234, 179, 8, 0.6)",
  glowGreen: "rgba(34, 197, 94, 0.6)"
}

const containerRef = ref(null)
const svgRef = ref(null)
const loading = ref(true)
const statusMessage = ref('Loading Map Data...')
const currentInfo = ref({ name: null, data: null, visible: false })

let isMounted = true

const aqiCardClasses = computed(() => {
  if (!currentInfo.value.data) return 'bg-gradient-to-br from-slate-100 to-white border-slate-200'
  const value = currentInfo.value.data.value
  if (value > 300) return 'bg-gradient-to-br from-red-50 to-white border border-red-200'
  if (value > 100) return 'bg-gradient-to-br from-yellow-50 to-white border border-yellow-200'
  return 'bg-gradient-to-br from-green-50 to-white border border-green-200'
})

const aqiValueColor = computed(() => {
  if (!currentInfo.value.data) return 'text-slate-800'
  const value = currentInfo.value.data.value
  if (value > 300) return 'text-red-700'
  if (value > 100) return 'text-yellow-700'
  return 'text-green-700'
})

const aqiBadgeClasses = computed(() => {
  if (!currentInfo.value.data) return 'bg-slate-100 text-slate-800'
  const value = currentInfo.value.data.value
  if (value > 300) return 'bg-red-100 text-red-800'
  if (value > 100) return 'bg-yellow-100 text-yellow-800'
  return 'bg-green-100 text-green-800'
})

const sleep = (ms) => new Promise(r => setTimeout(r, ms))

const getSafeId = (name) => name.replace(/\s+/g, '').replace(/&/g, '').toLowerCase()

const normalizeName = (name) => {
  if (!name) return ""
  if (name.includes("Delhi")) return "Delhi"
  if (name.includes("Jammu")) return "Jammu and Kashmir"
  if (name.includes("Ladakh")) return "Ladakh"
  if (name.includes("Odisha") || name.includes("Orissa")) return "Odisha"
  if (name.includes("Telangana") || name.includes("Telengana")) return "Telangana"
  return name
}

const initializeMap = async () => {
  try {
    const d3Module = await import('https://cdn.jsdelivr.net/npm/d3@7/+esm')
    const topojsonModule = await import('https://cdn.jsdelivr.net/npm/topojson-client@3/+esm')
    const d3 = d3Module
    const topojson = topojsonModule

    if (!isMounted) return

    statusMessage.value = 'Fetching GeoJSON...'

    const [worldData, indiaData] = await Promise.all([
      d3.json("https://unpkg.com/world-atlas@2.0.2/countries-110m.json"),
      d3.json("https://raw.githubusercontent.com/Subhash9325/GeoJson-Data-of-Indian-States/master/Indian_States")
    ])

    if (!isMounted) return

    loading.value = false
    statusMessage.value = 'Starting Animation...'

    const width = containerRef.value.clientWidth
    const height = containerRef.value.clientHeight

    const svg = d3.select(svgRef.value)
      .attr("viewBox", [0, 0, width, height])
      .style("background-color", COLORS.ocean)

    svg.selectAll("*").remove()
    const g = svg.append("g")

    const projection = d3.geoMercator().scale(width / 6.5).translate([width / 2, height / 1.5])
    const path = d3.geoPath().projection(projection)

    const countries = topojson.feature(worldData, worldData.objects.countries)

    g.selectAll("path.country")
      .data(countries.features)
      .enter()
      .append("path")
      .attr("class", "country")
      .attr("d", path)
      .attr("fill", COLORS.land)
      .attr("stroke", "#fff")
      .attr("stroke-width", 0.5)

    const indiaFeatures = indiaData.features

    g.selectAll("path.state")
      .data(indiaFeatures)
      .enter()
      .append("path")
      .attr("class", "state")
      .attr("d", path)
      .attr("fill", COLORS.land)
      .attr("stroke", "#fff")
      .attr("stroke-width", 0.5)
      .attr("id", d => {
        const normalized = normalizeName(d.properties.NAME_1 || d.properties.st_nm || d.properties.name)
        return `state-${getSafeId(normalized)}`
      })

    const zoomToBox = (bounds, duration = 500) => {
      const [[x0, y0], [x1, y1]] = bounds
      const dx = x1 - x0
      const dy = y1 - y0
      const x = (x0 + x1) / 2
      const y = (y0 + y1) / 2
      const scale = Math.max(1, Math.min(20, 0.75 / Math.max(dx / width, dy / height)))
      const translate = [width / 2 - scale * x, height / 2 - scale * y]
      return g.transition()
        .duration(duration)
        .attr("transform", `translate(${translate})scale(${scale})`)
        .ease(d3.easeCubicInOut)
    }

    // Run animation
    await sleep(250)

    let minX = Infinity, minY = Infinity, maxX = -Infinity, maxY = -Infinity
    indiaFeatures.forEach(feature => {
      const b = path.bounds(feature)
      if (b[0][0] < minX) minX = b[0][0]
      if (b[0][1] < minY) minY = b[0][1]
      if (b[1][0] > maxX) maxX = b[1][0]
      if (b[1][1] > maxY) maxY = b[1][1]
    })
    const indiaBounds = [[minX, minY], [maxX, maxY]]

    statusMessage.value = 'Zooming to India...'
    await zoomToBox(indiaBounds, 625).end()

    g.selectAll("path.country").transition().attr("stroke-width", 0.1)
    g.selectAll("path.state").transition().attr("stroke-width", 0.2)

    await sleep(125)
    statusMessage.value = 'Visualizing AQI Data...'

    const allDataKeys = Object.keys(AQI_DATA)

    for (const stateName of allDataKeys) {
      if (!isMounted) break

      const targetFeature = indiaFeatures.find(f => {
        const name = normalizeName(f.properties.NAME_1 || f.properties.st_nm || f.properties.name)
        return name === stateName
      })

      if (targetFeature) {
        const bounds = path.bounds(targetFeature)
        const aqiInfo = AQI_DATA[stateName]

        let fillColor = COLORS.land
        let glowColor = "transparent"

        if (aqiInfo.value > 300) {
          fillColor = COLORS.aqiRed
          glowColor = COLORS.glowRed
        } else if (aqiInfo.value > 100) {
          fillColor = COLORS.aqiYellow
          glowColor = COLORS.glowYellow
        } else {
          fillColor = COLORS.aqiGreen
          glowColor = COLORS.glowGreen
        }

        await zoomToBox(bounds, 375).end()

        const safeId = `state-${getSafeId(stateName)}`
        const stateSelection = g.select(`#${safeId}`)

        if (!stateSelection.empty()) {
          stateSelection.transition()
            .duration(125)
            .attr("fill", fillColor)
            .style("filter", `drop-shadow(0px 0px 10px ${glowColor})`)
          stateSelection.raise()
        }

        currentInfo.value = { name: stateName, data: aqiInfo, visible: true }
        await sleep(500)
        currentInfo.value = { ...currentInfo.value, visible: false }
      }
    }

    if (isMounted) {
      statusMessage.value = 'Overview'
      await zoomToBox(indiaBounds, 500).end()
      statusMessage.value = 'Animation Complete'
      await sleep(800)
      emit('animation-complete')
    }

  } catch (error) {
    console.error("Error initializing map:", error)
    statusMessage.value = "Error loading map data."
  }
}

watch(() => props.visible, (newVal) => {
  if (newVal && props.startAnimation) {
    initializeMap()
  }
})

watch(() => props.startAnimation, (newVal) => {
  if (newVal && props.visible) {
    initializeMap()
  }
})

onMounted(() => {
  isMounted = true
})

onUnmounted(() => {
  isMounted = false
})
</script>

