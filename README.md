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