### 1、*暂时性死区*

```
    在es6中，let、const跟class同样也存在变量提升，只是在进入作用域和被声名之间有一段时间他们是不能被访问的
```

### 2、*web优化图片相关*

#### 请求优化

1. 懒加载: 监听scroll事件,当图片img进入可视区域时再进行图片加载；（页面的scrollTop加上浏览器的可视高度 大于 img标签距离顶部的距离）
    ##### 解决方案
    1. load.js
        ```
            npm install --save lozad

            <img class="lozad" data-src="image.png" />

            const observer = lozad(); // 默认会去找 .lozad class
            observer.observe();
        ```
    2. vue-lazyload
        ```
            npm i vue-lazyload -S

            Vue.use(VueLazyload)
        ```
    3. react-lazyload
        ```
            npm i react-lazyload -S

            import React from 'react';
            import ReactDOM from 'react-dom';
            import LazyLoad from 'react-lazyload';
            import MyComponent from './MyComponent';

            const App = () => {
            return (
                <div className="list">
                <LazyLoad height={200}>
                    <img src="tiger.jpg" />
                </LazyLoad>
                <LazyLoad>
                    <MyComponent />
                </LazyLoad>
                </div>
            );
            };

            ReactDOM.render(<App />, document.body);
        ```

    


2. 正确使用缓存: 保存资源副本，再下次请求时直接使用资源副本（强缓存和协商缓存）
    ##### 解决方案
    Cache-Control
    指令|说明
    --|:--:|
    max-age|指令指定从请求的时间开始，允许提取的响应时间被重用的最长时间（单位: 秒）
    private|只允许单个缓存，不允许任何中间缓存对其进行缓存
    no-cache|先与服务器确认返回的响应是否发生改变，走协商缓存
    no-store|禁止浏览器以及所有的中间缓存存储任何版本的返回响应

3. 雪碧图: 将小图片合并成一张图片，利用background-position属性值来确定图片呈现的位置
   ##### 解决方案
   1. webpack-spritesmith: 合并精灵图，并计算每张图片的相对位置，可用于定位
    ```
    const SpritesmithPlugin = require('webpack-spritesmith');
    module.exports = {
        resolve: {
            modules: ["node_modules", "spritesmith-generated"]
        },
        plugins: [
            new SpritesmithPlugin({
                src: {
                    cwd: path.resolve(__dirname, 'src/ico'),
                    glob: '*.png'
                },
                target: {
                    image: path.resolve(__dirname, 'src/spritesmith-generated/sprite.png'),
                    css: path.resolve(__dirname, 'src/spritesmith-generated/sprite.styl')
                },
                apiOptions: {
                    cssImageRef: "~sprite.png"
                }
            })
        ]
    }
    ```
   2. gulp.spritesmith

4. iconfont: 通过字体的方式展示图标，多用于简单徒刑，特殊字体等；

5. base64: 是网络最常见的用于传输8Bit字节码的编码方式之一；可将图片编码为特殊的字符串，由52个大小写字母和19个数字，以及+, /, =三个字符组成
   ##### 优点
   1. 有效减少HTTP的请求次数
   2. 对数据进行简单加密，无法肉眼获取信息
   3. 没有跨域问题，无需考虑图片缓存
   ##### 缺点
   1. 编码后文件体积增大，base64仅适用于小体积的图片编码
   2. 增加的图片编码和解码的工作量
   3. 不支持IE8.0以下版本

#### 体积优化

1. [tinypng](https://tinypng.com/): 使用智能有损压缩减少png文件的大小
2. [智图](https://zhitu.isux.us/): 可选择的图片的压缩质量
3. [iloveimg](https://www.iloveimg.com/zh-cn): 支持压缩JPG,PNG,GIF,能保持最佳的文件质量和压缩程度
   
#### 格式尺寸优化
1. 图片格式
   
    *常用格式: gif, png, jpg, webp*
    
    ![使用场景](https://user-gold-cdn.xitu.io/2019/11/10/16e552e821433148?imageslim)

2. 多倍图
   
   *像素概念*
   1. DP: 设备像素，又称为物理像素，即设备屏幕上真是的物理像素，以矩阵的方式进行排列，例如: iphone X 屏幕分辨率为2436*1125，即屏幕每行包含1125个物理像素，每列包含2436个物理像素。
   2. DIP: 设备无关像素，是一种基于屏幕坐标的的抽象像素，应用程序以抽象像素作为单位，如css中的px，实际渲染时通过底层程序为物理像素。
   3. DPR: 设备像素比，设备像素 / 设备无关像素（DP / DIP）的值即为设备像素比，在 Javascript 中可以通过 window.devicePixelRatio 来获取。
   *自适应DPR加载图片*
   ```
        // picture标签
        <picture>  
            <source srcset="photo@3x.jpg" media="(min-width: 800px)">  
            <source srcset="photo@2x.jpg" media="(min-width: 600px)">  
            <img srcset="photo.jpg">  
        </picture>

        // img srcset属性
        <img src="photo.png" srcset="photo@2x.png 2x, photo@3x.png 3x" alt="photo" />

        // css3中 img-set函数（兼容性较差）
        background-image: image-set("photo.png" 1x,
                            "photo@2x.png" 2x,
                            "photo@3x.png" 3x);
 
   ```

### 3、单元测试学习

    ```
        # jest插件安装
        vue add @vue/cli-plugin-unit-jest 
        # 包含了安装和调试两个过程，把相关的依赖一并安装
        # 单元测试文件: 后缀为: .spec.js 或 .test.js
    ```

#### 四个基础单词
   * describe 定义一个测试套件
   * it 定义一个测试用例
   * expect 断言的判断条件
   * toEqual 断言的比较结果
        ```
            descript('test ...', () => {
                it('should ...', function() {
                    expect(sth).toEqual(sth)
                    expect(sth.length)toEqual(1)
                    expect(sth > oth).toEqual(true)
                })
            })
        ```
   * 常用的断言语句
       ```
           toBe()----测试具体的值
           toEqual()----测试对象类型的值
           toBeCalled()----测试函数被调用
           toHaveBeenCalledTimes()----测试函数被调用的次数
           toHaveBeenCalledWith()----测试函数被调用时的参数
           toBeNull()----结果是null
           toBeUndefined()----结果是undefined
           toBeDefined()----结果是defined
           toBeTruthy()----结果是true
           toBeFalsy()----结果是false
           toContain()----数组匹配，检查是否包含
           toMatch()----匹配字符型规则，支持正则
           toBeCloseTo()----浮点数
           toThrow()----支持字符串，浮点数，变量
           toMatchSnapshot()----jest特有的快照测试
           .not.+matcher，eg. .not.toBe()----前面加上.not就是否定形式，
       ```
  * 挂载组件    
      * 通过mount方法来创建包裹器，包裹起会暴露很多封装、遍历和查询其内部的Vue组件实例的便捷方法
        ```
            import { mount } from '@vue/test-utils'
            import Counter from './counter'
            describe('Counter', () => {
                // 现在挂载组件，你便得到了这个包裹器
                const wrapper = mount(Counter)

                it('renders the correct markup', () => {
                    expect(wrapper.html()).toContain('<span class="count">0</span>')
                })

                // 也便于检查已存在的元素
                it('has a button', () => {
                    expect(wrapper.contains('button')).toBe(true)
                })
            }
        ```
      * shallowMount: 浅渲染（只挂载一个组件而不渲染其子组件，即保留它们的存根）

        ```
            import { shallowMount } from '@vue/test-utils'

            const wrapper = shallowMount(Component)
            wrapper.vm // 挂载的 Vue 实例
        ```
#### 常用的单元测试写法
   1. Snapshot测试: 测试整体的html有没有更改，每一个文件都应该进行该测试
       * 更改html后，该测试会失败，删除保存在__snapshots__文件内对应的.snap文件后，重新测试
            ```
                # 测试内容: snapshot->概括的测试DOM结构
                it('matches snapshot', () => {
                    const wrapper = shallowMount(Count)
                    expect(wrapper.html()).toMatchSnapshot()
                    wrapper.destroy()
                })
            ```
   2. 精确DOM结构测试
       * 断言指定DOM节点对象是否存在，视项目情况而定需不需要进行测试
            ```
                # 精确DOM结构测试示例
                it('DOM test', () => {
                    const wrapper = shallowMount(Count)
                    expect(wrapper.contains('button')).toBeTruthy()
                    wrapper.destroy()
                })
            ```
   3. v-on
       * 以click事件为例
       * 测试思路: 点击，断言对应函数是否为触发，触发几次，参数是否为预期参数
       * 注意被断言的函数必须为mock函数，不然会报错
            ```
                # 测试内容: func测试，
                # 点击组件按钮时，正确触发点击事件
                it('click button onClick is called', () => {
                    const wrapper = shallowMount(Count)
                    # 创建mock函数
                    const mockFn = jest.fn()
                    # 设置 Wrapper vm实例方法并强制更新
                    wrapper.setMethods({
                        # 注意: 此处的onClick为 button元素@click绑定的事件名，即覆盖了button按钮上的原有点击事件
                        onClick: mockFn
                    })
                    # 发现button DOM元素
                    const button = wrapper.find('button')
                    # 测试点击按钮事件有没有正确被触发
                    button.trugger('click')
                    # 断言函数被触发，且被触发一次
                    expect(mockFn).toBeCalled()
                    expect(mockFn).toHaveBeenCalledTimes(1)

                    wrapper.destroy()
                })
            ```
   4. v-bind
       * 大多数情况下可以不写此测试
            ```
                # 测试内容 data v-bind
                # 断言data中的变量值
                it('data test', () => {
                    const wrapper = shallowMount(Count)
                    expect(wrapper.vm.name).toBe('wangyiyang')
                    expect(wrapper.vm.isShow).toBeFalsy()

                    # 更改值后
                    wrapper.vm.name = 'muzi'
                    expect(wrapper.find('p').text()).toBe('muzi')
                })
            ```
   5. v-slot
       * slots分为普通插槽、具名插槽、和作用域插槽
            ```
                # 测试内容: slots 普通插槽
                # 测试默认值 （当slots有默认值时）
                it('slots default value test', () => {
                    const wrapper = shallowMount(Count)
                    const button = wrapper.find('button')
                    expect(button.text()).toBe('submit')
                    wrapper.destroy()
                })

                # 测试内容: slots普通插槽
                # mount时传入自定义内容作为slots，然后在断言自定义的内容存在与否
                # 自定义的内容可能是text、html、components等允许的内容
                it('slots test', () => {
                    const wrapper = shallowMount(Count, {
                        slots: {
                            # 自定义默认插槽slots的默认值
                            # 自动将默认值填充到<slot></slot>中
                            default: 'i am a slots text'
                        }
                    })
                    const button = wrapper.find('button')
                    expect(button.text()).toBe(i am a slots text)
                    wrapper.destroy()
                })

                # 测试内容: slots具名插槽
                # 当传入组件时，只需断言wrapper中是否包含组件的DOM元素即可
                # 例: expect(wrapper.contains('.container')).toBeTruthy()
                it('name slots test', () => {
                    const wrapper = shallowMount(Count, {
                        slots: {
                            # 注意: 此处的namedSlots即为插槽的name
                            # <slot name="namedSlots"></slot>
                            namedSlot: `<span>i am a slots html</span>`
                        }
                    })
                    const button = wrapper.find('button')
                    expect(button.contains('span')).isBeTruthy()

                    const span = wrapper.find('button span')
                    expect(span.text()).toBe('i am a slots html')

                    wrapper.destroy()
                })

                # 测试内容: 作用域插槽
                # scopedSlots
                it('scoped slots test', () => {
                    const wrapper = shallowMount(Count, {
                        # scopeSlots 作用域插槽
                        scopeSlots: {
                            # 自定义slots内容
                            # <slot name="scopedSlot" :user="{name: 'wangyiyang'}"></slot>
                            # 注意: 此处的slot-scoped包裹了 为slot传递的props中的user
                            scopedSlot: '<span slot-scope="foo">{{foo.user.name}}</span>'
                        }
                    })

                    cconst button = wrapper.find('button')
                    expect(button.contains('span')).toBeTruthy()

                    const span = wrapper.find('button span')
                    expect(span.text()).toBe('wangyiyang')

                    wrapper.destroy()
                })
            ```        
   6. v-if/v-show
       * 断言对应的DOM显示与否
            ```
                # 设置变量的值，断言对应的结构显示与否
                it('v-show test', () => {
                    const wrapper = shallowMount(Count)
                    # 为true时div显示
                    const trueDiv = wrapper.find('.true_div')
                    # 为false时div隐藏
                    const falseDiv = shallowMount('.false_div')

                    expect(trueDiv.isVisible()).toBeTruthy()
                    expect(falseDiv.isVisible()).toBeFalsy()

                    wrapper.vm.isShow = false

                    expect(trueDiv.isVisible()).toBeFalsy()
                    expect(falseDiv.isVisible()).toBeFalsy()

                    wrapper.destroy()
                })
            ```
   7. filter过滤器
       * filter不能通过wrapper或者vm进行获取只能通过组件进行获取
       * filter需要测试函数的所有可能性
            ```
                # filter需测试函数的所有可能性
                it('filter test', () => {
                    # console.log(Count.filters) # 通过组件获取过滤器
                    # 例如: 格式化数字过滤器
                    expect(Count.filters.formatNum('123456').toBe('123,456'))
                    expect(Count.filters.formatNum('12345678').toBe('12,345,678'))
                    expect(Count.filter.formatNum('123').toBe('123'))
                })
            ```
   8. props
       * 注意: props可能存在默认值的情况
            ```
                # 自定义props传递给组件，判断组件获取到的props是否一致
                it('props test', () => {
                    const countProps = {
                        type: 'primary',
                        size: 10,
                        disabled: true
                    }
                    const wrapper = shallowMount(Count, {
                        # 传递组件props
                        propsData: countProps
                    })

                    # 断言组件已经获取到了props
                    expect(wrapper.props().type).toBe('primary')
                    expect(wrapper.props().size).toBe(10)
                    expect(wrapper.disabled).toBeTruthy()

                    wrapper.destroy()
                })
            ```
   9. Methods (func函数测试)
       * 主动触发函数，断言执行接口是否符合预期
            ```
                # methods方法测试，断言函数的执行结果
                it('methods test', () => {
                    const wrapper = shallowMount(Count)
                    # 手动更改 isShow 变量为false
                    wrapper.vm.isShow = false
                    # 手动调用changeIsShow()方法，更改isShow变量的值
                    wrapper.vm.changeIsShow()

                    # 对变量isShow变量值进行断言
                    expect(wrapper.vm.isShow).toBeTruthy()

                    # 再次调用changeIsShow方法
                    wrapper.vm.changeIsShow()

                    # 再次断言
                    expect(wrapper.vm.isShow).toBeFalsy()

                    wrapper.destroy()
                })
            ```
   10. $emit
       * 子组件内触发父组件内的函数，组件结构越复杂，使用的越多
            ```
                # 注意: 包含$emit的函数被触发后，emit的函数也会被触发
                it('when onClick is called $emit is called', () => {
                    const wrapper = shallowMount(Count)
                    # 测试 $emit被正确触发
                    # mock函数代替点击按钮后$emit的函数
                    const mockFn = jest.fn()
                    # 绑定$emit事件 <Child @changeName="...">
                    wrapper.vm.$on('changeName', mockFn)

                    # mock函数被触发(触发组件实例上的methdos方法)
                    # 触发次数及入参
                    wrapper.vm.onClick() # 注意: 这里的onClick函数为实例上methods上的onClick函数 {methods: {onClick() { ... }}}
                    expect(mockFn).toBeCalled()
                    expect(mockFn).toHavaBeenCalledTimes(1)
                    expect(mockFn).toHaveBeenCalledWith('params')

                    # 二次触发事件
                    wrapper.vm.onClick()
                    expect(mockFn).toBeCalled()
                    expect(mockFn).toHaveBeenCalledTimes(2)
                    expect(mockFn).toHaveBeenCalledWith('params')

                    wrapper.destroy()
                })
            ```
   11. watch
       * 更改watch中监听data里的值，断言是否与预期相同
            ```
                it('watch test', () => {
                    const wrapper = shallowMount(Count)
                    const spy = jest.spyOn(console, 'log')
                    wrapper.vm.name = 'muzi'

                    # 断言mock函数是否执行    
                    expect(spy).toBeCalled()
                    expect(spy).toHaveBeenCalledTimes(1)
                    expect(spy).toHaveBeenCalledWith('muzi')

                    # 清楚调mock
                    spy.mockClear()

                    wrapper.destroy()
                })
            ```
   