<script>
    import translate from 'moji-translate';
    import { onMount } from 'svelte';
    import { isMaybeAlreadyAnEmoji } from 'moji-translate';

    let inp = '';
    let translatedPhrase;
    let wrds;
    let wlist = [];
    let emlist = [];
    let copyv;
    let emojiofday;
    let texttocopy;
    let copytoclip;
    let fest;
    let emojinameofday;
    let emojiofdaypng;
    let showResults = false;
    
    let festivals = [
        { name: "New Year", date: "1-1", emoji: "🎆" },
        { name: "Valentine's Day", date: "2-14", emoji: "❤️" },
        { name: "St. Patrick's Day", date: "3-17", emoji: "🍀" },
        { name: "Easter", date: "4-09", emoji: "🐰" },
        { name: "Halloween", date: "10-31", emoji: "🎃" },
        { name: "Thanksgiving", date: "11-23", emoji: "🦃" },
        { name: "Christmas", date: "12-25", emoji: "🎄" },
        { name: "Launch Day", date: "30-7", emoji: "🎉"}
    ];

    onMount(async() => {
        const today = new Date();
        const currentdate = `${today.getDate()}-${today.getMonth()}`
        fest = festivals.find(f => f.date === currentdate)
        emojiofday = fest ? fest.emoji : "🤩"
        let t = await fetch("https://raw.githubusercontent.com/muan/emojilib/main/dist/emoji-en-US.json")
        t = await t.json();
        for (const key in t) {
            if(key === emojiofday) {
                emojinameofday = t[key][0]
                emojiofdaypng = `https://emojiapi.dev/api/v1/${emojinameofday}/100.png`
                const link = document.createElement('link');
                link.rel = 'icon';
                link.type = 'image/png';
                link.href = emojiofdaypng;
                document.title = `Mojilang - ${fest ? `Happy ${fest.name}` : emojiofday}`;

                const existingFavicon = document.querySelector('link[rel="icon"]');
                if (existingFavicon) {
                    existingFavicon.remove();
                }
                document.head.appendChild(link)
            }
        }

        copytoclip = () => {
            texttocopy = copyv.textContent;
            navigator.clipboard.writeText(texttocopy);
            alert("Copied to clipboard!");
        }
    });

    function convert2moji() {
        if (inp.trim() === '') {
            showResults = false;
            return;
        }
        
        showResults = true;
        let emojis = translate.translate(inp, true);
        translatedPhrase = emojis;
        let wrds = inp.split(' ');
        wlist = [];
        for (let i = 0; i < wrds.length; i++) {
            var word = wrds[i] + " ";
            let emojisForWord = translate.getAllEmojiForWord(word);
            wlist.push({ word, emojis: emojisForWord });
        }
    }
</script>

<div class="main">
    <div class="search-container">
        <div class="logo">
            <h1>{emojiofday} Mojilang</h1>
            {#if fest}
                <p class="festival-text">Happy {fest.name} {emojiofday}</p>
            {/if}
        </div>
        
        <div class="search-box">
            <input 
                class="search-input" 
                type="text" 
                placeholder="Type to convert to emojis..." 
                bind:value={inp} 
                on:input={() => convert2moji()} 
            />
        </div>

        {#if showResults}
            <div class="results-container" class:show={showResults}>
                <div class="output" bind:this={copyv}>
                    {#if translatedPhrase}
                        {#each wlist as item}
                            {#if item.emojis !== ""}
                                {#if item.emojis.length > 1}
                                    <select class="sel">
                                        {#each item.emojis as tp}
                                            <option class="op" value={tp}>{tp}</option>
                                        {/each}
                                    </select>
                                {:else}
                                    {#each item.emojis as tp}
                                        <span class="emoji">{tp}</span>
                                    {/each}
                                {/if}
                            {:else}
                                <span class="text">{item.word}</span>
                            {/if}
                        {/each}
                        <button class="copy-button" on:click={copytoclip}>
                            Copy to clipboard
                        </button>
                    {/if}
                </div>
            </div>
        {/if}
    </div>

    <div class="footer">
        <a href="https://anshwadhwa.vercel.app/" target="_blank" rel="noopener noreferrer">
            Made by Ansh Wadhwa
        </a>
    </div>
</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600;700&display=swap');

    * {
        font-family: 'Open Sans', sans-serif;
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    .main {
        min-height: 100vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 20px;
    }

    .search-container {
        width: 100%;
        max-width: 600px;
        margin-top: 100px;
        text-align: center;
    }

    .logo {
        margin-bottom: 40px;
    }

    .logo h1 {
        font-size: 3.5rem;
        color: #ffffff;
        margin-bottom: 10px;
    }

    .festival-text {
        color: #ffffff;
        font-size: 1.2rem;
    }

    .search-box {
        width: 100%;
        margin-bottom: 20px;
    }

    .search-input {
        width: 100%;
        padding: 15px 20px;
        font-size: 1.1rem;
        border: none;
        border-radius: 24px;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        transition: all 0.3s ease;
    }

    .search-input:focus {
        outline: none;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    }

    .results-container {
        background: white;
        border-radius: 12px;
        padding: 20px;
        margin-top: 20px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        opacity: 0;
        transform: translateY(-20px);
        transition: all 0.3s ease;
    }

    .results-container.show {
        opacity: 1;
        transform: translateY(0);
    }

    .output {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        justify-content: center;
        align-items: center;
    }

    .sel {
        padding: 8px;
        border: 1px solid #eee;
        border-radius: 8px;
        font-size: 1.2rem;
        background: #f8f8f8;
    }

    .emoji {
        font-size: 1.5rem;
    }

    .text {
        font-size: 1.2rem;
        color: #333;
    }

    .copy-button {
        margin-top: 20px;
        padding: 10px 20px;
        background-color: #398AB9;
        color: white;
        border: none;
        border-radius: 20px;
        cursor: pointer;
        transition: background-color 0.3s ease;
        width: auto;
    }

    .copy-button:hover {
        background-color: #2c6f8e;
    }

    .footer {
        margin-top: auto;
        padding: 20px;
    }

    .footer a {
        color: #ffffff;
        text-decoration: none;
        font-weight: 500;
    }

    .footer a:hover {
        text-decoration: underline;
    }

    @media (max-width: 600px) {
        .search-container {
            margin-top: 50px;
        }

        .logo h1 {
            font-size: 2.5rem;
        }
    }
</style>