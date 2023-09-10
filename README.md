//# Discord_GPT
//js


const openApiKey = process.env['OpenAPIKey'];
const discordKey = process.env['DiscordKey'];
const openAIOrg = process.env['OpenAIOrg'];

const { Client, GatewayIntentBits, MessageAttachment } = require('discord.js');
const { Configuration, OpenAIApi } = require('openai');

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
  ],
});

const configuration = new Configuration({
  apiKey: openApiKey,
  organization: openAIOrg,
});

const openai = new OpenAIApi(configuration);

client.on('messageCreate', async (message) => {
  try {
    if (message.author.bot) return;

    const response = await openai.createCompletion({
      model: 'text-davinci-003',
      prompt: message.content,
      temperature: 0.3,
      max_tokens: 1000, // Increase the max_tokens value for longer responses
      top_p: 1.0,
      frequency_penalty: 0.5,
      presence_penalty: 0.0,
      stop: ['You:'],
    });

    console.log(message.content);
    const output = response.data.choices[0].text;

    if (output.length <= 2000) {
      message.reply(output);
    } else {
      const attachment = new MessageAttachment(Buffer.from(output), 'response.txt');
      message.reply('The response exceeds the character limit. Here is the full response:', attachment);
    }
  } catch (error) {
    console.log(error);
  }
});

client.login(discordKey);


