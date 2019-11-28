# clean-code-javascript 中文版

## 目录

1. [简介](#简介)
2. [变量](#变量)
3. [函数](#函数)
4. [对象与数据结构](#对象与数据结构)
5. [类](#类)
6. [原则](#原则)
7. [测试](#测试)
8. [并发](#并发)
9. [异常监控](#异常监控)
10. [格式化](#格式化)
11. [注释](#注释)
12. [翻译](#翻译)

## 简介

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

将 Robert C. Martin 的
[代码整洁之道](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) 书中的软件工程原则应用于 JavaScript 这门语言. 这并不是一个风格指南。 它是一份能够帮助你写出[可读性、易用性和易重构](https://github.com/ryanmcdermott/3rs-of-software-architecture)的 JavaScript 代码。

这里的每一条规则并非一定要遵循，甚至只有少部分会被广泛认知。虽然这些是《代码简洁之道》作者多年经验的结晶，但这些也只是指导建议而已。

人类的软件开发行业也只是经过 50 多年，仍然还需要我们继续学习。当软件行业跟建筑行业一样历史悠久的时候，我们就会存在一些严格遵守的规定。但现在，那些指导建议只是为了让你和你的团队生产出更加更有质量的 JavaScript 代码。

需要知道的是：了解他并不能使你成为一名优秀的软件工程师，多年践行这些规则也并不意味着你不会犯错。合抱之木，生于毫末；九层之台，起于累土。最后，我们需要经常和同事们 code review，不断优化代码。不要因为最初的代码需要修改而自责。而是加油对那些代码下手。


## **变量**

### 使用有意义且可读性强的变量名

**Bad:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Good:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ 回到顶部](#目录)**

### 功能类似的变量名统一风格

**Bad:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**

```javascript
getUser();
```

**[⬆ 回到顶部](#目录)**

### 使用易于检索的变量名

我们阅读代码的数量比写的数量多得多。所以我们写出来代码的可读性和可检索性是非常重要的。阅读和理解那些变量名晦涩难懂的代码，对于读者的体验是极差的。所以让你的代码更具有可检索性。可以使用 [buddy.js](https://github.com/danielstjules/buddy.js) 和
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) 来帮助我们找到未命名的常量。

**Bad:**

```javascript
// 这里的 86400000 是什么意思？？？
setTimeout(blastOff, 86400000);
```

**Good:**

```javascript
// 用大写字母来声明常量
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

**[⬆ 回到顶部](#目录)**

### 使用解释性的变量名

**Bad:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Good:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ 回到顶部](#目录)**

### 避免精神分裂

显式优于隐式。

**Bad:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // 等等，l 究竟代表的是什么？？？
  dispatch(l);
});
```

**Good:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ 回到顶部](#目录)**

### 不要过分描述

如果你的 类/对象名已经有意义了，请不要在属性名中重复了。

**Bad:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car) {
  car.carColor = "Red";
}
```

**Good:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car) {
  car.color = "Red";
}
```

**[⬆ 回到顶部](#目录)**

### 使用默认参数来替代短路运算及条件判断

通常来说，默认参数是会被短路运算更简洁的。当你意识到使用他们时，你的函数可能只会为 `undefined` 提供默认参数，其他（如 `''`，`""`，`false`，`null`，`0`，`NaN`）的 假值（"falsy"）不会被替换为默认参数。

**Bad:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Good:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

## **函数**

### 函数参数 (理想情况下控制在两个及两个以下)

限制函数参数个数是很有必要的，可以帮助我们更容易的测试函数。 一旦超过三个函数参数，会造成编写测试用例的难度增大，并需要覆盖所有的参数组合情况。

一个或两个参数是较为理想的，尽量避免三个参数。

大部分公司都会认可这一点。

通常来说，超过两个参数的函数意味着它的功能很复杂，如果不是的话，可以将多个参数封装到一个高级对象中。

JavaScript 可以允许开发者不通过类来创建对象，这给我们带了极大的方便，在遇到多个参数时，可以使用对象。

为了明确函数的预期属性，可以使用 `ES2015/ES6` 的解构语法。下面是一些建议：

1. 当其他人看到函数签名的时候，立即就会明白该使用什么属性。
2. 解构会克隆（浅拷贝）函数参数对象中属性对应的值。这可以避免某些副作用影响。注意：对象和数组不会被解构克隆（浅拷贝对于数组和对象是拷贝的引用。）
3. Linters 对于没有使用的属性会给出提醒，如果不进行解构，是不会有提醒的。

**Bad:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ 回到顶部](#目录)**

### 函数应该只做一件事

这是软件工程中最重要的一条原则。当函数不仅做一件事时，他们将难以编写、测试和理解。当你将一个函数分隔为只做一个动作时，该函数将易于重构且更具可读性。如果你谨遵本指南的这条规则，你会大幅超越许多开发者。

**Bad:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ 回到顶部](#目录)**

### 函数名应该明确指出函数的功能

**Bad:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Good:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ 回到顶部](#目录)**

### 函数应该只做一层抽象

当你的函数超过一层抽象时，那通常意味着函数做了太多事情。拆分函数提供他们的重用性和测试性。


**Bad:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Good:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ 回到顶部](#目录)**

### 移除重复的代码

尽最大努力避免重复代码！重复代码意味着变更某些逻辑的时候，需要修改多处地方，这样是不好的。

想象一下，如果你经营一家餐厅，并记录下你的库存：所有的西红柿、洋葱、大蒜、香料等。当你端上一道菜里面有西红柿时，如果你有多个清单需要更新多个地方。如果你只有一个列表，就只有一个地方需要更新！

经常有重复的代码，因为你有两个或更多的轻微不同点，有很多共同点，但是它们的不同点迫使你有两个或多个独立的函数。移除重复代码意味着创建一个抽象来处理这组函数/模块/类的不同点。

正确的抽象是至关重要的。所以你需要遵循 类 中的坚实原则。错误的抽象可能比重复代码更糟糕，所以要小心哦。说了这些，如果你能做好抽象的话，那么放手去做吧。DRY（不要重复你自己），否则你会发现修改一个问题时，需要修改多处地方。

**Bad:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ 回到顶部](#目录)**

### 使用 `Object.assign` 设置默认对象

**Bad:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  config = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ 回到顶部](#目录)**

### 不要使用标识符作为函数参数

标识符告诉其他开发者该函数会做不只一件事。函数需要具有单一性。如果你的函数因为一个布尔值会出线不同的逻辑，那么请拆分他们。

**Bad:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ 回到顶部](#目录)**

### 避免副作用（第一部分）

如果函数产生了“接受一个值并返回一个结果”之外的行为时，那么该函数就产生副作用。副作用可能是写入文件，修改全局变量或者是将你所有的钱转给陌生人。

现在来说，有些时候你确实需要在程序中产生副作用。例如前面的例子，写入一个文件，你需要将功能集中到一起。不要用多个函数/类修改某个文件。用且只用一个服务完成这一需求。

重点是避免一些常见的易犯的错误： 比如在对象之间共享状态而不使用任何结构，使用任何地方都可以写入的可变的数据类型，没有集中化导致副作用。如果你能做到这些， 那么你将会比其它的码农大军更加幸福。

**Bad:**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ 回到顶部](#目录)**

### 避免副作用（第二部分）

在 JavaScript 中，基本类型是通过值传递，对象和数组使用引用传递。对于对象和数组来说，举个栗子，你的函数通过添加一个购买项目修改了购物车数组。那么所有使用到该购物车数组的任何其他函数都会受到影响。这样是好的，但是有时候也是不好的。来一起看一下不好的情况：

用户单击“Purchase”按钮，该按钮调用“Purchase”函数发起网络请求并将“cart”数组发送到服务器。因为如果网络连接不好，`purchase`函数必须继续重试请求。现在，如果多个用户同时点击了“添加到购物车”怎么办在网络请求开始之前，他们实际上不想要的项目上的按钮？如果发生这种情况并且网络请求开始，那么购买功能将发送意外添加的项目，因为它具有对购物的引用
“addItemToCart”函数通过添加不需要的物品。

一个很好的解决方案是“addItemToCart”总是克隆“cart”，编辑它，然后返回克隆。这样可以确保保存购物车引用的其他功能不会受到任何更改的影响。

要提到这种情况的两个注意事项：

1. 在某些情况下，您可能真的想要修改输入对象，但是当你采用这种编程实践时，你会发现非常罕见。大多数东西都可以重构，没有副作用！
2. 克隆大型对象在性能方面可能非常昂贵。幸运的是，这在实践中并不是一个大问题，因为有[较优实践](https://facebook.github.io/immutable-js/)可以让克隆变得更快，而且不像手动克隆对象和数组那样占用内存。

**Bad:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ 回到顶部](#目录)**

### 不要写入全局函数

污染全局作用域在 JavaScript 中是一个糟糕的做法。因为可能会与引入的另一个库冲突，而且你的 API 调用方在生产环境中使用可能会得到一个异常。来一起看一种案例：如果你想拓展 JavaScript 中的原生 `Array`，使其支持 `diff` 函数用于展示两个数组之间的差异。你可以在 `Array.prototype` 中增加新的方法，但这么做会和其他有类似需求的库造成冲突。
如果另一个库对 diff 的需求为比较一个数组中首尾元素间的差异呢？这就是为什么更加推荐使用 `ES2015/ES6` 中的 类，来对 `Array` 做简答继承及拓展。

**Bad:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ 回到顶部](#目录)**

### 函数式编程优于指令式编程

JavaScript 并不是类似 Haskell 那种的函数式编程语言，但他有自己的函数式编程风格。函数式语言更加简洁清晰、易于测试。

你可以使用函数式编程风格时请尽情使用。

**Bad:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ 回到顶部](#目录)**

### 封装条件判断

**Bad:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Good:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 避免否定情况的判断

**Bad:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 避免条件语句

这似乎是一项不可完成的任务。大多数人听完这条会说：“怎么可能不用`if`完成所有功能呢？”答案就是：大多数情况你可以使用多态来达到相同的功能。第二个问题在于采用这种方式的原因是什么。答案就是：前面提到过一点：函数具有单一性。当你的类和方法中存在`if`语句，那其实是在高速用户你的函数不仅做一件事。记住，只做一件事！

**Bad:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ 回到顶部](#目录)**

### 避免类型检查 (第一部分)

JavaScript 是一门弱类型语言，这意味着你的函数能接受任何类型的参数。 但是有时又会被这种自由所伤害， 于是又尝试在你的函数中做类型检查。其实有很多方法可以避免这个，第一个要考虑的是一致的 API。

**Bad:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Good:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ 回到顶部](#目录)**

### 避免类型检查 (第二部分)

如果需处理的数据为字符串，整型，数组等类型，无法使用多态并仍有必要对其进行类型检测时，你可以考虑使用 TypeScript。 它是一个常规 JavaScript 的优秀的替代品， 因为它在标准的 JavaScript 语法之上为你提供静态类型。 对常规 JavaScript 做人工类型检查的问题是需要大量的冗词来仿造类型安 全而不缺失可读性。 保持你的 JavaScript 简洁，编写良好的测试，并有良好的代码审阅。另外使用 TypeScript （就像我说的， 它是一个伟大的替代品）来完成这些。

**Bad:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Good:**

```typescript
function combine(val1: number, val2: number) {
  return val1 + val2;
}
```

**[⬆ 回到顶部](#目录)**

### 不要过度优化

现代的浏览器在运行时会对代码自动进行优化。大部分时间，你所做的优化其实是在浪费时间。这些是一些[好的案例](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)，可以查看那些地方需要优化。 为这些而优化， 直到他们被修正。

**Bad:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ 回到顶部](#目录)**

### 移除僵尸代码

僵尸代码同冗余代码一样糟糕。我们没有理由容忍他们活在代码中。如果它没有被调用，那么删除它。等到需要它们的时候，可以从历史版本中找到它们。
Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Bad:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Good:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ 回到顶部](#目录)**

## **对象与数据结构**

### 使用 getters 和 setters

在对象中使用 getters 和 setters 是比使用 property 更优的做法。你可能会问“为什么要这么做？”好吧，下面是列举的几条原因：

- 当你想要获取对象属性之外做更多的事情，你不需要在代码中查找和修改每一处访问
- 使用 `set` 可以让数据验证变得简单
- 封装内部实现
- 在 `getting`、`setting` 时，可以更加容易的添加日志和错误处理
- 可以延迟加载对象的属性，例如从服务端获取数据

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ 回到顶部](#目录)**

### 使对象具有私有属性

可以通过闭包来完成（针对于 ES5 及以下版本）

**Bad:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ 回到顶部](#目录)**

## **类**

### ES2015/ES6 的类优先于 ES5 的纯函数

很难给经典的 ES5 类创建可读的继承，构造函数及放发等。如果你需要使用继承（讲道理你应该会用到），那么优先使用 ES2015/ES6 中的类。不过，短小的函数还优于类，在更大更复杂的对象上类是比较好的。

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ 回到顶部](#目录)**

### 使用方法链

这个模式在 JavaScript 中十分有用，你可能已经在很多库（如 jQuery、Lodash 等）中看到过了。它使得代码变得更有表现力，减少冗余。因为上面的原因，所以说推荐使用方法链之后再看看代码会变得多么整洁。在类/方法中，可以简单得在每个方法后面都 `return this`，然后就可以与这个类/方法中的其他方法链式调用。

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ 回到顶部](#目录)**

### 组合优先于继承

如[设计模式](https://en.wikipedia.org/wiki/Design_Patterns)中提到，应该优先使用组合而不是继承。有很多理由去使用继承，也有很多理由去使用组合。如果你本能的观点是继承，那么请想一想组合能否更好的解决你的问题。很多情况下它是可以的。

那么你可能会想，什么时候我该使用继承？这取决于你手头的问题，下面几条关于什么时候继承比组合更好用的说明：

1. 你的继承使用来表示 "is-a" 的关系而不是 "has-a" 的关系（例如：人 => 动物 VS 用户 => 用户详情）
2. 你可以重用来自基类的代码（人可以复用所有动物的逻辑）
3. 你想通过基类对子类进行全局的修改（改变所有动物行动时消耗的卡路里）

**Bad:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ 回到顶部](#目录)**

## **原则**

### 单一职责原则 (SRP)

如《代码简洁之道》中提到的，“修改一个类的理由不应该超过一个”。将多个功能塞进一个类的想法很诱人，但这将导致你的类无法达到概念上的内聚，并经常不得不进行修改。最小化对于一个类的修改次数是非常有必要的。如果一个类中包含着过多过杂的功能，当你只对其中一小部分修改时，将很难想象到这块修改会对依赖该类的其他模块造成怎样的影响。

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ 回到顶部](#目录)**

### 开闭原则 (OCP)

如 Bertrand Meyer 所说，“软件实体（类，模块，函数等）应该易于拓展，难于修改。”。这意味着，我们应该允许用户方便的拓展我们代码模块的功能，而不需要打开 js 源码文件对其修改。

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ 回到顶部](#目录)**

### 里氏替换原则 (LSP)

对于一个简单的概念而言，这是一个听起来吓人的术语。它的正式定义是：“如果 S 是 T 的 子类型，那么类型为 T 的对象可以被类型为 S 的对象替换（例如，类型为 S 的对象可以作为类型为 T 的替代品）”不需要修改目标程序的期望性质（正确性、任务执行性等）。当然显然这也是一个糟糕的定义。

更好的解释是：“子类对象应该能够替换其超类对象被使用”。也就是说，如果有一个父类和一个子类，当采用子类替换父类时不应该产生错误的结果。用一个经典的正方形和矩形作为例子，在数学上，一个正方形是矩形，但是“is-a”的关系使用继承来实现，很快将会遇到麻烦。


**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ 回到顶部](#目录)**

### 接口隔离原则 (ISP)

JavaScript 中没有接口这个概念，所以这个原则不像是其他语言那么严谨。但是，对于 JavaScript 这类弱类型语言，它依然很重要且有意义。

接口隔离原则告诉我们：“客户端不应该依赖它所不需要的接口”。由于 JavaScript 是“duck typing”类型，所以接口都是隐式的。

在 JavaScript 中能比较好的说明这个原则的是一个类需要一个巨大的配置对象。 不需要客户端去设置大量的选项是有益的， 因为多数情况下他们不需要全部的设置。 让它们变成可选的有助于防止出现一个“胖接口”。

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ 回到顶部](#目录)**

### 依赖反转原则 (DIP)

这条原则阐述了两条重要的事情：

1. 高级模块不应该依赖低级模块，两者都应该依赖抽象接口
2. 抽象接口应该脱离具体实现，具体实现应该依赖抽象接口。

这个概念刚开始理解可能会比较晦涩，但是你如果用过 AngularJS 的话，你应该看过通过依赖注入来实现这个原则，虽然他们的概念不同，但是依赖反转原则是让高级模块避免低级模块的细节和创建，可以通过 Dependency Injection (DI) 来实现。这样做的好处是降低模块间的耦合程度。耦合会导致代码难以重构，是一种非常糟糕的开发模式。

就像上面说的那样，JavaScript 没有接口，所以被依赖的抽象都是隐式的。即一个对象/类的方法和属性直接暴露给另一个对象/类。在下面的例子中，任何一个 Request 模块的隐式 `InventoryTracker` 都回会有一个 `requestItems` 方法。

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ 回到顶部](#目录)**

## **测试**

测试比发布更加重要。如果你的项目没有测试或测试不够充分时，那么每次发布的时候你都不能确认有没有破坏其他地方。测试的量有你的团队决定，拥有 100% 覆盖率（所有语句和分支）是你高度自信和内心平静的源泉。这意味着需要一个伟大的测试框架，当然也需要一个好的 [覆盖率工具](https://gotwarlost.github.io/istanbul/)

没有理由不写测试。这里有[大量优秀的测试框架](https://jstherightway.org/#testing-tools)，选一个适合你团队的就好。当为团队选择测试框架之后，加下来的目标是为生产的每一个新功能/模块都编写测试代码。如果你倾向于测试驱动开发（TDD），那就太棒了，但是要点是确定你在上线任意功能或重构现有功能之前，达到目标覆盖率。

### 一个措施一个概念

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ 回到顶部](#目录)**

## **并发**

### 使用 Promise，避免回调

回调不够简洁且会造成大量的嵌套。在 `ES2015/ES6` 中内置了 Promise，尽管用它吧~

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ 回到顶部](#目录)**

### Async/Await 是 Promises 更好的选择

Promise 较回调而言是一种更好的选择，然而在 `ES2017/ES8` 中的 async/await 提供了更为简洁的解决方案。你需要做的只是在相关函数前增加 `async` 关键字，接下来就不需要在 `then` 函数链中编写逻辑。如果你能使用 `ES2017/ES8` 的高级功能， 那么尽管使用它吧！

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ 回到顶部](#目录)**

## **异常监控**

抛出错误是件好事情。它们表示你当前程序存在错误，运行时可以成捕获，并且通过停止执行当前堆栈上的函数来让开发者知晓，结束当前进程（在 Node 中），并在控制台中输出堆栈跟踪来提示开发者。

### 不要忽略捕获到的问题

对于捕获到的错误不做任何处理是没有意义的。向控制台记录错误 (console.log) 也不怎么好， 因为往往会丢失在海量的控制台输出中。如果你把任意一段代码用 `try/catch` 包装那就意味着你想到这里可能会错， 因此你应该有个修复计划， 或者当错误发生时有一个代码路径。

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### 不要忽略被拒绝的 promises

理由同 try/catch。

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ 回到顶部](#目录)**

## **格式化**

格式化是主观的。 就像其它规则一样， 没有必须让你遵守的硬性规则。重点不是为了格式化而争论，这里有 [大量工具](https://standardjs.com/rules.html) 可以自动化做格式化工作，使用其中一个即可。做为工程师去争论格式化就是在浪费时间和金钱！

针对自动格式化工具不能涵盖的问题（缩进、制表符还是空格、双引号还是单引号等），这里有一些指南。

### 使用一致的大小写

JavaScript 是弱类型语言， 合理的采用大小写可以告诉你关于变量/函数等的许多消息。这些规定都是主观的，所以你的团队可以自行选择。重点在于无论选择何种风格，都需要注意保持一致性。


**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ 回到顶部](#目录)**

### 调用函数的函数和被调函数应放在较近的位置

如果一个函数调用另一个， 则在代码中这两个函数的竖直位置应该靠近。 理想情况下，保持被调用函数在被调用函数的正上方。我们倾向于从上到下阅读代码，就像读一章报纸。由于这个原因，所以保持你的代码可以按照这种方式阅读。

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ 回到顶部](#目录)**

## **注释**

### 只对存在一定业务逻辑复杂性的代码进行注释

注释是代码的辩解，不是要求。多数情况下，好的代码就是文档。

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ 回到顶部](#目录)**

### D不要在代码库中保存注释掉的代码

因为有版本控制（git、svn），把旧的代码留在历史记录即可。

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ 回到顶部](#目录)**

### 不要有日志式的注释

记住，使用版本控制！不需要僵尸代码，注释掉的代码，尤其是日志式的注释。使用 `git log` 来 获取历史记录。

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ 回到顶部](#目录)**

### 避免占位符

它们仅仅添加了干扰。让函数和变量名称与合适的缩进和格式化为你的代码提供视觉结构。

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ 回到顶部](#目录)**

## 翻译

下面是一些翻译版本:

- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**:
  [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**:
  [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**:
  [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)

**[⬆ 回到顶部](#目录)**
