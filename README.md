<!--
edit:   true

script: https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest
        https://cdn.jsdelivr.net/npm/chart.js


@onload
class XYChart extends HTMLElement {
  connectedCallback() {
    if (this.chart) return;

    const [x, y] = JSON.parse(this.textContent);
    this.textContent = "";

    const data = Array.from(x).map((v, i) => ({ x: v, y: y[i] }));

    this.innerHTML = `<div style="width:100%;height:200px;position:relative"><canvas style="background:white"></canvas></div>`;

    this.chart = new Chart(this.querySelector("canvas"), {
      type: "scatter",
      data: {
        datasets: [{
          label: "XY Data",
          data,
          showLine: true
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          x: { type: "linear" }
        }
      }
    });
  }

  disconnectedCallback() {
    this.chart?.destroy();
  }
}

customElements.define("xy-chart", XYChart);
@end

@TF.eval
<script>
setTimeout(async () => {
       const backup = window.console
       window.console = console
       try {
              @input
       } catch (e) {
              console.error(e.message)
       } finally {
              window.console = backup
              send.lia("LIA: stop")
       }
}, 100)

"LIA: wait"
</script>
@end
-->

# Why Is Software Documentation Still Static – And Why Modern Browsers Deserve Better

*André Dietrich -- TU Bergakademie Freiberg*

![](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExd3p5b3h5YWV4cTEwd3hwOWo0OTdoMjR6dmRkaXN2aWN5ZGZmbTVhOSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/dw9hPQTpuecWPJUy7u/giphy.gif)<!-- style="width: 100%" -->

> [!NOTE]
> This presentation is authored in LiaScript — the format under discussion.
> You are looking at the source at: https://github.com/andre-dietrich/We-Are-Developers-2026
>
> Open it at [liascript.github.io/course](https://liascript.github.io/course/?https://raw.githubusercontent.com/andre-dietrich/We-Are-Developers-2026/refs/heads/main/README.md) to run it live.


## The Unreasonable Effectiveness of HTML

![Screenshot: Blog article](./assets/img/screenshot-effectiveness-of-html.png "Link: https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html")


## The Problem: Documentation Optimized for Publishing

__The Publishing Pipeline__

``` ascii
.--------------.   .--------------.   .-------------.   .-------------.   .--------------.
|   Markdown   +-->|  build step  +-->| static HTML +-->|     CDN     +-->|    reader    |
'--------------'   '--------------'   '-------------'   '-------------'   '-------+------'
       A                                                                          |
       |                         (publishing loop)                                |
       +--------------------------------------------------------------------------+
```

---

__How Developers Actually Use Documentation__

``` ascii
 .------.       .-----.       .--------.       .---------.       .-------.       .-------.
(  read  )<--->(  run  )<--->(  modify  )<--->(  observe  )<--->(  debug  )<--->(  share  )
 '------'       '-----'       '--------'       '---------'       '-------'       '-------'
     A                                                                               A
     |                                                                               |
     '-------------------------------------------------------------------------------'
```

### Example: Before AI There Was AI

![Tensorflow logo](/assets/img/tensorflow-js-logo-social.png)

Example: https://github.com/tensorflow/tfjs

## Mental Model Shift: Browser as Runtime

|                  | Old mental model        | New mental model                      |
|------------------|-------------------------|---------------------------------------|
| **Markdown**     | Source for a build step | Source for a browser runtime          |
| **Browser**      | A display device        | A full application runtime            |
| **Code runs**    | In CI, at build time    | In the reader's browser, at read time |
| **Output lives** | In the repository       | In the live document                  |


## In A Perfect World

    --{{0}}--
TensorFlow.js is a JavaScript library that allows machine learning models to run directly in the browser or in Node.js. It lets developers create, train, and use neural networks without needing Python or a separate machine learning server. With TensorFlow.js, data is stored in tensors, which are multi-dimensional arrays used for numerical computation.

    --{{1}}--
The first code snippet creates two tensors, `a` and `b`, each containing four numbers. The function `tf.mul(a, b)` multiplies the values in the two tensors element by element. This means `1` is multiplied by `6`, `2` by `5`, `3` by `4`, and `4` by `3`. The result is a new tensor containing `[6, 10, 12, 12]`, and `multiply.print()` displays this result.

    {{1}}
``` js
const a = tf.tensor([1,2,3,4])
const b = tf.tensor([6,5,4,3])

const multiply = tf.mul(a,b)
multiply.print()
```
@TF.eval

    --{{2}}--
The second code snippet creates a simple neural network model using `tf.sequential()`. It adds one dense layer with one unit, then compiles the model using mean squared error as the loss function and stochastic gradient descent as the optimizer. The training data follows the rule `y = 2x - 1`, so after training for 250 epochs, the model learns this pattern. When the model is given the input `20`, it should predict a value close to `39`.

    {{2}}
``` js
// Create a simple model.
const model = tf.sequential();
model.add(tf.layers.dense({units: 1, inputShape: [1]}));

// Prepare the model for training: Specify the loss and the optimizer.
model.compile({loss: 'meanSquaredError', optimizer: 'sgd'});

// Generate some synthetic data for training. (y = 2x - 1)
const xs = tf.tensor2d([-1, 0, 1, 2, 3, 4], [6, 1]);
const ys = tf.tensor2d([-3, -1, 1, 3, 5, 7], [6, 1]);


console.html(`<xy-chart>[[${await xs.data()}],[${await ys.data()}]]</xy-chart>`)

// Train the model using the data.
await model.fit(xs, ys, {epochs: 250});

model.predict(tf.tensor2d([20], [1, 1])).print();
```
@TF.eval



## Perfct World #2

Russa started its 3 week war Ukraine
<script format="relativetime" unit="days">
const start = new Date("2022-02-04")
const today = new Date()

const diff = (today - start) / (1000 * 60 * 60 * 24)

Math.floor(-diff)
</script>.


longitude: <script default="13.33125" input="range" output="longitude">@input</script>

latitude: <script default="50.92558" input="range" output="latitude">@input</script>


<script run-once="true" style="display: block">
  fetch("https://api.open-meteo.com/v1/forecast?latitude=@input(`latitude`)&longitude=@input(`longitude`)&hourly=temperature_2m")
    .then(response => response.json())
    .then(data => {
      let table = "<!-- data-show -->"

      table += "| Time | Temperature |\n"
      table += "| ---- | ----------- |\n"

      for (let i=0; i < data.hourly.time.length; i++) {
        table += "| " + Date.parse(data.hourly.time[i]) + " | " + data.hourly.temperature_2m[i] + " |\n"
      }
      send.lia("LIASCRIPT: "+table) }
    )
    .catch(e => {
      send.lia("ups, something went wrong")
    })
  "waiting for the weather"
</script>

















## LiaScript - Resources

* __Project-Website:__ https://LiaScript.github.io
* __GitHub:__ https://github.com/liascript
* __YouTube:__ https://www.youtube.com/channel/UCyiTe2GkW_u05HSdvUblGYg
* __Additional resources:__

  - Documentation: https://github.com/LiaScript/docs
  - Free books: https://github.com/LiaBooks
  - Templates: https://github.com/topics/liascript-template
  - Courses & ...: https://github.com/topics/liascript-course
