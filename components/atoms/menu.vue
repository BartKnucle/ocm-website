<template>
  <svg
    v-bind="svg"
  >
    <!-- <polygon
      v-for="item in grid.items"
      :key="item.id"
      v-bind="item"
      fill='white'
    /> -->
     <polygon
      v-for="child in menuItems"
      :key="child.id"
      v-bind="child"
      :stroke-width="child.strokeWidth"
    />
    <rect
      @click="rootMenuClick"
      v-bind="menu.root"
      :stroke-width="menu.root.strokeWidth"
    />
  </svg>
</template>

<script>
const anime = require('animejs').default
export default {
  data () {
    const colors = {
      one: '#FF595E',
      two: '#FFCA3A',
      three: '#1982C4',
      four: '#8DA210',
      five: '#6A4C93'
    }

    return {
      colors,
      svg: {
        width: '100%',
        height: '100%',
        viewBox: '0 0 1000 1000'
      },
      grid: {
        x: 500,
        y: 500,
        width: 600,
        height: 600,
        side: 0,
        count: 0,
        items: []
      },
      menu: {
        x: 500,
        y: 500,
        width: 300,
        height: 300,
        root: {
          state: 'start',
          x: 500,
          y: 500,
          width: 100,
          height: 100,
          fill: colors.one,
          stroke: colors.two,
          rx: 100,
          strokeWidth: 7,
          children: [
            {
              id: 'menu1',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu2',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu3',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu4',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu5',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu6',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu7',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu8',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            },
            {
              id: 'menu9',
              square: [],
              fill: 'white',
              stroke: colors.two,
              strokeWidth: 7
            }
          ]
        }
      }
    }
  },
  computed: {
    menuItems () {
      return this.menu.root.children.map((child) => {
        if (child.square) {
          child.points = ''
          child.square.forEach((point) => {
            child.points += (point.x + ',' + point.y + ' ')
          })
        } else {
          child.points = '0,0 0,0'
        }
        return child
      })
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.getWindowSize()
    })
    window.addEventListener('resize', this.getWindowSize)
    this.setGrid()
    this.positionMenu()
  },
  methods: {
    setGrid () {
      this.grid.side = Math.ceil(Math.sqrt(this.menu.root.children.length + 1))
      this.grid.count = this.grid.side ** 2
      //  let itemWidth = this.menu.width / this.grid.side
      //  let itemHeight = this.menu.height / this.grid.side
      for (let index = 0; index < this.grid.count; index++) {
        // x and y position from the left up corner
        const x = index % this.grid.side
        const y = Math.floor(index / this.grid.side)

        // Translated positions to the center
        const transX = x - (Math.ceil(this.grid.side / 2) - this.grid.side % 2 * 1)
        const transY = y - (Math.ceil(this.grid.side / 2) - this.grid.side % 2 * 1)

        //  console.log(`x: ${transX}, y: ${transY}`)

        //  Set the center of each square
        const centerX = transX * this.grid.width / this.grid.side + this.grid.x
        const centerY = transY * this.grid.height / this.grid.side + this.grid.y

        //  console.log(`Center x: ${centerX}, Center y: ${centerY}`)
        const square = [
          {
            x: centerX - this.grid.width / this.grid.side / 2,
            y: centerY - this.grid.height / this.grid.side / 2
          },
          {
            x: centerX + this.grid.width / this.grid.side / 2,
            y: centerY - this.grid.height / this.grid.side / 2
          },
          {
            x: centerX + this.grid.width / this.grid.side / 2,
            y: centerY + this.grid.height / this.grid.side / 2
          },
          {
            x: centerX - this.grid.width / this.grid.side / 2,
            y: centerY + this.grid.height / this.grid.side / 2
          }
        ]
        let points = ''
        square.forEach((point) => {
          points += (point.x + ',' + point.y + ' ')
          //  console.log(`Center x: ${point.x}, Center y: ${point.y}`)
        })

        this.grid.items.push({
          id: index,
          points,
          square,
          centerX,
          centerY
        })
      }
    },
    positionMenu () {
      const positions = []

      const setPosition = () => {
        let position = null
        let exist = false
        do {
          position = this.getRandomIntInclusive(0, this.grid.items.length - 1)
          exist = positions.find(pos => pos === position)
        } while (exist !== undefined)
        positions.push(position)
        return position
      }

      // Set the root position in the grid
      this.menu.root.position = setPosition()
      this.menu.root.x = this.grid.items[this.menu.root.position].square[0].x
      this.menu.root.y = this.grid.items[this.menu.root.position].square[0].y
      this.menu.root.width = this.grid.items[this.menu.root.position].square[1].x - this.grid.items[this.menu.root.position].square[0].x
      this.menu.root.height = this.grid.items[this.menu.root.position].square[3].y - this.grid.items[this.menu.root.position].square[1].y

      //  Set the childrens position in the grid
      this.menu.root.children.forEach((child) => {
        child.position = setPosition()
        //  child.square = this.grid.items[child.position].square
      })
    },
    getWindowSize () {
      this.svg.width = window.innerWidth
      this.svg.height = window.innerHeight
    },
    getRootPoints (index) {
      const points = [
        {
          x: this.menu.root.x,
          y: this.menu.root.y
        },
        {
          x: this.menu.root.x,
          y: this.menu.root.y + this.menu.root.height
        },
        {
          x: this.menu.root.x + this.menu.root.width,
          y: this.menu.root.y + this.menu.root.height
        },
        {
          x: this.menu.root.x + this.menu.root.width,
          y: this.menu.root.y
        }
      ]
      return points[index]
    },
    rootMenuClick () {
      const animation = {
        targets: this.menu.root,
        rx: 0,
        fill: this.colors.one,
        stroke: this.colors.two,
        easing: 'linear',
        duration: 200,
        begin: (anim) => {},
        complete: (anim) => {
          this.menu.root.state = 'end'
          this.startChilds()
        }
      }

      switch (this.menu.root.state) {
        case 'start':
          anime(animation)
          break
        case 'end':
          animation.fill = this.colors.three
          animation.stroke = this.colors.two
          animation.rx = 100
          animation.complete = (anim) => {
            this.menu.root.state = 'start'
            this.endChilds()
          }
          anime(animation)
          break
        default:
          break
      }
    },
    getRandomIntInclusive (min, max) {
      min = Math.ceil(min)
      max = Math.floor(max)
      return Math.floor(Math.random() * (max - min + 1)) + min
    },
    startChilds () {
      this.menu.root.children.forEach((child) => {
        if (!child.status || child.status !== 'start') {
          child.square = []
          for (let index = 0; index < this.grid.items[child.position].square.length; index++) {
            child.square.push({
              x: this.grid.items[child.position].centerX,
              y: this.grid.items[child.position].centerY
            })
          }

          for (let index = 0; index < this.grid.items[child.position].square.length; index++) {
            const animation = {
              targets: child.square[index],
              x: this.grid.items[child.position].square[index].x,
              y: this.grid.items[child.position].square[index].y,
              easing: 'easeOutElastic(1, .7)',
              duration: 200
            }
            anime(animation)
          }

          const animation = {
            targets: child,
            fill: this.colors.three,
            stroke: this.colors.two,
            easing: 'linear',
            duration: 200
          }
          anime(animation)

          child.status = 'start'
        }
      })
    },
    endChilds () {
      this.menu.root.children.forEach((child) => {
        child.status = 'end'
        for (let index = 0; index < this.grid.items[child.position].square.length; index++) {
          const animation = {
            targets: child.square[index],
            x: this.grid.items[child.position].centerX,
            y: this.grid.items[child.position].centerY,
            easing: 'linear',
            duration: 200
          }
          anime(animation)
        }
      })
    }
  }
}
</script>

<style lang="scss">
.menu {
  fill: $color2;
}
</style>
