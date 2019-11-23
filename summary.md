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

            <img class="lozad" data-src="image.png" />◊

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
### 3、git的常用操作指北

1. *配置用户信息*
    ```
        git config --global user.name '用户名'
        git config --global user.email '邮箱地址'
    ```
2. *基本提交操作*
    ```
        git init
        git add README.md
        git commit -m '版本信息'
        git remote add origin 'git地址'
        git push -u origin master
    ```
3. *版本控制*
    * 工作区和缓存区
        * 工作区: 
            * 电脑上的文件目录
        * 版本库: 
            * .git即git的版本库
                * 最重要的就是Index即暂存区
                * git自动为我们创建的第一个分支master，以及指向master的一个指针HEAD
            * git add 把文件修改添加到暂存区
            * git commit 提交更改，把暂存区的所有内容提交到当前分支（默认会提交到master分支上）
            * git status 查看当前仓库的状态
    * 版本回退
        * 可将每次commit理解为一次快照
        * git log 查看我们提交的历史记录（包含: 版本号、用户名、日期及版本描述等信息）
        * git log --pretty=oneline --abbrev-commit
        * git log --oneline --graph
        * commit后面的一串十六进制字符就是每次提交的版本号
        * git reset 进行版本回退操作
            * git reset --hard HEAD^
                * HEAD表当前版本，上一版本就是HEAD^,上俩版本是HEAD^^, HEAD~30回退30个版本
            * git reset --hard commit_id
                * **注意: 当执行git reset 进行版本回退之后，之前最新的版本就无法通过 git log查询到，此时就需要使用 git reflog 命令查询git的操作记录，获取到commit_id**
        * 重置命令（git reset [--hard|soft|mixed|merge|keep] [commit_id|HEAD]）
            * --hard 重设暂存区和工作区，从<commit>以来在工作区的任何改变都被丢弃，并把HEAD指向<commit>。**彻底会退到某个版本，本地源码也会变为commit_id版本的内容**
            * --soft 工作区的内容不做任何改变，更改会回退到暂存区中。即分支上的内容，会会退到暂存区。**会退到某个版本，只回退了commit的信息，如果还要提交，直接commit即可**
            * --mixed 仅重设"暂存区"，本地文件不受影响，该模式是默认模式。**回退到某个版本，只保留源码，回退commit和index信息**
                * --mixed可以进行文件粒度操作， 而 --hard和 --soft则不行
            * Reset常用示例
                ```
                    git add test
                    git reset HEAD test
                    # 将test从暂存区状态回滚到制定的commit 时的暂存区状态
                ```
                ```
                    git add test
                    git commit -m '版本信息'
                    git reset --soft HEAD^
                    # 将test从 "已提交" 状态变为 "已暂存" 状态
                ```
                ```
                    git branch topic # 已当前分支为基础，新建分支topic
                    git reset --hard HEAD~2 # 在当前分支上回滚提交两个版本
                    git checkout topic
                    # 通过临时分支来保留提交，然后在当前分支上硬回滚
                ```
                ```
                    git reset 497e350
                    # 当前HEAD会指向 "497e350",暂存区中的状态会恢复到提交 "497e350"时暂存区的状态。
                ```
    * 撤销修改
        * git checkout -- <file> 需要丢弃工作区某些文件修改时，可以使用**该命令仅会恢复工作区文件状态，不会对版本库有任何改动**
        * 如果文件已commit到暂存区，会回到暂存区的状态
        * 如果文件未提交，则会和版本库的状态一模一样

    * 删除文件
        ```
            git rm test.txt # 从版本库中删除
            rm test.txt # 本地文件删除
            git commti -m '版本信息' # 版本提交
        ```
4. 分支管理
    * git branch / git checkout 创建分支及切换分支
        ```
            git checkout -b dev
            # 创建dev分支，然后切换到dev分支
        ```
        * -b 参数表切换并创建， 相当于
        ```
            git branch dev
            git checkout dev
        ```
    * git branch 查看所有分支，会在当前分支前加上 * 号
    * git branch -d dev 删除dev分支
5. 分支策略管理
    * 如果可能，git会用 Fast forward 模式，但这种模式下，删除分支后，会丢掉分支信息
    * --no-ff 强制禁用Fast forawrd模式，git就会在merge时生成一个新的commit，从分支历史上就可以看出分支信息
        ```
            git merge --no-ff -m 'merge with no-ff' dev
            # 禁用Fast forward模式，由于本次合并要创建一个新的commit，所以加上 -m 参数，写入commit描述信息
        ```
6. git fetch
    ```
        git fetch origin master:temp 
        # 本地新建一个temp分支，并将远程origin仓库的master分支代码下载到本地temp分支
        git diff temp
        # 比较远程代码与本地代码的区别
        git merge temp
        # 将temp分支合并到本地master分支
        git branch -d temp
        # 删除本地分支temp
    ```
7. 解决冲突
    ```
        git stash # 先将本地修改的代码暂存
        git stash list # 查看保存信息
        git pull # 拉取内容
        git stash pop # 还原暂存的内容
    ```
8. git fetch / git pull
    **推荐使用git fetch 和 git merge 结合使用来代替git pull**
9. git stash # 将工作现场"存储起来"
    * git stash list # 查看
    * git stash pop  # 恢复并删除存储的stash

10. 多人协作
    * git remote -v # 查看远程库的详细信息
    * git push origin dev # 向远程分支推送
    * git checkout -b dev origin/dev
11. git branch -a # 需详细了解
    git checkout -track origin/dev 
    git pull

12. git merge 合并的理解
    1.  分叉到两个不同的分支，又各自有不同的提交
    ![不同分支，新的提交](https://upload-images.jianshu.io/upload_images/14360740-cd3bcb98824f8885.png?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)
    2. git merge进行合并，会把最新的历史(c3和c4)和这两个分支的最近的祖先(c2)进行三方合并，合并的结果生成一个新的提交历史
    ![merge合并](https://upload-images.jianshu.io/upload_images/14360740-0a5b42e4962e6309.png?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)
    3. 注意: 在将三方merge合并的时候，总是需要以一个提交历史作为依据，在这个提交历史的基础上增加其他的两次修改，merge上使用的就是这个两个分支的最近祖先(c2)作为依据。
13. git rebase
    1.  rebase同样是通过合并来整合分叉的历史，唯一不同的就是，合并时所依据的提交历史不同（基），它是直接拿两个分支的最新提交历史（c3和c4）中的一个作为依据（基），比如以c3为基础。提取c4的更新
        * 这个过程相当于改变c4的基底为c3，将c4的更新应用于c3上，生成新的c4。
    ![将c4的修改变基到c3上](https://upload-images.jianshu.io/upload_images/14360740-e1bcbdf345ed1ef4.png?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)
        * 操作
          * 切换分支到experiment
          * experiment分支变基为master分支(即将experiment分支的更新，应用于c3，重新创建了一个分支)
        ```
            git checkout experiment
            git rebase master
        ```
        * 分支合并
    ![回到master分支进行合并](https://upload-images.jianshu.io/upload_images/14360740-70b7b5695752e87e.png?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)
        * 操作
            * 切换到master分支
            * 在master分支上对experiment进行合并
        ```
            git checkout master
            git merger experiment
        ```
    2. git rebase [basebranch][topicbranch]
        * 以basebranch为基，将topicbranch的修改应用于basebranch上
    3. 总结: 
       1. 变基（rebase）: 将一个分支的更新应用于另外一个分支
       2. 三方合并（merge）: 把两方的最后提交在共同祖先的基础上进行合并
    4. 使用场景
       1. 本地与远程的同一分支提交历史不一致
            * 例如: 本地的分支落后于远程线上的分支会push失败
              * 此时，需先pull，版本与远程代码同步
              * 再进行push操作
              * 注意: 该操作，git会自动将本地代码跟远程代码进行合并，然后生成一个新的提交历史
                ```
                    f63ecbf (HEAD -> master) Merge branch 'master' of https://gitee.com/greenhn/ganlin
                ```
            * 使用rebase进行解决
                ```
                    # 将本地分支在远程分支的基础上进行更新
                    git pull --rebase
                    git push
                ```
        2. 不同分支间的合并
            * 例如: 不同分支文件有冲突，造成merge失败
               * git checkout -b dev 
               * git checkout master
               * git merge dev
               * git status # 查看冲突，然后进行解决再次提交
            * 采用rebase进行分支合并
               * git checkout dev
               * git rebase master # 在master的基础上应用dev分支的更新
               * git status # 有冲突时，会中断rebase操作，查看冲突，解决冲突
               * git rebase --continue # 继续rebase
               * git checkout master # 切换到master分支 
               * git merge dev # 合并dev
    5. 一般提交操作，推荐使用rebase
        ```
            git pull --rebase
            git add .
            git commit -m 'feat: xxx'
            git push
        ```
    6. git rebase --abort 在任何时候可终止rebase操作，并且分支会回退到rebase的开始前的状态
        ```
            git rebase --continue
            git rebase --abort
        ```
            

### PropTypes的使用
1. 示例
```
    import PropTypes from 'prop-types'

    export default class Demo extends React.Component {
        render() { ... }
    }

    Demo.propTypes = {
        name: PropTypes.string
    }
```
2. 不同的验证器
```
    Demo.propTypes: {
        optionalArray: PropTypes.array,
        optionalBool: PropTypes.bool,
        optionalFunc: PropTypes.func,
        optionalNumber: PropTypes.number,
        optionalObject: PropTypes.object,
        optionalString: PropTypes.string,
        optionalSymbol: PropTypes.symbol

        # 任何类型都可以被渲染成: numbers, strings, elements,或者包含这些类型的数组（或者片段）
        optionalNode: PropTypes.node,

        # 一个react元素
        optionalElement: PropTypes.element,

        # 可以声明porps是一个类的实例
        # 执行js的instanceof运算操作
        optionalMessage: PropTypes.instanceOf(Message)
        
        # 声明props是特定值，类似于枚举
        optionalEnum: PropTypes.oneOf(['News', 'Photos'])

        # 一个对象可以是多种类型的其中之一
        optionalUnion: PropTypes.oneOfType([
            PropTypes.string,
            PropTypes.number,
            PropTypes.instanceOf(Message)
        ])

        # 某类型的数组
        optionalArrayOf: PropTypes.arrayOf(PropTypes.number)

        # 属性值为某种类型的对象
        optionalObjectOf: PropTypes.objectOf(PropTypes.number)

        # 特定形式的对象
        optionalObjectWithShape: PropTypes.shape({
            color: PropTypes.string,
            fontSize: PropTypes.number
        })

        # 使用isRequired链接上述任何一个约束，以确保该props为必传
        requiedFunc: PropTypes.func.isRequired,

        # 任何数据类型的值
        requiredAny: PropTypes.any.isRequired,

        // 你也可以声明自定义的验证器。如果验证失败返回 Error 对象。不要使用 `console.warn` 或者 throw ，
        // 因为这不会在 `oneOfType` 类型的验证器中起作用。
        customProp: function(props, propName, componentName) {
            if (!/matchme/.test(props[propName])) {
            return new Error(
                'Invalid prop `' + propName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
            }
        },

        // 也可以声明`arrayOf`和`objectOf`类型的验证器，如果验证失败需要返回Error对象。
        // 会在数组或者对象的每一个元素上调用验证器。验证器的前两个参数分别是数组或者对象本身，
        // 以及当前元素的键值。
        customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
            if (!/matchme/.test(propValue[key])) {
            return new Error(
                'Invalid prop `' + propFullName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
            }
        })
    }
```
3. 限制单个子代
```
    // 组件
    const OnlyOneChild = props => {
        const children = props.children
        return <div>{children}</div>
    }
    OnlyOneChild.propTypes = {
        children: PropTypes.element.isRequired
    }

    // 容器
    <OnlyOneChild>
        <h3>第一个子节点</h3>
        <h3>第二个子节点</h3>
    </OnlyOneChild>
```
* 该情况下，PropTypes会报错
![报错内容](https://user-gold-cdn.xitu.io/2018/5/28/163a5c8190f094da?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
4. 属性默认值 (defaultProps)
```
    const DefaultVal = props => {
        return (
            <h4>{props.name}</h4>
        )`
    }

    DefaultVal.defaultProps = {
        name: '王一杨'
    }
```

### react开发技巧总结
1. 组件通讯
   1. props
      1. props传递多个值
        ```
            # 传统写法
            const {dataOne,dataTwo,dataThree} = this.state
            <Com dataOne={dataOne} dataTwo={dataTwo} dataThree={dataThree}>
            # 升级写法
            <Com {...{dataOne, dataTwo, dataThree}} />
        ```
      2. Context使用Provider和Customer模式，在顶层的Provider中传入value，在子孙级的Customer中获取该值，并且能够传递函数
        ```
            import React from 'react'
            let { Consumer, Provider } = React.createContext();
            # 创建 context 并暴露Consumer和Provider模式
            export {
                Consumer,
                Provider
            }

            # 父组件
            // 导入 Provider
            import {Provider} from "../../utils/context"

            <Provider value={name}> # 传入value值供子孙组件使用
                <div style={{border:'1px solid red',width:'30%',margin:'50px auto',textAlign:'center'}}>
                    <p>父组件定义的值:{name}</p>
                    <EightteenChildTwo></EightteenChildTwo>
                </div>
            </Provider>

            # 子组件
            // 导入Consumer
            import { Consumer } from "../../utils/context"
            function Son(props) {
            return (
                # Consumer容器,可以拿到上文传递下来的name属性,并可以展示对应的值
                <Consumer>
                {name => (
                    <div
                    style={{
                        border: "1px solid blue",
                        width: "60%",
                        margin: "20px auto",
                        textAlign: "center"
                    }}
                    >
                    # 在 Consumer 中可以直接通过 name 获取父组件的值
                    <p>子组件。获取父组件的值:{name}</p>
                    </div>
                )}
                </Consumer>
            );
            }
            export default Son;

        ```
       3. 路由传参
          1. params
            ```
                <Route path="/path/:name" component={Com} />>
                # 路由跳转
                <Link to="/path/wangyiyang" />>
                or
                this.props.history.push({pathname: '/path/' + 'wangyiyang'})

                # 参数获取
                this.props.match.params.name
            ```
           2. query
            ```
                <Route path="/path" component={Com}>

                <Link to={{path: '/path', query: {name: 'wyy'}}}>
                or
                this.props.history.push({pathname: '/path', query: {name: 'wyy'}})

                this.props.location.query.name
            ```
           3. state
            ```
                <Route path='/sort ' component={Sort}/>
                <Link to={{ path : '/sort ' , state : { name : 'sunny' }}}> 
                this.props.history.push({pathname:"/sort ",state : { name : 'sunny' }});
                读取参数用: this.props.location.state 
            ```
           4. search
            ```
                <Route path='/web/search ' component={Search}/>
                <link to="web/search?id=12121212">xxx</Link>
                this.props.history.push({pathname:`/web/search?id=${row.id}`});
                读取参数用: this.props.location.search
            ```
           5. 优缺点
            ```
                1.params和 search 只能传字符串,刷新页面参数不会丢
                2.query和 state 可以传对象,但是刷新页面参数会丢失
            ```
       4. onRef（父组件获取到子组件实例，从而在父组件中直接调用子组件属性或方法）
           ```
                # 子组件
                export default class Son extends React.Component {
                    componentDidMount() {
                        this.props.onRef(this)
                    }
                    click = () => { ... }
                    render() {
                        return (<></>)
                    }
                }

                # 父组件
                export default class Parent extends React.Component {
                    onRef = (son) => {
                        # son 即为子组件实例，直接调用clicl方法
                        son.click()
                    }
                    render() {
                        return (<>
                            <Son onRef={this.OnRef} />
                        </>)
                    }
                }
           ```
       5. ref （通过React的ref属性获取到整个子组件实例，从而在父组件中对子组件进行操作）
           ```
                # 子组件
                export default class Son extends React.Component {
                    click = () => {...}
                    render() {
                        return (<></>)
                    }
                }

                # 父组件
                export default class Parent extends React.Component {
                    componentDidMount() {
                        this.refs['son']
                    }
                    render() {
                        return (<>
                            <Son ref="son" />>
                        </>)
                    }
                }
           ```
2. require.context() # 批量引入某路径下的所有组件
    ```
        const path = require('path')
        const files = require.context('@/components/home', false, /\.vue$/)
        const modules = {}
            files.keys().forEach(key => {
            const name = path.basename(key, '.vue')
            modules[name] = files(key).default || files(key)
        })
        components:modules

        # require.context(path, useSubdirectories, regExp)
        1. path: 组件路径
        2. 是否检索子目录
        3. 匹配文件的正则表达式，一般是文件名   
    ```
3. state值改变的5中方式
    ```
        # 1
        this.setState({count: 2})

        # 2
        this.setState(({count}) => ({count: count + 1}))

        # 3. 接收state和props参数
        this.setState((state, props) => {
            return {count: state.count + props.step}
        })

        # 4. hooks
        const [ count, setCount ] = useState(1)
        setCount(10)

        # 5. setState批量更新后的回调
        this.setState({count: 2}, () => {
            // state批量更新之后，调用该回调
        })
    ```
4. componentWillReceiveProps / getDerivedStateFromProps
    ```
        # componentWillReceiveProps(nextProps)
        # 该方法当props发生变化时执行，初始化render时不执行，在这个回调函数里面，你可以根据属性的变化，通过调用this.setState()来更新你的组件状态，旧的属性还是可以通过this.props来获取,这里调用更新状态是安全的，并不会触发额外的render调用。
        # 方法里手动判断一下this.props和nextProps是否相同，不相同了才执行更新方法
        # 注意: componentWillReceiveProps() 在生命周期的第一次render后不会被调用，但是会在之后的每次render中被调用 = 当父组件再次传送props,都会触发render方法。

        componentWillReceiveProps(nextProps) {
            if(nextProps.isVisible !== this.props.isVisible) {
                # props改变后需要做的事情
            }
        }


        # 16.x以后新增
        static getDerivedStateFromProps(nextProps, prevState) {
            # return 的值会更新setState
            # 如果无需更新state，返回null
        }
    ```
5. React.createRef(), 创建ref, 通过current属性获取组件的实例
    ```
        export default class Demo extends React.Component {
            constructor(props) {
                super(props)
                this.myRef = React.createRef()
            }

            render() {
                return (<input ref={this.myRef} />>)
            }
        }
    ```
6. React.forward((props, ref) => {  })
7. static 使用
    * 声明静态组件，可通过类型直接进行调用
      ```
        export default class Demo extends React.Component {
            static getName() {
                return 'wangyiyang'
            }

            static getDeviredStateFromProps(nextProps, prevState) {

            }

            render() {
                return <>...</>
            }
        }
        # 通过类名直接进行调用
        Demo.getName() # wangyiyang
      ```
    * static getDerivedStateFromProps 就属于静态方法
    * 声明类中: 
      ```
        import PropTypes from 'prop-types'
        export default class Demo extends React.Component {
            static propTypes = {
                name: PropTypes.string
            }
            static defaultProps {
                name: 'wangyiyang'
            }

            state = {
                age: 27
            }
            render() {...}
        }
      ```
8. constructor和super
   1. constructor为构造函数的主函数，该函数内部的this指向由该类new 出的实例
   2. super关键字用于访问父类的属性和调用父类的构造函数
        ```
            export default class Demo extends React.Component {
                constructor(props) {
                    super(props)
                    this.state = {
                        name: 'wangyiyang'
                    }
                }
                render() {...}
            }
        ```
9. 使用箭头函数声明实例方法，无需考虑this的指向问题
    ```
        export default class Demo extends React.Component {
            state = {
                num: 0
            }
            addNum = () => {
                # 使用箭头函数，无需考虑函数内部this(类实例)指向的问题
                this.setState(({ num }) => ({num: num + 1}))
            }
            render() {
                return (
                    <>
                        <h4>{this.state.num}</h4>
                        <button onClick={this.addNum}>addNum</button>
                    </>
                )
            }
        }
    ```
10. react-loadable # 用来控制组件的异步加载，
    1.  loader:需要加载的组件
    2.  loading:未加载出来的页面展示组件
    3.  delay:延迟加载时间
    4.  timeout:超时时间
11. 递归组件调用示例
    ```
        export dfault class Item extends React.Component {
            render() {
                const { arr } = this.props
                # arr数据类似于: 
                [
                    {
                        name: 'wyy',
                        children: [
                            {
                                name: 'mz'
                            }
                        ]
                    }
                ]
                <div>
                    {
                        arr.map((v, i) => (
                            <React.Fragment key={i}>
                                <span>{v.name}</span>
                                {
                                    v.children &&
                                    v.children.length > 0 &&
                                    <Item arr={v.children} />
                                    # 此处即为递归进行调用Items组件
                                }
                            </React.Fragment>
                        ))
                    }
                </div>
            }
        }
    ```
12. 受控组件和非受控组件
    * 受控组件: 组件拥有自己的状态
      ```
        export default class Demo extends React.Component {
            constructor() {
                this.state = {
                    value: 'wyy'
                }
            }
            render() {
                retuen <input value={this.state.value} type="text" />
            }
        }
      ```
    * 非受控组件: 组件没有自己的状态，在父组件通过ref来控制，或者通过props进行传值
      ```
        export default class Demo extends React.Component {
            render() {
                const { value } = this.props
                return <input type="text" value={value} />
            }
        }
      ```
13. 组件动态显示隐藏
    ```
        { flag ? <Demo /> : null }
        { flag && <Demo /> }
    ```
14. React.memo
    * 第一个参数为函数组件（纯函数），第二个参数为比较函数
    * 如果组件传递的props与上一次一致，则不重新渲染组件，从而提高浏览器性能
    * 传递第二个参数来代替shouleComponentUpdate，组件是否需要重新渲染
    ```
        function propsEqual(prevProps, nextProps) {
            # 注意: 如果两次传递的props属性一致，返回true, 否则返回false
            return prevProps.value === nextProps.value
        }

        export default React.memo(
            props => <div>{props.value}</div>,
            propsEqual
        )
    ```
15. React.PureComponent
    * 内部通过props和state的浅比较，避免组件的重复渲染，即代替shouldComponent
    * React.PureComponent作用于类，React.memo作用于函数，
    * React.PureComponent跟React.memo的主要目的都是为了提高组件的性能
    ```
        export default class Demo extends React.PureComponent {
            render() {
                return (
                    <div>{this.props.value}</div>
                )
            }
        }
    ```
    * 注意: **工作中应该经常使用 React.memo 和 React.PureComponent**
16. jsx中渲染falsy值
    * React中，不会对虚值进行展示，即页面不展示虚值
    * 如需展示虚值 String(falsy)
    ```
        export default class Demo extends React.Component {
            render() {
                return <h4>{String(false)}---{String(null)}</h4>
            }
        }
    ```
17. ReactDOM.createPortal
    * 组件的render函数返回的元素一般会挂载到它的父组件，(appendChild)
    * 而createPortal提供一种将组件渲染到父组件以外的DOM节点上
    * 第一个参数: 需要挂载的组件；第二个参数: 挂载点（DOM节点）
    ```
        import React from 'react'
        import ReactDOM from 'react-dom'

        export default class Demo extends React.Component {
            children = () => {
                return (
                    <div>this is children Component</div>
                )
            }

            render() {
                # 将children组件挂载到 id="app" DOM节点上
                return ReactDOM.createPortal(
                    this.children(),
                    document.querySelector('#app')
                )
            }
        }
    ```
18. React中使用innerHTML
    ```
        render() {
            const html = '<h4>wyy</h4>'
            return (
                <div 
                dangerouslySetinnerHTML={{__html: html}}
                ></div>
            )
        }
    ```
19. React.createElement
    1.  语法: React.createElement(type, [props], [...children])
    2.  实质上，jsx中的DOM元素最后都会转化为React.createElement
    ```
        render() {
            return (
                {
                    React.createElement(
                        'div',  # type
                        {value: 'wyy'}, # props
                        # children
                        React.createElement('span', {name: 'mz'}),
                        React.createElement('h5', {title: 'wangyiyang'})
                    )
                }
            )
        }
    ```
20. React.cloneElement
    * 作用: 复制组件，给组件传值或者添加属性
    * 语法: 
        ```
            React.cloneElement(
                element,
                [props],
                [...children]
            )
        ```
21. React.Fragment
22. 获取DOM上的自定义属性，例如: data-id
    ```
        export default class Demo extends React.Component {
            click = e => {
                e.target.dataset.id
            }
            render() {
                return (
                    <div data-id="1" onClick={this.click}>wangyiyang</div>
                )
            }
        }
    ```
### loading 动画    
    ```
        .loadingWrap {
            width: 400px;
            height: 20px;
            background-color: #eaeaea;
            overflow: hidden;
            .loading {
                transform: translateX(-100px);
                height: 20px;
                width: 100px;
                background-color: royalblue;
                animation: goahead 2s linear infinite normal;
            }
        }

        @keyframes goahead {
            0% {
                transform: translateX(-100px);
            }
            100% {
                transform: translateX(400px);
            }
        }
    ```

    

   
