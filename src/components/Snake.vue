<script>
import Direction from './Direction.js'

// Helper functions to convert a percentage of canvas area to pixels.
const percentWidthToPix = (percent, ctx) => Math.floor((ctx.canvas.width / 100) * percent)
const percentHeightToPix = (percent, ctx) => Math.floor((ctx.canvas.height / 100) * percent)

export default {
  // Gets us the provider property from the parent <my-canvas> component.
  inject: ['provider'],

  props: {
    // Start coordinates (percentage of canvas dimensions).
    x1: {
      type: Number,
      default: 0
    },
    y1: {
      type: Number,
      default: 0
    },

    // End coordinates (percentage of canvas dimensions).
    x2: {
      type: Number,
      default: 0
    },
    y2: {
      type: Number,
      default: 0
    },

    // The color of the box.
    color: {
      type: String,
      default: '#F00'
    },

    // Direction of the next move
    direction: {
      type: String,
      default: Direction.RIGHT
    },

    // Tick id : for each new movement, the tick id is incremented
    tick: {
      type: Number,
      default: 0
    }

  },

  data () {
    return {
      // We cache the dimensions of the previous
      // render so that we can clear the area later.
      oldBox: {
        x: null,
        y: null,
        w: null,
        h: null
      }
    }
  },

  computed: {
    calculatedBox () {
      const ctx = this.provider.context

      // Turn start / end percentages into x, y, width, height in pixels.
      const calculated = {
        x: percentWidthToPix(this.x1, ctx),
        y: percentHeightToPix(this.y1, ctx),
        w: percentWidthToPix(this.x2 - this.x1, ctx),
        h: percentHeightToPix(this.y2 - this.y1, ctx)
      }

      // Yes yes, side-effects. This lets us cache the box dimensions of the previous render.
      // before we re-calculate calculatedBox the next render.
      this.oldBox = calculated
      return calculated
    }
  },

  render () {
    // Since the parent canvas has to mount first, it's *possible* that the context may not be
    // injected by the time this render function runs the first time.
    if (!this.provider.context) return null
    if (!this.cache) {
      this.cache = {
        tick: 0,
        direction: Direction.RIGHT,
        position: {
          x: 0,
          y: 0
        }
      }
    }
    if (this.direction !== this.cache.direction) {
      console.log('Snake render : changing direction to ' + this.direction)
      this.cache.direction = this.direction
    }

    if (this.tick !== this.cache.tick) {
      const distance = this.tick - this.cache.tick
      this.cache.tick = this.tick
      let { x, y } = this.cache.position
      for (let d = 1; d <= distance; d++) {
        if (this.cache.direction === Direction.RIGHT) {
          this.cache.position.x++
        } else if (this.cache.direction === Direction.LEFT) {
          x--
        } else if (this.cache.direction === Direction.UP) {
          y--
        } else if (this.cache.direction === Direction.UP) {
          y++
        }
      }
      console.log('Moving to ' + x + ',' + y)
    }

    return null
    /*
    const ctx = this.provider.context

    // Keep a reference to the box used in the previous render call.
    const oldBox = this.oldBox
    // Calculate the new box. (Computed properties update on-demand.)
    const newBox = this.calculatedBox

    ctx.beginPath()
    // Clear the old area from the previous render.
    ctx.clearRect(oldBox.x, oldBox.y, oldBox.w, oldBox.h)
    // Clear the area for the text.
    ctx.clearRect(newBox.x, newBox.y - 42, newBox.w, 100)

    // Draw the new rectangle.
    ctx.rect(newBox.x, newBox.y, newBox.w, newBox.h)
    ctx.fillStyle = this.color
    ctx.fill()

    // Draw the text
    ctx.fillStyle = '#000'
    ctx.font = '28px sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText(Math.floor(this.value), (newBox.x + (newBox.w / 2)), newBox.y - 14)
    return null */
  }
}
</script>
