<template>
  <svg class="diagram" viewBox="0 0 42 42">
    <circle :class="classCircleBack" :r="radius" cx="50%" cy="50%" />
    <circle
      ref="mainDiagram"
      class="front"
      :class="classCircleFront"
      :stroke-dasharray="dasharray"
      :stroke-dashoffset="dashoffset"
      :r="radius"
      cx="50%"
      cy="50%"
    />
    <circle
      v-if="satellite"
      class="satellite"
      r="1"
      cx="101%"
      cy="50%"
      :style="rotate"
    />
  </svg>
</template>

<script>
export default {
  name: "Diagram",
  props: {
    // Свойство которое принимает массив чисел, где:
    // нулевой элемент - это: длина отрезка для видимой части stroke-dasharray
    // элемент с индексом 1, это длина всего отрезка
    // например из 78 яблок продано 25, значит пропс должен принять [25, 78]
    dataDasharray: {
      type: Array,
      required: true,
    },
    // Радиус, не обязательный пропс, можно указывать в любой единице измерения
    radius: {
      type: String,
      default: "18",
    },
    // Кастомный css класс для стилизации фоновой фигуры круга
    classCircleBack: {
      type: String,
      default: "",
    },
    // Кастомный css класс для стилизации внешнего круга
    classCircleFront: {
      type: String,
      default: "",
    },
    // Если нужна фигура спутник
    satellite: {
      type: Boolean,
      default: false,
    },
    // Если true то dashoffset будет отменён для диаграммы таймера
    isTimer: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      dasharray: "0 100", // начальные данные псевдомассива отрезка
      dashoffset: "0", // Длина окружности для фигуры подложки
      radiusBaseVal: 0, // про эту переменную чуть ниже
      circumference: 0, // Длина окружности которую вычислим позже
    };
  },

  // Вычисляемые свойства для анимации спутника
  computed: {
    rotate() {
      // Поворот спутника относительно центра холста для инлайнового стиля
      return `transform: rotate(${this.degRotate}deg);`;
    },
    degRotate() {
      // Вычисляем градус поворота спутника, основываясь на пропсе dataDasharray
      const percent = Number(
        ((this.dataDasharray[0] * 100) / this.dataDasharray[1]).toFixed(1)
      );
      return (-360 * (percent / 100) - 90).toFixed(1);
    },
  },
  mounted() {
    this.radiusBaseVal = this.$refs.mainDiagram.r.baseVal.value;
    this.circumference = 2 * Math.PI * this.radiusBaseVal;
  },
  watch: {
    dataDasharray: {
      handler() {
        // вычисляем процентн данных из пропса dataDasharray
        const percent = (
          (this.dataDasharray[0] * 100) /
          this.dataDasharray[1]
        ).toFixed(1);
        // Сетим длину оффсетов для нашей диаграммы
        this.setLengthDasharray(percent, this.circumference);
      },
      deep: true, // Глубокое отслеживание пропса dataDasharray
      immediate: true, // запуск handler функции при mounted компонента
    },
  },
  methods: {
    setLengthDasharray(percent, circumference) {
      const offset = circumference - (percent / 100) * circumference;
      this.dasharray = `${offset} ${circumference}`;
      this.dashoffset = this.isTimer ? 0 : circumference.toFixed(3);
    },
  },
};
</script>
<style lang="scss" scoped>
.diagram {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  overflow: visible;
}
circle {
  fill: transparent;
  stroke: rgb(255, 255, 255);
  stroke-width: 0.8px;
  transform-origin: center;
  transform: rotate(-90deg);
  transition: stroke-dasharray 1s ease;
  z-index: 10;

  &.front {
    stroke: rgb(255, 255, 255);
  }
}

.satellite {
  fill: #fff;
  will-change: transform;
  stroke-width: 0.4px;
  transition: transform 1s ease;
}
</style>
