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
    <g
      v-for="child in menuItems"
      :key="child.id"
    >
      <polygon
        v-bind="child"
        :stroke-width="child.strokeWidth"
      />
      <component
        :is="child.icon.name"
        v-bind="child.icon"
      />
    </g>
    <rect
      @click="rootMenuClick"
      v-bind="menu.root"
      :stroke-width="menu.root.strokeWidth"
    />
    <Logo
      @click="rootMenuClick"
      :x = "menu.root.x + menu.root.width * 0.25 / 2"
      :y = "menu.root.y + menu.root.height * 0.25  / 2"
      :width = "menu.root.width * 0.75"
      :height = "menu.root.height * 0.75 "
    />
  </svg>
</template>

<script>
const loadIcon = (icon) => {
  return {
      [icon]: () => import(`~/assets/svg/icons/${icon}.svg`)
  }
}
import Logo from '~/assets/svg/ocm.svg'
const anime = require('animejs').default
export default {
  components: {
    Logo,
    ...loadIcon('list'),
    ...loadIcon('newspaper'),
    ...loadIcon('logo-github'),
    ...loadIcon('logo-facebook'),
    ...loadIcon('logo-slack'),
    ...loadIcon('logo-npm'),
    ...loadIcon('logo-twitter'),
    ...loadIcon('information-circle'),
    ...loadIcon('cloud-download')
  },
  data () {
    const colors = {
      one: '#3081FF',
      two: '#FF6049',
      three: '#FFBA30',
      four: '#9ECC12',
      five: '#6A4C93',
      six: '#184080',
      seven: '#7CAFFF',
      eight: '#FFFFFF',
      nine: '#333333'
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
          fill: colors.eight,
          stroke: colors.nine,
          rx: 100,
          strokeWidth: 7,
          children: [
            {
              id: 'menu1',
              square: [],
              fill: colors.one,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'list',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu2',
              square: [],
              fill: colors.two,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'newspaper',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu3',
              square: [],
              fill: colors.three,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'logo-github',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu4',
              square: [],
              fill: colors.four,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'logo-facebook',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu5',
              square: [],
              fill: colors.five,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'logo-slack',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu6',
              square: [],
              fill: colors.six,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'logo-npm',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu7',
              square: [],
              fill: colors.seven,
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'logo-twitter',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu8',
              square: [],
              fill: 'white',
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'information-circle',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
            },
            {
              id: 'menu9',
              square: [],
              fill: 'white',
              stroke: colors.nine,
              strokeWidth: 7,
              icon: {
                name: 'cloud-download',
                x: 0,
                y: 0,
                width: 0,
                height: 0
              }
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

        const width = this.grid.width / this.grid.side
        const height = this.grid.height / this.grid.side

        // Translated positions to the center
        const transX = x - (Math.ceil(this.grid.side / 2) - this.grid.side % 2 * 1)
        const transY = y - (Math.ceil(this.grid.side / 2) - this.grid.side % 2 * 1)

        //  console.log(`x: ${transX}, y: ${transY}`)
        
        //  Set the center of each square
        const centerX = transX * width + this.grid.x
        const centerY = transY * height + this.grid.y

        // high left corner positon
        const cornerX = centerX - width / 2
        const cornerY = centerY - height / 2

        //  console.log(`Center x: ${centerX}, Center y: ${centerY}`)       
        const square = [
          {
            x: centerX - width / 2,
            y: centerY - height / 2
          },
          {
            x: centerX + width / 2,
            y: centerY - height / 2
          },
          {
            x: centerX + width / 2,
            y: centerY + height / 2
          },
          {
            x: centerX - width / 2,
            y: centerY + height / 2
          }
        ]
        let points = ''
        square.forEach((point) => {
          points += (point.x + ',' + point.y + ' ')
          //  console.log(`Center x: ${point.x}, Center y: ${point.y}`)
        })

        this.grid.items.push({
          id: index,
          width,
          height,
          points,
          square,
          centerX,
          centerY,
          cornerX,
          cornerY
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
        //  fill: this.colors.one,
        //  stroke: this.colors.two,
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
          //  animation.fill = this.colors.three
          //  animation.stroke = this.colors.two
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
          child.icon.x = this.grid.items[child.position].cornerX
          child.icon.y = this.grid.items[child.position].cornerY
          child.icon.width = this.grid.items[child.position].width
          child.icon.height = this.grid.items[child.position].height
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
            //  fill: this.colors.three,
            //  stroke: this.colors.two,
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
        const iconAnimation = {
          targets: child.icon,
          x: this.grid.items[child.position].centerX,
          y: this.grid.items[child.position].centerY,
          width: 0,
          height: 0,
          easing: 'linear',
          duration: 200
        }
        anime(iconAnimation)

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
