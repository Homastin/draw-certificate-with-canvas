<template>
  <div class="cu-canvas-certificate" ref="canvasCert">
    <canvas :id="canvasId"
            :ref="canvasId"
    ></canvas>
  </div>
</template>

<script>
  /**
   * @author: Homastin
   * @date: 2020/3/25  10:54 上午
   * @Description: canvas 绘制电子证书
   */
  export default {
    name: 'canvas-certificate',
    props: {
      /**
       * @description canvasId
       */
      canvasId: {
        type: [String, Number],
        default: '',
        required: true,
      },
      /**
       * @description imageSrc
       */
      imageSrc: {
        type: String,
        default: '',
        required: true,
      },
      /**
       * @description canvas的状态 dead: 无操作，active: 操作中
       */
      remarkStatus: {
        type: String,
        default: 'dead',
      },
      /**
       * @description 选取结束的取消 确定工具
       * */
      confirmTool: {
        type: String,
        default: 'confirmTool',
      },
      /**
       * @description 涂鸦选取工具element (暂时停用)
       * */
      fixedTools: {
        type: String,
        default: 'cuFixedTool',
      },
      /**
       * @description 画笔类型
       * */
      strokeType: {
        type: String,
        default: 'rect',
      },
      /**
       * @description 画笔颜色
       * */
      strokeColor: {
        type: String,
        default: '#fb3838',
      },
      /**
       * @description 画笔大小
       * */
      strokeWidth: {
        type: [String, Number],
        default: 2,
      },
      /**
       * @description 填充颜色
       * */
      fillStyle: {
        type: [String],
        default: 'rgba(212,76,68,0.5)',
      },
      /**
       * @description: 点击标注
       */
      labelPos: {
        type: Array,
        default: () => ([]),
      },
      /**
       * @description: 详情默认带的标注
       */
      defaultPos: {
        type: Array,
        default: () => ([]),
      },
      flawFlag: {
        type: String,
        default: '',
      },
      isDrawSuccess: {
        type: Boolean,
        default: true,
      },
    },
    data() {
      return {
        canvasDom: {},
        canvasCtx: {},
        canvasState: {},
        canvasClientRect: {},
        imageData: {},
        scale: 1,
        textContent: [
          {
            value: '电子数据存证证书',
            x: 1500,
            y: 1500,
            font: '48px bold',
            color: ''
          },
          {
            value: '8888888888888',
            x: 1800,
            y: 1800,
            font: '26px Helvetica',
            color: '#376CD9'
          },
          {
            value: '一、存证主体',
            content: [
              {
                value: '企业名称:背景腾讯科技有限公司',
                x: 500,
                y: 2700,
                font: '14px Helvetica',
                color: '#000000'
              },
              {
                value: '统一信用代码:312312312312312',
                x: 500,
                y: 3100,
                font: '14px Helvetica',
                color: '#000000'
              },
              {
                value: '申请人:麻花藤',
                x: 500,
                y: 3400,
                font: '14px Helvetica',
                color: '#000000'
              },
              {
                value: '身份证:321323199912224933',
                x: 500,
                y: 3700,
                font: '14px Helvetica',
                color: '#000000'
              }
            ],
            font: '16px Helvetica',
            color: '#000000',
            x: 500,
            y: 2400,
          },
        ]
      };
    },
    methods: {
      createCanvas() {
        this.canvasDom = this.$refs[this.canvasId];
        this.canvasCtx = this.canvasDom.getContext('2d');
        // canvas 填充一张图片
        const image = new Image();
        image.src = this.imageSrc;

        image.onload = () => {
          const rect = this.calcImageTrulySize(image);
          this.canvasDom.width = rect.width;
          this.canvasDom.height = rect.height;
          this.canvasCtx.drawImage(image, 0, 0, this.canvasDom.width, this.canvasDom.height);
          this.canvasClientRect = { ...this.computedCanvasClientRect() };
          this.imageData = this.canvasCtx.getImageData(0, 0,
            this.canvasClientRect.width,
            this.canvasClientRect.height);
          this.handleDrawText();
          this.createSeal('北明软件有限公司','1111')
        };
      },
      // 渲染文本
      handleDrawText() {
        const textContent = this.textContent;

        textContent.forEach((val, idx, arr) => {

          if (val.content && val.content.length) {
            val.content.forEach((childVal, childIdx, childArr) => {
              this.drawText(childVal);
            });
          }
          this.drawText(val);
        });
      },
      // 绘制文本
      drawText({ font, color = '#041941', x, y, value }) {
        this.canvasCtx.beginPath();
        this.canvasCtx.fillStyle = color;
        this.canvasCtx.font = font;
        this.canvasCtx.fillText(value, x / this.scale, y / this.scale);
        this.canvasCtx.closePath();
        // this.canvasCtx.save();
        this.canvasCtx.restore();
      },
      // 计算canvas 容器的大小
      calcImageTrulySize(image) {
        this.resetScale();

        const { width } = image;
        const { height } = image;
        const wrapperRect = this.$refs.canvasCert.getBoundingClientRect(); // 返回元素的大小及其相对于视口的位置。

        if (width <= wrapperRect.width) {
          return {
            width,
            height,
          };
        }

        this.scale = width / wrapperRect.width;
        return {
          width: wrapperRect.width,
          height: height / this.scale,
        };
      },
      // 计算canvas容器在页面视图中的 getBoundingClientRect
      computedCanvasClientRect() {
        const canvas = this.canvasDom;
        const rect = canvas.getBoundingClientRect();

        return {
          width: canvas.width,
          height: canvas.height,
          offsetWidth: canvas.offsetWidth,
          offsetHeight: canvas.offsetHeight,
          top: rect.top,
          left: rect.left,
        };
      },
      createSeal(company, name) {
        let context = this.canvasCtx;

        // 绘制印章边框
        let arcX = this.canvasClientRect.width-150;
        let arcY = this.canvasClientRect.height-150;
        context.lineWidth = 7;
        context.strokeStyle = '#f00';
        context.beginPath();
        context.arc(arcX, arcY, 110, 0, Math.PI * 2);
        context.stroke();

        //画五角星
        this.create5star(this.canvasCtx, arcX, arcY, 30, '#f00', 0);

        // 绘制印章名称
        context.font = '22px Helvetica';
        context.textBaseline = 'middle';//设置文本的垂直对齐方式
        context.textAlign = 'center'; //设置文本的水平对对齐方式
        context.lineWidth = 1;
        context.fillStyle = '#f00';
        context.fillText(name, arcX, arcY + 65);

        // 绘制印章单位
        context.translate(arcX, arcY);// 平移到此位置,
        context.font = '30px Helvetica';
        let count = company.length;// 字数
        let angle = 4 * Math.PI / (3 * (count - 1));// 字间角度
        let chars = company.split('');
        let c;
        for (let i = 0; i < count; i++) {
          c = chars[i];// 需要绘制的字符
          if (i === 0) {
            context.rotate(5 * Math.PI / 6);
          } else {
            context.rotate(angle);
          }
          context.save();
          context.translate(90, 0);// 平移到此位置,此时字和x轴垂直
          context.rotate(Math.PI / 2);// 旋转90度,让字平行于x轴
          context.fillText(c, 0, 5);// 此点为字的中心点
          context.restore();
        }
      },
      //绘制五角星
      /**
       * 创建一个五角星形状. 该五角星的中心坐标为(sx,sy),中心到顶点的距离为radius,rotate=0时一个顶点在对称轴上
       * rotate:绕对称轴旋转rotate弧度
       */
      create5star(context, sx, sy, radius, color, rotato) {
        context.save();
        context.fillStyle = color;
        context.translate(sx, sy);//移动坐标原点
        context.rotate(Math.PI + rotato);//旋转
        context.beginPath();//创建路径
        let x = Math.sin(0);
        let y = Math.cos(0);
        let dig = Math.PI / 5 * 4;
        for (let i = 0; i < 5; i++) {//画五角星的五条边
          let x = Math.sin(i * dig);
          let y = Math.cos(i * dig);
          context.lineTo(x * radius, y * radius);
        }
        context.closePath();
        context.stroke();
        context.fill();
        context.restore();
      },


      // 重置缩放比列
      resetScale() {
        this.scale = 1;
      },
      init() {
        this.createCanvas();
      },
    },
    mounted() {
      this.init();
    },
  };
</script>

<style lang="scss">

</style>
