<template>
  <div class="game">
    <div class="game__area">
      <div class="row">
        <audio id="1">
          <source src="./assets/do.mp3" type="audio/mpeg" />
        </audio>
        <button
          @click="addActionUser"
          class="game__area-button"
          id="button_1"
          name="1"
        ></button>
        <audio id="2">
          <source src="./assets/fa.mp3" type="audio/mpeg" />
        </audio>
        <button
          @click="addActionUser"
          class="game__area-button"
          id="button_2"
          name="2"
        ></button>
      </div>

      <div class="row">
        <audio id="3">
          <source src="./assets/lya.mp3" type="audio/mpeg" />
        </audio>
        <button
          @click="addActionUser"
          class="game__area-button"
          id="button_3"
          name="3"
        ></button>
        <audio id="4">
          <source src="./assets/re.mp3" type="audio/mpeg" />
        </audio>
        <button
          @click="addActionUser"
          class="game__area-button"
          id="button_4"
          name="4"
        ></button>
      </div>
    </div>
    <div class="game__rules">
      <button class="game__rules-start" @click="startGame">START</button>

      <p class="game__rules-score">
        Your level <span>{{ this.score }} </span>
      </p>

      <div class="game__rules-complexity">
        <label v-for="(item, index) in complexity" :key="index" :for="item.key"
          ><input
            type="radio"
            :id="item.key"
            name="complexity"
            :checked="item.checked"
            @click="changeComplexity"
          />{{ item.title }}</label
        >
      </div>

      <span :class="this.msgError ? 'active' : ''" class="game__rules-error">{{
        this.msgError
      }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      user: [],
      score: 1,
      queue: [],
      speed: 1,
      msgError: "",
      isCheck: true,
      isStart: false,
      isPreview: false,
      complexity: [
        {
          key: "easy",
          title: "Easy",
          checked: false,
          speed: 1.8,
        },
        {
          key: "medium",
          title: "Medium",
          checked: true,
          speed: 1,
        },
        {
          key: "hard",
          title: "Hard",
          checked: false,
          speed: 0.5,
        },
      ],
    };
  },
  methods: {
    changeComplexity(event) {
      this.complexity.forEach((item) => {
        if (item.key === event.target.id) this.speed = item.speed;
      });
    },
    showQueue(tmp) {
      let timer = setInterval(() => {
        if (tmp === 0) {
          clearInterval(timer);
        } else {
          this.actionButton(this.queue[tmp - 1]);
          tmp--;
        }
      }, 500 * this.speed);
    },
    actionButton(id) {
      let button = document.querySelector("button[name='" + id + "']");
      button.classList.add("active");
      setTimeout(() => button.classList.remove("active"), 300);

      let audio = document.getElementById(id);
      audio.play();
    },
    startGame() {
      this.msgError = "";
      this.isStart = true;
      this.isPreview = true;
      // create queue
      for (let i = 0; i < this.score; i++)
        this.queue.push(Math.trunc(1 + Math.random() * 3));

      this.showQueue(this.queue.length);

      this.isPreview = false;
    },
    addActionUser(event) {
      if (this.isStart) {
        this.user.push(event.target.name);
        let count = this.user.length;
        let lengthQueue = this.queue.length - count;

        if (+this.user[count - 1] !== this.queue[lengthQueue])
          this.isCheck = false;

        if (!this.isCheck) {
          this.msgError = "Fatal error";
          this.isCheck = true;
          this.queue = [];
          this.user = [];
          this.score = 1;
          return;
        }

        if (count === this.queue.length) {
          this.user = [];
          this.queue = [];
          this.score++;
          setTimeout(() => this.startGame(), 1000);
        }
      }

      if (!this.isPreview) {
        let audio = document.getElementById(event.target.name);
        audio.play();

        event.target.classList.add("active");
        setTimeout(() => event.target.classList.remove("active"), 300);
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  background-color: #252525;
}
.game {
  width: 700px;
  margin: 0 auto;

  display: flex;
  align-content: center;
  justify-content: space-between;
}
.game__area {
  width: 550px;

  display: flex;
  align-content: center;
  flex-wrap: wrap;
  justify-content: space-around;
}
.row {
  width: 100%;
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}
.game__area-button {
  width: 250px;
  height: 250px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
#button_1 {
  background-color: #c3ff91;
}
#button_1.active {
  background-color: #99ff46;
}
#button_2 {
  background-color: #91d7ff;
}
#button_2.active {
  background-color: #38b5fd;
}
#button_3 {
  background-color: #bb91ff;
}
#button_3.active {
  background-color: #8539fd;
}
#button_4 {
  background-color: #ff91e4;
}
#button_4.active {
  background-color: #bb389a;
}

.game__rules {
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
}
.game__rules-start {
  padding: 6px 30px;
  font-size: 24px;
  cursor: pointer;
}

.game__rules-score {
  color: #fff;
  font-size: 20px;
  margin-top: 30px;
}

.game__rules-complexity {
  display: flex;
  flex-direction: column;
  padding: 8px 16px;
  margin-top: 20px;
  border-radius: 4px;
  color: #fff;
  background-color: rgb(134, 134, 134);
}

.game__rules-complexity label + label {
  margin-top: 5px;
}

.game__rules-complexity input {
  margin-right: 6px;
}

.game__rules-error {
  margin-top: 15px;
  opacity: 0;
  padding: 8px 16px;
  color: #fff;
  border: 2px solid #f8491d;
  border-radius: 3px;
  background-color: #f8491dc9;
  transition: 0.2s;
}

.game__rules-error.active {
  transition: 0.2s;
  opacity: 1;
}
</style>
