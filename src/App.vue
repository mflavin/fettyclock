<template>
  <div id="app">
    <div class="header">
      <h1>It's always 1738 somewhere...</h1>
      <a class="btn-info" @click="findFetty">Scroll to Fetty Clock</a>
    </div>
    <div v-for="n in 1440" :key="n">
      <div class="clock">
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
import dayjs from 'dayjs';
import advancedFormat from 'dayjs/plugin/advancedFormat';
dayjs.extend(advancedFormat);

export default {
  name: 'app',
  methods: {
    findFetty() {
      if (document.getElementById('fetty')) {
        document.getElementById('fetty').scrollIntoView({behavior: "smooth", block: "center", inline: "nearest"});
      }
    },
  },
  mounted() {
   function setDate() {
     const secondHand = document.getElementsByClassName('second-hand');
     const minsHand = document.getElementsByClassName('min-hand');
     const hourHand = document.getElementsByClassName('hour-hand');

     for (var i = 0; i < hourHand.length; i++) {
       const now = dayjs().subtract(i, 'minutes');

       const seconds = now.format('ss');
       const secondsDegrees = ((seconds / 60) * 360) + 90;
       secondHand[i].style.transform = `rotate(${secondsDegrees}deg)`;

       const mins = now.format('mm');
       const minsDegrees = ((mins / 60) * 360) + ((seconds/60)*6) + 90;
       minsHand[i].style.transform = `rotate(${minsDegrees}deg)`;

       const hour = now.format('kk');
       const hourDegrees = ((hour / 12) * 360) + ((mins/60)*30) + 90;
       hourHand[i].style.transform = `rotate(${hourDegrees}deg)`;

       const img = hourHand[i].closest('.inner-clock-face');
       const time = dayjs(now).format('kk:mm');
       if (time === '17:38') {
         img.id = 'fetty';
       } else {
         if (img) {
           img.removeAttribute('id');
         }
       }
     }
   }
   setInterval(setDate, 1000);
   setDate();
 }
}
</script>

<style media="screen">
  /* http://meyerweb.com/eric/tools/css/reset/
     v2.0 | 20110126
     License: none (public domain)
  */
  html, body, div, span, applet, object, iframe,
  h1, h2, h3, h4, h5, h6, p, blockquote, pre,
  a, abbr, acronym, address, big, cite, code,
  del, dfn, em, img, ins, kbd, q, s, samp,
  small, strike, strong, sub, sup, tt, var,
  b, u, i, center,
  dl, dt, dd, ol, ul, li,
  fieldset, form, label, legend,
  table, caption, tbody, tfoot, thead, tr, th, td,
  article, aside, canvas, details, embed,
  figure, figcaption, footer, header, hgroup,
  menu, nav, output, ruby, section, summary,
  time, mark, audio, video {
  	margin: 0;
  	padding: 0;
  	border: 0;
  	font-size: 100%;
  	font: inherit;
  	vertical-align: baseline;
  }
  /* HTML5 display-role reset for older browsers */
  article, aside, details, figcaption, figure,
  footer, header, hgroup, menu, nav, section {
  	display: block;
  }
  body {
  	line-height: 1;
  }
  ol, ul {
  	list-style: none;
  }
  blockquote, q {
  	quotes: none;
  }
  blockquote:before, blockquote:after,
  q:before, q:after {
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
    font-size: 10px;
  }

  body {
    margin: 0;
    font-size: 2rem;
    display: flex;
    flex: 1;
    min-height: 100vh;
    align-items: center;
    font-family: Arial, Helvetica, sans-serif;
  }

  #app {
    display: flex;
    flex-flow: wrap;
  }
  #fetty {
    background: url('https://upload.wikimedia.org/wikipedia/en/thumb/a/a9/Fetty_Wap_%E2%80%93_Fetty_Wap.png/220px-Fetty_Wap_%E2%80%93_Fetty_Wap.png');
    background-size: cover;
  }

  .btn-info {
    display: inline-block;
    font-weight: 400;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    border: 1px solid transparent;
    padding: .375rem .75rem;
    margin-right: 15rem;
    font-size: 2rem;
    line-height: 1.5;
    border-radius: .25rem;
    color: #fff;
    background-color: #17a2b8;
    border-color: #17a2b8;
    text-decoration: none;
    cursor: pointer;
  }

  .clock {
    width: 30rem;
    height: 30rem;
    border: 3px solid #545271;
    border-radius: 50%;
    margin: 100px 25px 0px 25px;
    position: relative;
    padding: 1rem;
    -webkit-box-shadow: 0 20px 30px rgba(104,75,106,0.65);
    -moz-box-shadow: 0 20px 30px rgba(104,75,106,0.65);
    box-shadow: 0 20px 30px rgba(104,75,106,0.65);
    background: #545271;
  }

  .outer-clock-face {
    position: relative;
    width: 100%;
    height: 100%;
    border-radius: 100%;
    background: #fefefc;
    -webkit-box-shadow: 0 20px 10px rgba(62,47,63,0.45);
    -moz-box-shadow: 0 20px 10px rgba(62,47,63,0.45);
    box-shadow: 0 20px 10px rgba(62,47,63,0.45);
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
    padding: 10px 0;
    background: #555;
    color: #f1f1f1;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 999;
    text-align: end;
    display: flex;
    justify-content: space-evenly;
  }

  .header h1 {
    font-size: 2em;
  }
</style>
