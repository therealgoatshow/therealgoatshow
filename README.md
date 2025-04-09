No worries. If you can’t find or upload the ZIP file directly on your iPhone, you can manually create each file in your GitHub repository using the file contents I provided.

Here’s how to do it step-by-step:

⸻

Option 1: Manually Create Files on GitHub

Step 1: Create a New Repository
	1.	Open Safari on your iPhone and go to GitHub (use desktop mode if needed).
	2.	Tap the + icon at the top-right and select New repository.
	3.	Name your repository (for example, summertide-tracker), add a description if you like, and click Create repository.

Step 2: Create Each File in the Repository

You will add four files: index.js, package.json, .env.example, and README.md.

A. Create index.js
	1.	In your new repository, tap Add file → Create new file.
	2.	Name it: index.js
	3.	Copy and paste the following code into the file editor:

const { Client, GatewayIntentBits, REST, Routes, SlashCommandBuilder } = require('discord.js');
require('dotenv').config();

const bot = new Client({ intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent] });

let grindList = {};
let isActive = true;

const deputies = [
  "arxangelos", "Hajduk \"The Jinxed\"", "x4jduk",
  "belvitaftw", "GOAT", "THE GREATEST OF ALL TIMES"
];

const commands = [
  new SlashCommandBuilder()
    .setName('setlevel')
    .setDescription('Set or remove your grind level')
    .addStringOption(opt =>
      opt.setName('level').setDescription('Your desired level')
    ),
  new SlashCommandBuilder()
    .setName('list')
    .setDescription('Show current grind list')
].map(cmd => cmd.toJSON());

const rest = new REST({ version: '10' }).setToken(process.env.TOKEN);

(async () => {
  try {
    await rest.put(
      Routes.applicationGuildCommands(process.env.CLIENT_ID, process.env.GUILD_ID),
      { body: commands }
    );
    console.log("Slash commands registered.");
  } catch (err) {
    console.error(err);
  }
})();

bot.on('ready', () => {
  console.log(`Bot online as ${bot.user.tag}`);
});

bot.on('interactionCreate', async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  const user = interaction.user.username;

  if (!isActive) {
    return interaction.reply({ content: "Nah. I'm dead. Blame the GOATs.", ephemeral: true });
  }

  if (interaction.commandName === 'setlevel') {
    const level = interaction.options.getString('level');
    if (level) {
      grindList[user] = level;
      await interaction.reply(`Alright ${user}, you're set to grind at level ${level}. Don't disappoint me.`);
    } else {
      delete grindList[user];
      await interaction.reply(`You’re off the list, ${user}. Don't get lazy.`);
    }
  }

  if (interaction.commandName === 'list') {
    if (Object.keys(grindList).length === 0) {
      return interaction.reply("Nobody's grinding right now. Bums.");
    }

    const list = Object.entries(grindList)
      .map(([name, level]) => `${name} → Level ${level}`)
      .join('\\n');

    await interaction.reply(`Here's the grind list:\n${list}`);
  }
});

bot.on('messageCreate', (msg) => {
  const content = msg.content.toLowerCase();
  const user = msg.author.username;

  if (!deputies.includes(user)) return;

  if (content.includes("let’s go guys") || content.includes("lets go guys")) {
    isActive = true;
    grindList = {};
    msg.reply("Yooo LET’S GOOOO! List reset. Time to grind.");
  }

  if (content.includes("bot please die")) {
    isActive = false;
    grindList = {};
    msg.reply("Okay. I’m out. Dead. Don't wake me up.");
  }
});

bot.login(process.env.TOKEN);


	4.	Scroll down and tap Commit new file.

B. Create package.json
	1.	Tap Add file → Create new file.
	2.	Name it: package.json
	3.	Paste the following content:

{
  "name": "summertide-tracker",
  "version": "1.0.0",
  "description": "Guild grind bot with attitude",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "discord.js": "^14.13.0",
    "dotenv": "^16.3.1"
  }
}


	4.	Commit the file.

C. Create .env.example
	1.	Tap Add file → Create new file.
	2.	Name it: .env.example
	3.	Paste the following content:

TOKEN=your-bot-token-here
CLIENT_ID=1359430975385505813
GUILD_ID=your-server-id-here


	4.	Commit the file.
Note: Later on Railway, you’ll set these variables. For now, this file just shows the format.

D. Create README.md
	1.	Tap Add file → Create new file.
	2.	Name it: README.md
	3.	Paste the following content:

# SummertideTracker Bot

**Commands:**
- `/setlevel [level]` → Set or update your grind level.
- `/setlevel` → Remove yourself from the list.
- `/list` → Show who’s grinding.

**Deputy Phrases:**
- "let’s go guys" → Resets the grind list and reactivates the bot.
- "bot please die" → Silences and deactivates the bot.

Only deputies can use those phrases:
- arxangelos
- Hajduk "The Jinxed"
- x4jduk
- belvitaftw
- GOAT
- THE GREATEST OF ALL TIMES

## How to Deploy on Railway

1. Upload this project to a GitHub repo.
2. Go to [Railway](https://railway.app) and create a project.
3. Link the repo and set these environment variables:
   - `TOKEN`: Your bot token
   - `CLIENT_ID`: 1359430975385505813
   - `GUILD_ID`: Your Discord Server ID
4. Click Deploy.


	4.	Commit the file.

⸻

Option 2: Upload the ZIP from Files (if you have a file manager that supports GitHub upload)

If you have an app like Working Copy (a Git client for iOS), you can:
	1.	Import the downloaded ZIP file.
	2.	Unzip it within the app.
	3.	Commit the files to a new repository.

⸻

Next: Deploying on Railway

Once your GitHub repository is ready, follow these steps on Railway:
	1.	Sign Up and Connect GitHub:
	•	Open Safari and go to Railway.app.
	•	Sign up or log in using your GitHub account.
	•	Tap New Project and then choose Deploy from GitHub Repo.
	2.	Select Your Repository:
	•	Find the repository you just created (e.g., summertide-tracker) and select it.
	3.	Set Environment Variables:
	•	After the repo is linked, go to the Variables tab.
	•	Add the following variables:
	•	TOKEN: Your bot’s token (make sure you’ve reset it in Discord Developer Portal if needed)
	•	CLIENT_ID: 1359430975385505813
	•	GUILD_ID: Your Discord server ID (enable Developer Mode in Discord to copy the server ID)
	4.	Deploy Your Project:
	•	Go back to the Deployments tab and click Deploy Now.
	•	Wait a few moments until Railway builds and deploys your bot. You should see logs indicating that the bot is online.
	5.	Invite Your Bot:
	•	Use this URL to invite your bot (replace CLIENT_ID if needed, but it should be correct):

https://discord.com/oauth2/authorize?client_id=1359430975385505813&scope=bot+applications.commands&permissions=1126864127453248


	•	Open the link in Safari, choose your server, and click Authorize.

⸻

Let me know if you get stuck on any step or need more details on a particular part. I’m here to help!