使用前记得在b站上关注chifishfish哦，谢谢！



<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Website</title>
    <!-- 您的其他样式和脚本 -->
</head>
<body>
    <!-- 您的内容 -->
    <h1>Welcome to My Website</h1>
    <p>This is my homepage content.</p>
    <!-- 将 JavaScript 代码添加在此处 -->
    <script>
        (function (name, factory) {
            if (typeof window === "object") {
                window[name] = factory();
            }
        })("Ribbons", function () {
            var _w = window, _b = document.body, _d = document.documentElement;
            var random = function () {
                if (arguments.length === 1) {
                    if (Array.isArray(arguments[0])) {
                        var index = Math.round(random(0, arguments[0].length - 1));
                        return arguments[0][index];
                    }
                    return random(0, arguments[0]);
                } else if (arguments.length === 2) {
                    return Math.random() * (arguments[1] - arguments[0]) + arguments[0];
                }
                return 0;
            };
            var screenInfo = function (e) {
                var width = Math.max(0, _w.innerWidth || _d.clientWidth || _b.clientWidth || 0),
                    height = Math.max(0, _w.innerHeight || _d.clientHeight || _b.clientHeight || 0),
                    scrollx = Math.max(0, _w.pageXOffset || _d.scrollLeft || _b.scrollLeft || 0) - (_d.clientLeft || 0),
                    scrolly = Math.max(0, _w.pageYOffset || _d.scrollTop || _b.scrollTop || 0) - (_d.clientTop || 0);
                return {
                    width: width,
                    height: height,
                    ratio: width / height,
                    centerx: width / 2,
                    centery: height / 2,
                    scrollx: scrollx,
                    scrolly: scrolly
                };
            };
            var mouseInfo = function (e) {
                var screen = screenInfo(e), mousex = e ? Math.max(0, e.pageX || e.clientX || 0) : 0,
                    mousey = e ? Math.max(0, e.pageY || e.clientY || 0) : 0;
                return {
                    mousex: mousex,
                    mousey: mousey,
                    centerx: mousex - screen.width / 2,
                    centery: mousey - screen.height / 2
                };
            };
            var Point = function (x, y) {
                this.x = 0;
                this.y = 0;
                this.set(x, y);
            };
            Point.prototype = {
                constructor: Point, set: function (x, y) {
                    this.x = x || 0;
                    this.y = y || 0;
                }, copy: function (point) {
                    this.x = point.x || 0;
                    this.y = point.y || 0;
                    return this;
                }, multiply: function (x, y) {
                    this.x *= x || 1;
                    this.y *= y || 1;
                    return this;
                }, divide: function (x, y) {
                    this.x /= x || 1;
                    this.y /= y || 1;
                    return this;
                }, add: function (x, y) {
                    this.x += x || 0;
                    this.y += y || 0;
                    return this;
                }, subtract: function (x, y) {
                    this.x -= x || 0;
                    this.y -= y || 0;
                    return this;
                }, clampX: function (min, max) {
                    this.x = Math.max(min, Math.min(this.x, max));
                    return this;
                }, clampY: function (min, max) {
                    this.y = Math.max(min, Math.min(this.y, max));
                    return this;
                }, flipX: function () {
                    this.x *= -1;
                    return this;
                }, flipY: function () {
                    this.y *= -1;
                    return this;
                }
            };
            var Factory = function (options) {
                this._canvas = null;
                this._context = null;
                this._sto = null;
                this._width = 0;
                this._height = 0;
                this._scroll = 0;
                this._ribbons = [];
                this._options = {
                    colorSaturation: "80%",
                    colorBrightness: "60%",
                    colorAlpha: 0.65,
                    colorCycleSpeed: 6,
                    verticalPosition: "center",
                    horizontalSpeed: 200,
                    ribbonCount: 4,
                    strokeSize: 0,
                    parallaxAmount: -0.5,
                    animateSections: true
                };
                this._onDraw = this._onDraw.bind(this);
                this._onResize = this._onResize.bind(this);
                this._onScroll = this._onScroll.bind(this);
                this.setOptions(options);
                this.init();
            };
            Factory.prototype = {
                constructor: Factory, setOptions: function (options) {
                    if (typeof options === "object") {
                        for (var key in options) {
                            if (options.hasOwnProperty(key)) {
                                this._options[key] = options[key];
                            }
                        }
                    }
                }, init: function () {
                    try {
                        this._canvas = document.createElement("canvas");
                        this._canvas.style["display"] = "block";
                        this._canvas.style["position"] = "fixed";
                        this._canvas.style["margin"] = "0";
                        this._canvas.style["padding"] = "0";
                        this._canvas.style["border"] = "0";
                        this._canvas.style["outline"] = "0";
                        this._canvas.style["left"] = "0";
                        this._canvas.style["top"] = "0";
                        this._canvas.style["width"] = "100%";
                        this._canvas.style["height"] = "100%";
                        this._canvas.style["z-index"] = "-1";
                        // this._canvas.style["background-color"] = "#1f1f1f";
                        this._canvas.id = "bgCanvas";
                        this._onResize();
                        this._context = this._canvas.getContext("2d");
                        this._context.clearRect(0, 0, this._width, this._height);
                        this._context.globalAlpha = this._options.colorAlpha;
                        // 这里可以设置是否随着窗口的滚动而滚动
                        window.addEventListener("resize", this._onResize);
                        window.addEventListener("scroll", this._onScroll);
                        // 这里设置添加的位置
                        var body_ = document.getElementsByTagName('body')[0];
                        body_.appendChild(this._canvas);
                    } catch (e) {
                        console.warn("Canvas Context Error: " + e.toString());
                        return;
                    }
                    this._onDraw();
                }, addRibbon: function () {
                    var dir = Math.round(random(1, 9)) > 5 ? "right" : "left", stop = 1000, hide = 200, min = 0 - hide,
                        max = this._width + hide, movex = 0, movey = 0, startx = dir === "right" ? min : max,
                        starty = Math.round(random(0, this._height));
                    if (/^(top|min)$/i.test(this._options.verticalPosition)) {
                        starty = 0 + hide;
                    } else if (/^(middle|center)$/i.test(this._options.verticalPosition)) {
                        starty = this._height / 2;
                    } else if (/^(bottom|max)$/i.test(this._options.verticalPosition)) {
                        starty = this._height - hide;
                    }
                    var ribbon = [], point1 = new Point(startx, starty), point2 = new Point(startx, starty), point3 = null,
                        color = Math.round(random(0, 360)), delay = 0;
                    while (true) {
                        if (stop <= 0) break;
                        stop--;
                        movex = Math.round((Math.random() * 1 - 0.2) * this._options.horizontalSpeed);
                        movey = Math.round((Math.random() * 1 - 0.5) * (this._height * 0.25));
                        point3 = new Point();
                        point3.copy(point2);
                        if (dir === "right") {
                            point3.add(movex, movey);
                            if (point2.x >= max) break;
                        } else if (dir === "left") {
                            point3.subtract(movex, movey);
                            if (point2.x <= min) break;
                        }
                        ribbon.push({
                            point1: new Point(point1.x, point1.y),
                            point2: new Point(point2.x, point2.y),
                            point3: point3,
                            color: color,
                            delay: delay,
                            dir: dir,
                            alpha: 0,
                            phase: 0
                        });
                        point1.copy(point2);
                        point2.copy(point3);
                        delay += 4;
                        color += this._options.colorCycleSpeed;
                    }
                    this._ribbons.push(ribbon);
                }, _drawRibbonSection: function (section) {
                    if (section) {
                        if (section.phase >= 1 && section.alpha <= 0) {
                            return true;
                        }
                        if (section.delay <= 0) {
                            section.phase += 0.02;
                            section.alpha = Math.sin(section.phase) * 1;
                            section.alpha = section.alpha <= 0 ? 0 : section.alpha;
                            section.alpha = section.alpha >= 1 ? 1 : section.alpha;
                            if (this._options.animateSections) {
                                var mod = Math.sin(1 + section.phase * Math.PI / 2) * 0.1;
                                if (section.dir === "right") {
                                    section.point1.add(mod, 0);
                                    section.point2.add(mod, 0);
                                    section.point3.add(mod, 0);
                                } else {
                                    section.point1.subtract(mod, 0);
                                    section.point2.subtract(mod, 0);
                                    section.point3.subtract(mod, 0);
                                }
                                section.point1.add(0, mod);
                                section.point2.add(0, mod);
                                section.point3.add(0, mod);
                            }
                        } else {
                            section.delay -= 0.5;
                        }
                        var s = this._options.colorSaturation, l = this._options.colorBrightness,
                            c = "hsla(" + section.color + ", " + s + ", " + l + ", " + section.alpha + " )";
                        this._context.save();
                        if (this._options.parallaxAmount !== 0) {
                            this._context.translate(0, this._scroll * this._options.parallaxAmount);
                        }
                        this._context.beginPath();
                        this._context.moveTo(section.point1.x, section.point1.y);
                        this._context.lineTo(section.point2.x, section.point2.y);
                        this._context.lineTo(section.point3.x, section.point3.y);
                        this._context.fillStyle = c;
                        this._context.fill();
                        if (this._options.strokeSize > 0) {
                            this._context.lineWidth = this._options.strokeSize;
                            this._context.strokeStyle = c;
                            this._context.lineCap = "round";
                            this._context.stroke();
                        }
                        this._context.restore();
                    }
                    return false;
                }, _onDraw: function () {
                    for (var i = 0, t = this._ribbons.length; i < t; ++i) {
                        if (!this._ribbons[i]) {
                            this._ribbons.splice(i, 1);
                        }
                    }
                    this._context.clearRect(0, 0, this._width, this._height);
                    for (var a = 0; a < this._ribbons.length; ++a) {
                        var ribbon = this._ribbons[a], numSections = ribbon.length, numDone = 0;
                        for (var b = 0; b < numSections; ++b) {
                            if (this._drawRibbonSection(ribbon[b])) {
                                numDone++;
                            }
                        }
                        if (numDone >= numSections) {
                            this._ribbons[a] = null;
                        }
                    }
                    if (this._ribbons.length < this._options.ribbonCount) {
                        this.addRibbon();
                    }
                    requestAnimationFrame(this._onDraw);
                }, _onResize: function (e) {
                    var screen = screenInfo(e);
                    this._width = screen.width;
                    this._height = screen.height;
                    if (this._canvas) {
                        this._canvas.width = this._width;
                        this._canvas.height = this._height;
                        if (this._context) {
                            this._context.globalAlpha = this._options.colorAlpha;
                        }
                    }
                }, _onScroll: function (e) {
                    var screen = screenInfo(e);
                    this._scroll = screen.scrolly;
                }
            };
            return Factory;
        });
        new Ribbons();
    </script>
</body>
</html>









<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Existing Website</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
            background-image: url('your-background-image.jpg');
            background-size: cover;
            background-position: center;
        }
        .pointer {
            width: 10px;
            height: 10px;
            background: #000;
            border-radius: 50%;
            position: absolute;
            pointer-events: none;
            transform: translate(-50%, -50%);
        }
        body.is-pressed .pointer {
            background: #f00;
        }
        body.is-longpress .pointer {
            background: #0f0;
        }
    </style>
</head>
<body>
    <h1>Welcome to My Website</h1>
    <p>This is my homepage content.</p>
 <!-- 将 JavaScript 代码添加在此处 -->

    <script>
        function clickEffect() {
            let balls = [];
            let longPressed = false;
            let longPress;
            let multiplier = 0;
            let width, height;
            let origin;
            let normal;
            let ctx;
            const colours = ["#F73859", "#14FFEC", "#00E0FF", "#FF99FE", "#FAF15D"];
            const canvas = document.createElement("canvas");
            document.body.appendChild(canvas);
            canvas.setAttribute("style", "width: 100%; height: 100%; top: 0; left: 0; z-index: 99999; position: fixed; pointer-events: none;");
            const pointer = document.createElement("span");
            pointer.classList.add("pointer");
            document.body.appendChild(pointer);

            if (canvas.getContext && window.addEventListener) {
                ctx = canvas.getContext("2d");
                updateSize();
                window.addEventListener('resize', updateSize, false);
                loop();
                window.addEventListener("mousedown", function (e) {
                    pushBalls(randBetween(10, 20), e.clientX, e.clientY);
                    document.body.classList.add("is-pressed");
                    longPress = setTimeout(function () {
                        document.body.classList.add("is-longpress");
                        longPressed = true;
                    }, 500);
                }, false);
                window.addEventListener("mouseup", function (e) {
                    clearInterval(longPress);
                    if (longPressed == true) {
                        document.body.classList.remove("is-longpress");
                        pushBalls(randBetween(50 + Math.ceil(multiplier), 100 + Math.ceil(multiplier)), e.clientX, e.clientY);
                        longPressed = false;
                    }
                    document.body.classList.remove("is-pressed");
                }, false);
                window.addEventListener("mousemove", function (e) {
                    let x = e.clientX;
                    let y = e.clientY;
                    pointer.style.top = y + "px";
                    pointer.style.left = x + "px";
                }, false);
            } else {
                console.log("canvas or addEventListener is unsupported!");
            }

            function updateSize() {
                canvas.width = window.innerWidth * 2;
                canvas.height = window.innerHeight * 2;
                canvas.style.width = window.innerWidth + 'px';
                canvas.style.height = window.innerHeight + 'px';
                ctx.scale(2, 2);
                width = (canvas.width = window.innerWidth);
                height = (canvas.height = window.innerHeight);
                origin = {
                    x: width / 2,
                    y: height / 2
                };
                normal = {
                    x: width / 2,
                    y: height / 2
                };
            }
            class Ball {
                constructor(x = origin.x, y = origin.y) {
                    this.x = x;
                    this.y = y;
                    this.angle = Math.PI * 2 * Math.random();
                    if (longPressed == true) {
                        this.multiplier = randBetween(14 + multiplier, 15 + multiplier);
                    } else {
                        this.multiplier = randBetween(6, 12);
                    }
                    this.vx = (this.multiplier + Math.random() * 0.5) * Math.cos(this.angle);
                    this.vy = (this.multiplier + Math.random() * 0.5) * Math.sin(this.angle);
                    this.r = randBetween(8, 12) + 3 * Math.random();
                    this.color = colours[Math.floor(Math.random() * colours.length)];
                }
                update() {
                    this.x += this.vx - normal.x;
                    this.y += this.vy - normal.y;
                    normal.x = -2 / window.innerWidth * Math.sin(this.angle);
                    normal.y = -2 / window.innerHeight * Math.cos(this.angle);
                    this.r -= 0.3;
                    this.vx *= 0.9;
                    this.vy *= 0.9;
                }
            }

            function pushBalls(count = 1, x = origin.x, y = origin.y) {
                for (let i = 0; i < count; i++) {
                    balls.push(new Ball(x, y));
                }
            }

            function randBetween(min, max) {
                return Math.floor(Math.random() * max) + min;
            }

            function loop() {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                for (let i = 0; i < balls.length; i++) {
                    let b = balls[i];
                    if (b.r < 0) continue;
                    ctx.fillStyle = b.color;
                    ctx.beginPath();
                    ctx.arc(b.x, b.y, b.r, 0, Math.PI * 2, false);
                    ctx.fill();
                    b.update();
                }
                if (longPressed == true) {
                    multiplier += 0.2;
                } else if (!longPressed && multiplier >= 0) {
                    multiplier -= 0.4;
                }
                removeBall();
                requestAnimationFrame(loop);
            }

            function removeBall() {
                for (let i = 0; i < balls.length; i++) {
                    let b = balls[i];
                    if (b.x + b.r < 0 || b.x - b.r > width || b.y + b.r < 0 || b.y - b.r > height || b.r < 0) {
                        balls.splice(i, 1);
                    }
                }
            }
        }
        clickEffect();
    </script>
</body>
</html>


