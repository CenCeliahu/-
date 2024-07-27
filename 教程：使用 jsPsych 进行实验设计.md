### 教程：使用 jsPsych 进行实验设计

这个教程将指导你如何使用 jsPsych 库创建一个简单的实验，即使你对 jsPsych 并不了解。我们将分为以下几个步骤：

1. **准备环境**
2. **编写 HTML 文件**
3. **编写 CSS 文件**
4. **编写 JavaScript 实验代码**
5. **运行实验**

接下来我将以N-back实验为例来展示我们如何使用jspsych进行实验程序的编制。

**实验逻辑**
该实验的核心是一个自适应的n-back任务。参与者需要根据指示按下不同的按键。当当前字母与之前的字母匹配时按左键，否则按下键。实验会根据参与者的表现调整任务难度，使其保持在适当的挑战水平。

#### 第一步：准备环境

首先，你需要确保你的实验文件夹中包含以下文件类型：

- `index.html`
- `style.css`
- `experiment.js`

同时，你需要下载并包含 jsPsych 库及其插件。你可以从 [jsPsych 官方网站](https://www.jspsych.org/)下载这些文件。

#### 第二步：编写 HTML 文件

在你的实验文件夹中创建一个名为 `index.html` 的文件，

HTML 文件是实验的骨架，它定义了实验的页面结构和引用的外部文件。

1. **头部（`<head>`）：**
    - **引入外部库和插件**：通过 `<script>` 和 `<link>` 标签引入 jsPsych 库、其插件和 CSS 样式文件。
    - **引入自定义文件**：引用自定义的样式文件 `style.css` 和实验脚本 `experiment.js`。

2. **主体（`<body>`）：**
    - 页面主体内容为空，因为实验的内容将由 JavaScript 动态生成。

3. **脚本（`<script>`）：**
    - **初始化实验**：当页面加载完成后，调用 `jsPsych.init` 函数，开始实验并设置实验完成后的回调函数。
4. 下面是总的html文件的代码

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Adaptive N-Back</title>
  
  <!-- 引入 jsPsych 库和插件 -->
  <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/jspsych.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/plugins/jspsych-html-keyboard-response.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/plugins/jspsych-survey-text.js"></script>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/css/jspsych.css">

  <!-- 引入自定义样式和脚本 -->
  <link rel="stylesheet" href="style.css">
  <script src="experiment.js"></script>
</head>

<body></body>
<script>
    document.addEventListener("DOMContentLoaded", function () {
      jsPsych.init({
        timeline: timeline,
        on_finish: function() {
          jsPsych.data.displayData();
        }
      });
    });
</script>

</html>
```

#### 第三步：编写 CSS 文件

在你的实验文件夹中创建一个名为 `style.css` 的文件，

CSS 文件定义了实验的样式，使实验界面更加美观。

这个文件很简单，只定义了一个样式类 `.center-text`，将文本的字体大小设置为 100 像素。你可以在实验脚本中使用这个类来调整文本显示的样式。

```css
.center-text {
    font-size: 100px;
}
```

总的css代码如下：

```css
body {
    font-family: Arial, sans-serif;
}

.centerbox {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.center-text {
    font-size: 48px;
}

.block-text {
    font-size: 24px;
}
```

#### 第四步：编写 JavaScript 实验代码

在你的实验文件夹中创建一个名为 `experiment.js` 的文件，并添加以下内容：

这个文件是实验的核心，包含实验的逻辑、变量和流程控制。

#### 1. 定义实验变量
```javascript
var run_attention_checks = false;
var attention_check_thresh = 0.65;
var sumInstructTime = 0;
var instructTimeThresh = 0;
var credit_var = true;
```

这些变量控制实验的运行，例如是否运行注意力检查、注意力检查的阈值、指令时间阈值等。

#### 2. 定义辅助函数
```javascript
function evalAttentionChecks() {
    // 计算注意力检查通过率
}

function assessPerformance() {
    // 评估实验表现
}
```

这些辅助函数用于评估参与者的表现和注意力。

#### 3. 设置实验块
```javascript
var attention_check_block = {
    type: 'attention-check',
    data: { trial_id: "attention" },
    timing_response: 180000,
    response_ends_trial: true,
    timing_post_trial: 200
};

var post_task_block = {
    type: 'survey-text',
    data: { exp_id: "adaptive_n_back", trial_id: "post task questions" },
    questions: ['请总结这个任务的要求。', '对这个任务有何评论？'],
    rows: [15, 15],
    columns: [60, 60]
};
```

定义了注意力检查块和任务后的问卷调查块。

#### 4. 定义实验流程
```javascript
var instruction_node = {
    timeline: [feedback_instruct_block, instructions_block],
    loop_function: function(data) {
        // 判断是否需要重新展示指令
    }
};

var end_block = {
    type: 'poldrack-text',
    text: '感谢完成这个任务！按下<strong>回车键</strong>继续。',
    cont_key: [13],
    data: { trial_id: "end", exp_id: 'adaptive_n_back' },
    timing_response: 180000,
    timing_post_trial: 0,
    on_finish: assessPerformance
};
```

这些块定义了指令节点和实验结束块，确保参与者阅读指令并在结束时评估他们的表现。

#### 5. 设置实验试次
```javascript
var practice_trials = [];
for (var i = 0; i < (base_num_trials + 1); i++) {
    var stim = randomDraw(letters);
    stims.push(stim);
    var practice_block = {
        type: 'poldrack-categorize',
        is_html: true,
        stimulus: '<div class="centerbox"><div class="center-text">' + stim + '</div></div>',
        key_answer: correct_response,
        data: { trial_id: "stim", exp_stage: "practice", stim: stim, target: target },
        correct_text: '<div class="centerbox"><div style="color:green;font-size:60px;" class="center-text">Correct!</div></div>',
        incorrect_text: '<div class="centerbox"><div style="color:red;font-size:60px;" class="center-text">Incorrect</div></div>',
        timeout_message: '<div class="centerbox"><div style="font-size:60px" class="center-text">Respond Faster!</div></div>',
        timing_feedback_duration: 500,
        show_stim_with_feedback: false,
        choices: [37, 40],
        timing_stim: 500,
        timing_response: 2000,
        timing_post_trial: 500
    };
    practice_trials.push(practice_block);
}
```

定义了练习试次，参与者可以通过练习熟悉实验流程。

#### 6. 设置自适应试次
```javascript
var adaptive_test_node = {
    timeline: [update_target_block, adaptive_block],
    loop_function: function() {
        trials_left -= 1;
        return trials_left !== 0;
    }
};
```

这个节点包含自适应试次逻辑，控制试次循环。

总的实验代码如下：

```javascript
/* ************************************ */
/* Define helper functions */
/* ************************************ */
function evalAttentionChecks() {
    var check_percent = 1
    if (run_attention_checks) {
        var attention_check_trials = jsPsych.data.getTrialsOfType('attention-check')
        var checks_passed = 0
        for (var i = 0; i < attention_check_trials.length; i++) {
            if (attention_check_trials[i].correct === true) {
                checks_passed += 1
            }
        }
        check_percent = checks_passed / attention_check_trials.length
    }
    return check_percent
}

function assessPerformance() {
    /* Function to calculate the "credit_var", which is a boolean used to
    credit individual experiments in expfactory. */
    var experiment_data = jsPsych.data.getTrialsOfType('poldrack-single-stim')
    var missed_count = 0
    var trial_count = 0
    var rt_array = []
    var rt = 0
    var choice_counts = { '-1': 0, '32': 0 }
    for (var i = 0; i < experiment_data.length; i++) {
        if (experiment_data[i].possible_responses != 'none') {
            trial_count += 1
            rt = experiment_data[i].rt
            key = experiment_data[i].key_press
            choice_counts[key] += 1
            if (rt == -1) {
                missed_count += 1
            } else {
                rt_array.push(rt)
            }
        }
    }
    var avg_rt = rt_array.length !== 0 ? math.median(rt_array) : -1
    var missed_percent = missed_count / experiment_data.length
    var responses_ok = true
    Object.keys(choice_counts).forEach(function(key, index) {
        if (choice_counts[key] > trial_count * 0.85) {
            responses_ok = false
        }
    })
    credit_var = (missed_percent < 0.4 && (avg_rt > 200) && responses_ok)
    jsPsych.data.addDataToLastTrial({ "credit_var": credit_var })
}

var getInstructFeedback = function() {
    return '<div class = centerbox><p class = center-block-text>' + feedback_instruct_text + '</p ></div>'
}

var randomDraw = function(lst) {
    var index = Math.floor(Math.random() * (lst.length))
    return lst[index]
}

// More helper functions...

/* ************************************ */
/* Define experimental variables */
/* ************************************ */
var run_attention_checks = false
var sumInstructTime = 0
var instructTimeThresh = 0
var credit_var = true

var letters = 'bBdDgGtTvV'.split("")
var num_blocks = 20
var base_num_trials = 20
var control_num_trials = 42
var block_acc = 0
var delay = 2
var trials_left = 0
var target_trials = []
var current_trial = 0
var current_block = 0
var block_trial = 0
var target = ""
var curr_stim = ''
var stims = []

/* ************************************ */
/* Set up jsPsych blocks */
/* ************************************ */
var feedback_instruct_text = '欢迎参加我们的实验。本任务大约需要 20 分钟。按 <strong>回车键</strong> 开始。'
var feedback_instruct_block = {
    type: 'html-keyboard-response',
    stimulus: getInstructFeedback,
    choices: [13],
    post_trial_gap: 0,
    trial_duration: 180000
}

var instructions_block = {
    type: 'html-keyboard-response',
    stimulus: '<div class = "centerbox"><p class = "block-text">在这个实验中，你将看到一系列逐个呈现的字母...</p ></div>',
    choices: [13],
    post_trial_gap: 1000,
    on_finish: function() {
        var rt = jsPsych.data.getLastTrialData().select('rt').values[0]
        sumInstructTime += rt
        if (sumInstructTime <= instructTimeThresh * 1000) {
            feedback_instruct_text = '阅读说明过快，请仔细阅读。按 <strong>回车键</strong> 继续。'
        } else {
            feedback_instruct_text = '完成说明。按 <strong>回车键</strong> 继续。'
        }
    }
}

var end_block = {
    type: 'html-keyboard-response',
    stimulus: '<div class = "centerbox"><p class = "center-block-text">感谢完成任务！</p ><p class = "center-block-text">按 <strong>回车键</strong> 继续。</p ></div>',
    choices: [13],
    post_trial_gap: 0,
    on_finish: assessPerformance
}

// Define other blocks...

/* ************************************ */
/* Create timeline and start experiment */
/* ************************************ */
var timeline = []

timeline.push(feedback_instruct_block)
timeline.push(instructions_block)
// Add more blocks to the timeline...

timeline.push(end_block)
```



#### 第五步：运行实验

将这三个文件保存到同一文件夹中，然后打开 `index.html` 文件，即可在浏览器中运行你的实验。

通过上述步骤，你可以创建一个简单的实验，即使你之前没有使用过 jsPsych。通过不断调整和扩展 `experiment.js` 文件中的代码，你可以设计出更加复杂和多样的实验。