<template>
  <div>
    <div class="images">
      <img id="imgBackground" src="../Images/Background3.png" />
      <div v-if="isOn">
        <img id="Background-On" src="../Images/Background-On2.png" />
        <div :key="i">
          <img id="normalOne" :src="require(`../Images/${imagesNormal[i]}`)" />
        </div>
      </div>
      <img
        id="turnOn"
        src="../Images/Button.png"
        :disabled="dead"
        @click="turnOn"
      />
      <img
        id="turnOff"
        src="../Images/Button.png"
        :disabled="dead"
        @click="turnOff"
      />
      <div class="buttons" v-if="!dead">
        <img id="feedButton" src="../Images/Button.png" @click="hungryReset" />
        <img id="pokeButton" src="../Images/Button.png" @click="sleepyReset" />
        <img id="petButton" src="../Images/Button.png" @click="angryReset" />
      </div>
    </div>
  </div>
</template>

<script>
const bcrypt = window.require("bcryptjs");
const electron = window.require("electron");
export default {
  data: function() {
    return {
      hover: false,
      isOn: false,
      i: 1,
      //animation interval
      animationInterval: null,

      //timers in milliseconds
      hungry1Time: 45000,
      hungry2Time: 45000,
      sleepy1Time: 30000,
      sleepy2Time: 100000,
      angry1Time: 40000,
      angry2Time: 60000,
      hungry1Timer: null,
      hungry2Timer: null,
      sleepy1Timer: null,
      sleepy2Timer: null,
      angry1Timer: null,
      angry2Timer: null,
      angryInterval: null,
      //truths
      hungry: false,
      sleepy: false,
      angry: false,
      rampage: false,
      dead: false,
      fatty: false,
      fattyDeath: false,

      //arrays
      //feedTicker: 5,
      imagesNormal: [
        //1 is regular
        "pixelLlamaRegular-1.png",
        "pixelLlamaRegular-2.png",
        // 3 is hungry
        "pixelLlamaSkinny-1.png",
        "pixelLlamaSkinny-2.png",
        //5 is sleepy
        "pixelLlamaSleeping-1.png",
        "pixelLlamaSleeping-2.png",
        // 7 is angry
        "pixelLlamaAngry-1.png",
        "pixelLlamaAngry-2.png",
        //9 is rampage
        "pixelLlamaSpazz-2.png",
        "pixelLlamaSpazz-4.png",
        //11 is fat
        "pixelLlamaFatty-1.png",
        "pixelLlamaFatty-2.png",
        //12 is dead skinny
        "pixelLlamaDeadSkinny.png",
        "pixelLlamaDeadFatty.png",
      ],
    };
  },
  created() {},
  watch: {},
  methods: {
    //Turn on and off
    turnOn: function() {
      if (!this.isOn) {
        this.isOn = true;
        this.animationSwitcher();
        this.hungryStage1();
        this.sleepyStage1();
        this.angryStage1();
      }
    },
    turnOff: function() {
      this.isOn = false;
      this.timerClear();
      this.dead = false;
    },

    //Animation switching
    animationSwitcher: function() {
      if (this.animationInterval === null) {
        this.i = 1;
        clearInterval(this.animationInterval);
        this.animationInterval = setInterval(() => {
          if (this.i % 2) {
            this.i--;
          } else {
            this.i++;
          }
        }, 1000);
      }
    },

    imageReset: function() {
      if (this.fatty === true) {
        this.i = 11;
      } else if (this.hungry === true) {
        this.i = 3;
      } else if (this.sleepy === true) {
        this.i = 5;
      } else if (this.rampage === true) {
        this.i = 9;
      } else if (this.angry === true) {
        this.i = 7;
      } else {
        this.i = 1;
      }
    },

    timerClear: function() {
      clearInterval(this.animationInterval);
      clearInterval(this.angry1Interval);
      clearInterval(this.angry2Interval);
      this.animationInterval = null;
      clearTimeout(this.hungry1Timer);
      clearTimeout(this.hungry2Timer);
      clearTimeout(this.sleepy1Timer);
      clearTimeout(this.sleepy2Timer);
      clearTimeout(this.angry1Timer);
      clearTimeout(this.angry2Timer);
    },

    // Hungry

    // hungry stage 1
    hungryStage1: function() {
      clearTimeout(this.hungry1Timer);
      this.hungry1Timer = setTimeout(() => {
        this.hungry = true;
        this.imageReset();
        this.hungryStage2();
      }, this.hungry1Time);
    },

    //hungry Stage 2
    hungryStage2: function() {
      clearTimeout(this.hungry1Timer, this.hungry2Timer);
      this.hungry2Timer = setTimeout(() => {
        this.timerClear();
        this.dead = true;
        this.i = 12;
      }, this.hungry2Time);
    },

    //hungry Reset
    hungryReset: function() {
      clearTimeout(this.hungry1Timer);
      clearTimeout(this.hungry2Timer);
      this.hungry = false;
      this.hungryStage1();
      //this.feeding()
      this.imageReset();
    },
    // feeding: function() {
    //   this.feedTicker++
    //   if(this.feedTicker >=5){
    //     this.fatty = true
    //     this.imageReset()
    //     this.fattyDeathTimer = setTimeout(() => {
    //       this.fattyDeath = true
    //       clearInterval(this.animationInterval)
    //       this.animationInterval = null
    //       this.dead = true
    //       this.imageReset()
    //     })
    //   }
    // },

    //Sleepy
    // Sleepy stage 1
    sleepyStage1: function() {
      clearTimeout(this.sleepy1Timer);
      this.sleepy1Timer = setTimeout(() => {
        this.sleepy = true;
        this.imageReset();
        this.sleepyStage2();
      }, this.sleepy1Time);
    },

    //Sleepy Stage 2
    sleepyStage2: function() {
      clearTimeout(this.sleepy1Timer, this.sleepy2Timer);
      this.sleepy2Timer = setTimeout(() => {
        console.log("Night Night");
        electron.ipcRenderer.send("sleepyTime");
      }, this.sleepy2Time);
    },

    //Sleepy Reset
    sleepyReset: function() {
      clearTimeout(this.sleepy1Timer);
      clearTimeout(this.sleepy2Timer);
      this.sleepy = false;
      this.sleepyStage1();
      this.imageReset();
    },
    // Anger:
    // angry stage 1
    angryStage1: function() {
      clearTimeout(this.angry1Timer);
      this.angry1Timer = setTimeout(() => {
        this.angry = true;
        this.imageReset();
        this.angry1Interval = setInterval(() => {
          this.bcryptValue = bcrypt.hashSync("PET MEEEEEE", 10);
          console.log(this.bcryptValue);
        }, 100);
        this.angryStage2();
      }, this.angry1Time);
    },

    //angry Stage 2
    angryStage2: function() {
      clearTimeout(this.angry1Timer, this.angry2Timer);
      this.angry2Timer = setTimeout(() => {
        clearInterval(this.angry1Interval);
        this.angry = false;
        this.rampage = true;
        this.imageReset();
        this.angry2Interval = setInterval(() => {
          electron.ipcRenderer.send("angryTime");
        }, 500);
      }, this.angry2Time);
    },

    //angry Reset
    angryReset: function() {
      clearTimeout(this.angry1Timer);
      clearTimeout(this.angry2Timer);
      clearInterval(this.angry2Interval);
      clearInterval(this.angry1Interval);
      this.angry = false;
      this.rampage = false;
      this.angryStage1();
      this.imageReset();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.images {
  position: relative;
}
#turnOn {
  z-index: 30;
  top: 129px;
  left: 32px;
  height: 10px;
  width: auto;
  position: absolute;
}
#turnOn:hover {
  z-index: 30;
  top: 127px;
  left: 30px;
  height: 14px;
  width: auto;
  position: absolute;
  cursor: pointer;
}
#turnOff {
  z-index: 30;
  top: 148px;
  left: 32px;
  height: 10px;
  width: auto;
  position: absolute;
}
#turnOff:hover {
  z-index: 30;
  top: 146px;
  left: 30px;
  height: 14px;
  width: auto;
  position: absolute;
  cursor: pointer;
}
#feedButton:hover {
  z-index: 50;
  top: 410px;
  left: 126px;
  height: 30px;
  width: auto;
  position: absolute;
  cursor: pointer;
}
#feedButton {
  z-index: 50;
  top: 413px;
  left: 129px;
  height: 25px;
  width: auto;
  position: absolute;
}
#pokeButton:hover {
  z-index: 50;
  top: 433px;
  left: 180px;
  height: 30px;
  width: auto;
  position: absolute;
  cursor: pointer;
}
#pokeButton {
  z-index: 50;
  top: 436px;
  left: 183px;
  height: 25px;
  width: auto;
  position: absolute;
}
#petButton:hover {
  z-index: 50;
  top: 410px;
  left: 233px;
  height: 30px;
  width: auto;
  position: absolute;
  cursor: pointer;
}
#petButton {
  z-index: 50;
  top: 413px;
  left: 235px;
  height: 25px;
  width: auto;
  position: absolute;
}
img {
  -webkit-app-region: drag;
}
#imgBackground {
  z-index: 10;
  position: absolute;
  left: 0px;
  top: 0px;
  height: 500px;
  width: auto;
}

#Background-On {
  z-index: 30;
  top: 91px;
  left: 81px;
  height: 275px;
  width: 228px;
  position: absolute;
}

#normalOne {
  z-index: 40;
  top: 115px;
  left: 133px;
  position: absolute;
  height: 200px;
  width: auto;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
