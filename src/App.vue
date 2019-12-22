<template>
<div id="app">

  <div class="header">
    <h1>It's always 1738 somewhere...</h1>
    <div class="buttons">
      <a v-if="!showClose" class="btn btn-find" @click="findFetty(0)">Find Fetty Clock</a>
      <a class="btn btn-show" @click="alwaysShowFetty">
        {{allClocks ? 'Only Show Timezones' : 'Always Show Fetty Clock'}}
      </a>
      <a v-if="!allClocks" class="btn btn-warn" @click="showClose = !showClose">
        {{showClose ? 'Hide Times Close To 1738' : 'Show Times Close To 1738'}}
      </a>
    </div>
  </div>

  <div class="sidenav">
    <div v-if="showClose">
      <h1 v-if="!allClocks">Almost 1738...</h1>
      <div v-for="f in allFetty" :key="f.i+'_fetty'">
        <a style="margin: 10px 0 !important;" class="btn btn-find" @click="findFetty(f.i)"> Fetty @{{f.time}}</a>
      </div>
    </div>
  </div>

  <div v-for="n in (allClocks ? 1440 : getTimezones)" :key="n+'_clock'">
    <div class="clock" :id="n+'_fettyclock'">
      <h1 class="location">
        {{allClocks ? `Your time minus ${timeDiff(n)}.` : getLocation(n)}}
      </h1>
      <div class="outer-clock-face">
        <div class="marking marking-one"></div>
        <div class="marking marking-two"></div>
        <div class="marking marking-three"></div>
        <div class="marking marking-four"></div>
        <div class="inner-clock-face">
          <div class="hand hour-hand"></div>
          <div class="hand min-hand"></div>
          <div class="hand second-hand"></div>
        </div>
      </div>
    </div>
  </div>

</div>
</template>

<script>
/* eslint-disable */
import timezones from './timezones.json'
import dayjs from 'dayjs';
import advancedFormat from 'dayjs/plugin/advancedFormat';
dayjs.extend(advancedFormat);

export default {
  name: 'app',
  data: () => ({
    allClocks: false,
    showClose: false,
    fetties: [],
  }),
  computed: {
    getTimezones() {
      return Object.keys(timezones).length;
    },
    allFetty() {
      return this.fetties;
    }
  },
  methods: {
    timeDiff(diff) {
      if (diff < 60) {
        return `${diff} minutes`;
      } else if (diff < 1440) {
        const mins = diff % 60;
        const hours = (diff - mins) / 60;
        return `${hours} hours and ${mins} minutes`;
      } else {
        return `1 day`;
      }
    },
    addFetty(i, time) {
      const fetty = this.fetties.find(f => f.i === i);
      // Updates time
      if (fetty) {
        if (fetty.time !== time) {
          fetty.time = time;
        }
      }
      // Chceks if fetty already exists
      if (!fetty) {
        this.fetties.push({
          i,
          time,
        });
      }
    },
    deleteFetty(i) {
      const fetty = this.fetties.find(f => f.i === i);
      const idx = this.fetties.indexOf(fetty);
      this.fetties.splice(idx, 1);
    },
    alwaysShowFetty() {
      const fetties = document.getElementsByClassName('fetty');
      this.allClocks = !this.allClocks;
      if (this.allClocks) {
        for (var i = 0; i < fetties.length; i++) {
          if (fetties[i].classList.contains('fetty')) {
            fetties[i].classList.remove('fetty');
          }
        }
        this.fetties = [];
      }
    },
    findFetty(i) {
      if (i) {
        if (document.getElementById(i + '_fettyclock')) {
          document.getElementById(i + '_fettyclock').scrollIntoView({
            behavior: "smooth",
            block: "center",
            inline: "nearest"
          });
        } else {
          alert('No Fetty Clock On Page')
        }
      } else {
        if (document.getElementsByClassName('fetty')[0]) {
          document.getElementsByClassName('fetty')[0].scrollIntoView({
            behavior: "smooth",
            block: "center",
            inline: "nearest"
          });
        } else {
          alert('No Fetty Clock On Page')
        }
      }
    },
    getLocation(i) {
      const idx = i - 1;
      const key = Object.keys(timezones)[idx];
      const time = timezones[key];
      const location = time.substr(12);
      return location;
    },
    offsetTime(i) {
      // Return days minus 1 minutes, sends back 1440 clocks
      if (this.allClocks) {
        return dayjs().subtract(i, 'minutes');
      }
      // Return days minus UTC offsetTime, sends back 250 clocks
      else {
        const key = Object.keys(timezones)[i];
        const time = timezones[key];
        const addORsub = time[4];
        const hours = time.substr(5, 2);
        const mins = time.substr(8, 2);
        const totalDiff = parseFloat(hours) + parseFloat(mins / 60);

        if (addORsub === '+') {
          return dayjs().add(totalDiff, 'hours');
        } else {
          return dayjs().subtract(totalDiff, 'hours');
        }
      }
      return dayjs();
    }
  },
  mounted() {
    let scrollHeight = window.scrollY;
    let header = document.getElementsByClassName('header')[0];
    let sidenav = document.getElementsByClassName('sidenav')[0];

    window.onscroll = function() {
      if (window.scrollY > scrollHeight && scrollHeight > 50) {
        header.classList.add("slide-up");
        sidenav.classList.add("slide-up");
        header.classList.remove("slide-down");
        sidenav.classList.remove("slide-down");
      } else if (window.scrollY < scrollHeight) {
        header.classList.remove("slide-up");
        sidenav.classList.remove("slide-up");
        header.classList.add("slide-down");
        sidenav.classList.add("slide-down");
      }
      scrollHeight = window.scrollY;
    };

    const self = this;

    function setDate() {
      const secondHand = document.getElementsByClassName('second-hand');
      const minsHand = document.getElementsByClassName('min-hand');
      const hourHand = document.getElementsByClassName('hour-hand');

      for (var i = 0; i < hourHand.length; i++) {
        const now = self.offsetTime(i);

        const seconds = now.format('ss');
        const secondsDegrees = ((seconds / 60) * 360) + 90;
        secondHand[i].style.transform = `rotate(${secondsDegrees}deg)`;

        const mins = now.format('mm');
        const minsDegrees = ((mins / 60) * 360) + ((seconds / 60) * 6) + 90;
        minsHand[i].style.transform = `rotate(${minsDegrees}deg)`;

        const hour = now.format('kk');
        const hourDegrees = ((hour / 12) * 360) + ((mins / 60) * 30) + 90;
        hourHand[i].style.transform = `rotate(${hourDegrees}deg)`;

        const img = hourHand[i].closest('.inner-clock-face');
        const time = dayjs(now).format('kk:mm');
        if (time === '17:38') {
          img.classList.add('fetty');
        } else {
          if ('17:59' >= time && time >= '17:00' && self.showClose) {
            if (!self.allClocks) {
              img.classList.add('fetty');
              self.addFetty(i, time)
            }
          } else {
            if (img.classList.contains('fetty')) {
              img.classList.remove('fetty');
              self.deleteFetty(i)
            }
          }
        }
      }
    }
    setInterval(setDate, 1000);
    setDate();
  },
}
</script>

<style media="screen">
/* http://meyerweb.com/eric/tools/css/reset/
     v2.0 | 20110126
     License: none (public domain)
  */
html,
body,
div,
span,
applet,
object,
iframe,
h1,
h2,
h3,
h4,
h5,
h6,
p,
blockquote,
pre,
a,
abbr,
acronym,
address,
big,
cite,
code,
del,
dfn,
em,
img,
ins,
kbd,
q,
s,
samp,
small,
strike,
strong,
sub,
sup,
tt,
var,
b,
u,
i,
center,
dl,
dt,
dd,
ol,
ul,
li,
fieldset,
form,
label,
legend,
table,
caption,
tbody,
tfoot,
thead,
tr,
th,
td,
article,
aside,
canvas,
details,
embed,
figure,
figcaption,
footer,
header,
hgroup,
menu,
nav,
output,
ruby,
section,
summary,
time,
mark,
audio,
video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}

/* HTML5 display-role reset for older browsers */
article,
aside,
details,
figcaption,
figure,
footer,
header,
hgroup,
menu,
nav,
section {
  display: block;
}

body {
  line-height: 1;
}

ol,
ul {
  list-style: none;
}

blockquote,
q {
  quotes: none;
}

blockquote:before,
blockquote:after,
q:before,
q:after {
  content: '';
  content: none;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
}


/* Custom Styles begin below */
html {
  background: #ff9998;
  text-align: center;
  font-size: 5px;
}

body {
  margin: 0;
  font-size: 2em;
  display: flex;
  flex: 1;
  min-height: 100vh;
  align-items: center;
  font-family: Arial, Helvetica, sans-serif;
  margin-top: 13em;
}

#app {
  display: flex;
  flex-flow: wrap;
  justify-content: center;
}

.fetty {
  background: url('https://upload.wikimedia.org/wikipedia/en/thumb/a/a9/Fetty_Wap_%E2%80%93_Fetty_Wap.png/220px-Fetty_Wap_%E2%80%93_Fetty_Wap.png') !important;
  background-size: cover !important;
}

.btn {
  display: inline-block;
  font-weight: 400;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  border: 1px solid transparent;
  padding: .375em .75em;
  margin: 10px 20px;
  font-size: 1em;
  line-height: 1.5;
  border-radius: .25em;
  color: #fff;
  text-decoration: none;
  cursor: pointer;
}

.btn-find {
  background-color: #17a2b8;
  border-color: #17a2b8;
}

.btn-show {
  background-color: #28a745;
  border-color: #28a745;
}

.btn-warn {
  background-color: #ffc107;
  border-color: #ffc107;
}

.clock {
  display: flex;
  flex-direction: row;
  justify-content: center;

  width: 30rem;
  height: 30rem;
  border: 3px solid #545271;
  border-radius: 50%;
  margin: 100px 25px 0px 25px;
  position: relative;
  padding: 1rem;
  -webkit-box-shadow: 0 20px 30px rgba(104, 75, 106, 0.65);
  -moz-box-shadow: 0 20px 30px rgba(104, 75, 106, 0.65);
  box-shadow: 0 20px 30px rgba(104, 75, 106, 0.65);
  background: #545271;
}

.outer-clock-face {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 100%;
  background: #fefefc;
  -webkit-box-shadow: 0 20px 10px rgba(62, 47, 63, 0.45);
  -moz-box-shadow: 0 20px 10px rgba(62, 47, 63, 0.45);
  box-shadow: 0 20px 10px rgba(62, 47, 63, 0.45);
  overflow: hidden;
}

.inner-clock-face {
  position: absolute;
  top: 0%;
  left: 0%;
  width: 100%;
  height: 100%;
  background: url('https://cdn.shopify.com/s/files/1/0054/5157/9462/products/Remy-Martin-1738-Cognac-750-ml_1_1200x1200.png?v=1547605497');
  background-size: cover;
  -webkit-border-radius: 100%;
  -moz-border-radius: 100%;
  border-radius: 100%;
  z-index: 1;
}

.inner-clock-face::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 16px;
  height: 16px;
  border-radius: 18px;
  margin-left: -9px;
  margin-top: -6px;
  background: #4d4b63;
  z-index: 11;
}

.hand {
  width: 50%;
  right: 50%;
  height: 6px;
  background: #61afff;
  position: absolute;
  top: 50%;
  border-radius: 6px;
  transform-origin: 100%;
  transform: rotate(90deg);
  transition-timing-function: cubic-bezier(0.1, 2.7, 0.58, 1);
}

.hand.hour-hand {
  width: 20%;
  z-index: 3;
}

.hand.min-hand {
  height: 3px;
  z-index: 10;
  width: 35%;
}

.hand.second-hand {
  background: #ff5e5e;
  width: 40%;
}

.header {
  background-color: #111;
  color: #f1f1f1;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 99;
  text-align: end;
  display: block;
  text-align: center;
  margin-left: 130px;
  width: calc(100% - 130px);
}

.header h1 {
  font-size: 2em;
  margin-bottom: 15px;
}

.buttons {
  display: flex;
  justify-content: space-evenly;
  flex-wrap: wrap;
}

.location {
  font-weight: bold;
  position: absolute;
  top: 200px;
  font-size: 2em;
}

.sidenav {
  width: 130px;
  position: fixed;
  z-index: 90;
  top: 0;
  left: 0;
  background-color: #111;
  overflow-x: hidden;
}

.sidenav,
.header {
  height: 180px;
  padding: 10px 0;
}

.slide-up {
  transform: translateY(-160px);
  transition-property: all;
  transition-duration: .5s;
}

.slide-down {
  /* transform: translateY(130px); */
  transition-property: all;
  transition-duration: .5s;
}

.sidenav h1 {
  color: #f1f1f1;
}

@media only screen and (min-width: 470px) {
  html {
    font-size: 6px;
  }

  body {
    margin-top: 10em;
  }

  .clock {
    margin-bottom: 50px;
  }

  .location {
    top: 215px;
  }
}

@media only screen and (min-width: 535px) {
  html {
    font-size: 7px;
  }

  .location {
    top: 250px;
  }
}

@media only screen and (min-width: 827px) {
  html {
    font-size: 10px;
  }

  body {
    margin-left: 160px;
    margin-top: 7em;
  }

  .header {
    margin-left: 0;
    width: 100%;
  }

  .sidenav {
    height: 100%;
    width: 160px;
    top: 0;
    left: 0;
    overflow-x: hidden;
    padding-top: 170px;
  }

  .slide-up {
    transform: unset;
  }

  .location {
    top: 330px;
  }
}
</style>
