<template>
    <div>
        <a class="z-10 absolute right-3 top-3 transition hover:opacity-80" href="https://github.com/orhanemree/millionaire">
            <svg class="w-8 fill-white" role="img" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><title>GitHub</title><path d="M12 .297c-6.63 0-12 5.373-12 12 0 5.303 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61C4.422 18.07 3.633 17.7 3.633 17.7c-1.087-.744.084-.729.084-.729 1.205.084 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 22.092 24 17.592 24 12.297c0-6.627-5.373-12-12-12"/></svg>
        </a>
        <div class="text-center" v-if="!ctrls.started">
            <h1 class="text-4xl font-bold mb-24">Who Wants to be a Millionaire?</h1>
            <button @click="start" class="text-4xl">start</button>
        </div>
        <div v-else>
            <div>
                <div id="q">
                    <h2>{{ game.qs[game.current]?.title }}</h2>
                    <div id="choices">
                        <button :disabled="ctrls.process" @click.prevent="choice('a')"
                            :class="`${game.qs[game.current].answer === 'a' && (ctrls.correct || ctrls.wrong) ? 'correct' : (game.answer === 'a' && ctrls.wrong) ? 'wrong' : ''}`"
                        >A) {{ game.qs[game.current]?.choices?.a }}</button>
                        <button :disabled="ctrls.process" @click.prevent="choice('b')"
                            :class="`${game.qs[game.current].answer === 'b' && (ctrls.correct || ctrls.wrong) ? 'correct' : (game.answer === 'b' && ctrls.wrong) ? 'wrong' : ''}`"
                        >B) {{ game.qs[game.current]?.choices?.b }}</button>
                        <button :disabled="ctrls.process" @click.prevent="choice('c')"
                            :class="`${game.qs[game.current].answer === 'c' && (ctrls.correct || ctrls.wrong) ? 'correct' : (game.answer === 'c' && ctrls.wrong) ? 'wrong' : ''}`"
                        >C) {{ game.qs[game.current]?.choices?.c }}</button>
                        <button :disabled="ctrls.process" @click.prevent="choice('d')"
                            :class="`${game.qs[game.current].answer === 'd' && (ctrls.correct || ctrls.wrong) ? 'correct' : (game.answer === 'd' && ctrls.wrong) ? 'wrong' : ''}`"
                        >D) {{ game.qs[game.current]?.choices?.d }}</button>
                    </div>    
                    <div id="final" v-if="ctrls.final">
                        <h2>Is it your final answer?</h2>
                        <div>
                            <button :disabled="ctrls.process" @click="final(1)">Yes</button>
                            <button :disabled="ctrls.process" @click="final(0)">No</button>
                        </div>
                    </div>
                </div>
                <div id="scores">
                    <ul>
                        <li v-for="(score, i) in game.scores" :key="score">
                            <div :class="`${game.current === i ? 'current' : ''}`">{{ score }}</div>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            ctrls: { started: false, final: false, correct: false, wrong: false, process: true },
            game: { qs: [], scores: [], /*lifelines: [],*/ sounds: [], current: 0, answer: "" }
        }
    },
    methods: {
        delay(sec) {
            return new Promise(resolve => setTimeout(resolve, sec));
        },
        async start() {
            this.ctrls.started = true;
            this.getSounds();
            this.game.sounds.play.play();
            const n = 7;
            this.game.qs = this.getQ(n);
            this.game.scores = this.createScores(n);
            await this.delay(4000);
            this.ctrls.process = false;
            this.game.sounds.bg.play();
            console.log(this.game);
        },
        getQ(n) {
            let qs = []; // qs: questions
            for (let i = 1; i <= n; ++i){
                const q = { // q: question
                    title: `title ${i}`,
                    choices: {
                        a: `a ${i}`,
                        b: `b ${i}`,
                        c: `c ${i}`,
                        d: `d ${i}`,
                    },
                    answer: "a"
                }
                qs.push(q);
            }
            return qs;
        },
        createScores(n) {
            let scores = [];
            for (let i = 1; i <= n; ++i){
                scores.push((i * i * 5 * 10000));
            }
            return scores;
        },
        getSounds() {
            this.game.sounds.play = new Audio("https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=lets+play!&filename=22/226000-d8c5d02e-e2d8-4552-a34f-ee006709e7b3.mp3");
            this.game.sounds.bg = new Audio("https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=1000000+music&filename=22/226000-d812f5cc-04ab-4858-9045-00731ef1f3f3.mp3");
            this.game.sounds.final = new Audio("https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=final+answer&filename=22/226000-99ef9794-cc3e-46b1-ab80-0a5ff96a0639.mp3");
            this.game.sounds.correct = new Audio("https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=correct+answer!&filename=22/226000-40bfdd88-c10f-4f75-99af-8569db18de8e.mp3");
            this.game.sounds.wrong = new Audio("https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=wrong+answer&filename=22/226000-9027b0d6-7a4f-4ee7-946f-6d011370681f.mp3");
        },
        choice(c) {
            this.stopSounds();
            this.game.sounds.final.play();
            this.game.answer = c;
            this.ctrls.final = true;
        },
        async final(f) {
            this.ctrls.process = true;
            this.stopSounds();
            this.ctrls.final = false;
            if (f) {
                if (this.game.answer === this.game.qs[this.game.current].answer) {
                    this.ctrls.correct = true;
                    this.game.sounds.correct.play();
                    await this.delay(4000);
                    this.nextQ();
                } else {
                    this.ctrls.wrong = true;
                    this.game.sounds.wrong.play();
                }
            } else {
                this.game.sounds.bg.play();
                this.ctrls.process = false;
            }
        },
        async nextQ() {
            if (this.game.current + 1 < this.game.qs.length) {
                this.ctrls.process = true;
                this.ctrls.correct = false;
                this.stopSounds();
                this.game.sounds.play.play();
                ++this.game.current;
                await this.delay(4000);
                this.ctrls.process = false;
                this.game.sounds.bg.play();
            } else {
                console.log("finished");
            }
        },
        stopSounds() {
            const sounds = Object.entries(this.game.sounds);
            for (let i = 0; i < sounds.length; ++i) {
                sounds[i][1].pause();
                sounds[i][1].currentTime = 0;
            }
        }
    }
}
</script>
