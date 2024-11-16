import discord
from discord.ext import commands, tasks
from discord import ui
import json
import os
import random
import math
from keep_alive import keep_alive
from discord import app_commands
import requests
import asyncio
from datetime import datetime, timezone
from dateutil.relativedelta import relativedelta
from dotenv import load_dotenv
import validators

load_dotenv()

max_content_length = 200

TOKEN = os.getenv('TOKEN')
intents = discord.Intents.default()
intents.presences = True  # Enable tracking presences
intents.members = True  # Enable member access
intents.message_content = True
bot = commands.Bot(command_prefix='Skibidi ', intents=intents)
data_file = 'economy.json'
currency_symbol = 'Robux'
prefixx = 'Skibidi'
tracking = {}
user_id = 1294151366297780248
user_id2 = 1250450550688845926
CHANNEL_ID = 1256477082133987329
def get_data():
    if os.path.exists(data_file):
        with open(data_file, 'r') as f:
            return json.load(f)
    return {}

def save_data(data):
    with open(data_file, 'w') as f:
        json.dump(data, f)

@bot.event
async def on_ready():
    
    print(f'Logged in as {bot.user}')

    
    try:
        synced = await bot.tree.sync()
        print(f"Synced {len(synced)} slash commands.")
    except Exception as e:
        print(f"Failed to sync commands: {e}")

    
    guild_count = len(bot.guilds)
    print(f'The bot is in {guild_count} server(s):')

    
    for guild in bot.guilds:
                
        guild_name = guild.name
        guild_id = guild.id
        member_count = guild.member_count

    channel = bot.get_channel(CHANNEL_ID)
    if channel:
        embed = discord.Embed(
            title="Status Update",
            description="✅ RECONNECTED! The bot is now online.",
            color=discord.Color.green()
        )
        await channel.send(embed=embed)

@bot.event
async def on_disconnect():
    
    channel = bot.get_channel(CHANNEL_ID)
    if channel:
        embed = discord.Embed(                                                                                                                                                                title="Status Update",
            description="❌ BOT IS DISCONNECTED!",
            color=discord.Color.red()
        )
        await channel.send(embed=embed)

@bot.event
async def on_resumed():
    
    channel = bot.get_channel(CHANNEL_ID)
    if channel:
        embed = discord.Embed(
            title="Status Update",
            description="🔄 RECONNECTING...",
            color=discord.Color.from_rgb(169, 169, 169)  # Gray color
        )
        await channel.send(embed=embed)


        # Print details in a cool format
        print(f'---')
        print(f'Guild Name: {guild_name} (ID: {guild_id})')
        print(f'Member Count: {member_count}')
        print(f'Owner: {guild.owner} (ID: {guild.owner_id})')
        print(f'Features: {", ".join(guild.features) if guild.features else "None"}')
        print(f'---')

    new_name = "Skibidi Bot"
    try:
        await bot.user.edit(username=new_name)
        print(f"Bot name changed to {new_name}")
    except discord.HTTPException as e:
        print(f"Failed to change bot name: {e}")


    print('Bot is ready')
    
    @bot.command()
async def track(ctx, username: str, mode: str = "Discord"):
    
    valid_modes = ["Discord", "Activity"]                                             
    if mode not in valid_modes:
        await ctx.send(f"Invalid mode. Choose from: {', '.join(valid_modes)}.")
        return

    member = discord.utils.get(ctx.guild.members, name=username)

    if member is None:
        await ctx.send(f"Could not find a user with the username `{username}`.")
        return

    
    tracking[member.id] = {"user": member, "author": ctx.author, "mode": mode}
    await ctx.send(f"Tracking `{username}` on {mode}. You will be notified in DMs about their status.")
    track_status.start(member)
    
    @tasks.loop(seconds=120)  
async def track_status(member):
    target = tracking[member.id]["user"]
    author = tracking[member.id]["author"]
    mode = tracking[member.id]["mode"]
    dm_channel = await author.create_dm()

    embed = discord.Embed(title="**Tracking**", color=discord.Color.blue())
    embed.add_field(name="**Username**", value=target.name, inline=True)

    if target.status == discord.Status.offline:
        status = "Offline"
        embed.color = discord.Color.red()
    elif target.status == discord.Status.online:
        status = "Online"
        embed.color = discord.Color.green()
    else:
        status = str(target.status).capitalize()

    embed.add_field(name="**Status**", value=status, inline=True)
    embed.add_field(name="**Tracking Mode**", value=mode, inline=True)
    embed.set_thumbnail(url=target.avatar.url)

    await dm_channel.send(embed=embed)

@bot.command()
async def stop_tracking(ctx, username: str):
    member = discord.utils.get(ctx.guild.members, name=username)

    if member is None and username not in tracking:
        await ctx.send(f"Could not find an active tracking session for `{username}`.")
        return

    if member is not None and member.id in tracking:
        del tracking[member.id]
    elif username in tracking:
        del tracking[username]

    await ctx.send(f"Stopped tracking `{username}`.")
    
    track_status.stop()
    
    @bot.tree.command(description="Track a user on Discord.")
async def track_slash(interaction: discord.Interaction, username: str, mode: str = "Discord"):
    
    valid_modes = ["Discord", "Activity"]  # Add more modes as needed

    if mode not in valid_modes:
        await interaction.response.send_message(f"Invalid mode. Choose from: {', '.join(valid_modes)}.", ephemeral=Tru>
        return

    member = discord.utils.get(interaction.guild.members, name=username)

    if member is None:
        await interaction.response.send_message(f"Could not find a user with the username `{username}`.", ephemeral=Tr>
        return

    
    tracking[member.id] = {"user": member, "author": interaction.user, "mode": mode}
    await interaction.response.send_message(f"Tracking `{username}` on {mode}. You will be notified in DMs about their>
    track_status.start(member)


@bot.tree.command(description="Stop tracking a user.")
async def stop_tracking_slash(interaction: discord.Interaction, username: str):
    member = discord.utils.get(interaction.guild.members, name=username)

    if member is None and username not in tracking:
        await interaction.response.send_message(f"Could not find an active tracking session for `{username}`.", epheme>
        return

    if member is not None and member.id in tracking:
        del tracking[member.id]
    elif username in tracking:
        del tracking[username]

    await interaction.response.send_message(f"Stopped tracking `{username}`.")
    
    track_status.stop()

class MyBot(commands.Bot):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.active_searches = {}

    async def setup_hook(self):
        await self.tree.sync()
      
        
trivia_questions = [
    {"question": "What is the capital city of Japan?", "answer": "Tokyo"},
    {"question": "In which year did the Titanic sink?", "answer": "1912"},
    {"question": "What is the largest planet in our solar system?", "answer": "Jupiter"},
    {"question": "Who wrote the play 'Romeo and Juliet'?", "answer": "William Shakespeare"},
    {"question": "What is the smallest country in the world?", "answer": "Vatican City"},
    {"question": "What element does 'O' represent on the periodic table?", "answer": "Oxygen"},
    {"question": "Which famous scientist developed the theory of relativity?", "answer": "Albert Einstein"},
    {"question": "In which continent is the Sahara Desert located?", "answer": "Africa"},
    {"question": "What is the hardest natural substance on Earth?", "answer": "Diamond"},
    {"question": "Who painted the Mona Lisa?", "answer": "Leonardo da Vinci"},
    {"question": "What is the main ingredient in guacamole?", "answer": "Avocado"},
    {"question": "What is the largest ocean on Earth?", "answer": "Pacific Ocean"},
    {"question": "In which year did World War II end?", "answer": "1945"},
    {"question": "What is the currency of the United Kingdom?", "answer": "Pound Sterling"},
    {"question": "Who was the first man to walk on the moon?", "answer": "Neil Armstrong"},
    {"question": "What is the chemical symbol for gold?", "answer": "Au"},
    {"question": "What is the longest river in the world?", "answer": "Nile River"},
    {"question": "What animal is known as the King of the Jungle?", "answer": "Lion"},
    {"question": "Which planet is known as the Red Planet?", "answer": "Mars"},
    {"question": "What is the name of the first artificial satellite sent into space?", "answer": "Sputnik 1"},
    {"question": "What is the largest land animal?", "answer": "African Elephant"},
    {"question": "Who wrote the novel '1984'?", "answer": "George Orwell"},
    {"question": "What is the hardest natural material?", "answer": "Diamond"},
    {"question": "In which country would you find the Great Pyramid of Giza?", "answer": "Egypt"},
    {"question": "What is the capital of Australia?", "answer": "Canberra"},
    {"question": "Which ocean is the Bermuda Triangle located in?", "answer": "Atlantic Ocean"},
    {"question": "What is the smallest planet in our solar system?", "answer": "Mercury"},
    {"question": "Who painted the ceiling of the Sistine Chapel?", "answer": "Michelangelo"},
    {"question": "What is the largest mammal in the world?", "answer": "Blue Whale"},
    {"question": "What is the main language spoken in Brazil?", "answer": "Portuguese"},
    {"question": "Which element has the atomic number 1?", "answer": "Hydrogen"},
    {"question": "Who discovered penicillin?", "answer": "Alexander Fleming"},
    {"question": "What is the most widely spoken language in the world?", "answer": "Mandarin Chinese"},
    {"question": "In what year did the Berlin Wall fall?", "answer": "1989"},
    {"question": "What is the tallest mountain in the world?", "answer": "Mount Everest"},
    {"question": "What is the capital of Canada?", "answer": "Ottawa"},
    {"question": "Which planet is known for its rings?", "answer": "Saturn"},
    {"question": "Who is the author of the 'Harry Potter' series?", "answer": "J.K. Rowling"},
    {"question": "What is the largest desert in the world?", "answer": "Antarctic Desert"},
    {"question": "What gas do plants absorb from the atmosphere?", "answer": "Carbon Dioxide"},
    {"question": "What is the square root of 64?", "answer": "8"},
    {"question": "Who was the first female Prime Minister of the United Kingdom?", "answer": "Margaret Thatcher"},
    {"question": "What is the most common blood type?", "answer": "O positive"},
    {"question": "Which country is known as the Land of the Rising Sun?", "answer": "Japan"},
    {"question": "What is the name of the longest river in South America?", "answer": "Amazon River"},
    {"question": "What is the capital city of France?", "answer": "Paris"},
    {"question": "What type of animal is a Komodo dragon?", "answer": "Lizard"},
    {"question": "Which planet is closest to the sun?", "answer": "Mercury"},
    {"question": "What is the largest country in the world by land area?", "answer": "Russia"},
    {"question": "In which organ of the human body would you find the cerebellum?", "answer": "Brain"},
    {"question": "Who was the first president of the United States?", "answer": "George Washington"},
    {"question": "What is the most popular sport in the world?", "answer": "Soccer (Football)"},
    {"question": "What is the chemical formula for water?", "answer": "H2O"},
    {"question": "What is the main ingredient in traditional hummus?", "answer": "Chickpeas"},
    {"question": "In which city would you find the Statue of Liberty?", "answer": "New York City"},
    {"question": "What is the capital city of Italy?", "answer": "Rome"},
    {"question": "Which planet is known as the 'Giant Planet'?", "answer": "Jupiter"},
    {"question": "Who invented the telephone?", "answer": "Alexander Graham Bell"},
    {"question": "What is the main ingredient in beer?", "answer": "Barley"},
    {"question": "What is the most spoken language in the United States?", "answer": "English"},
    {"question": "Which animal is known to have the longest lifespan?", "answer": "Bowhead Whale"},
    {"question": "What year did the first manned space flight occur?", "answer": "1961"},
    {"question": "What is the largest island in the world?", "answer": "Greenland"},
    {"question": "Which fruit has its seeds on the outside?", "answer": "Strawberry"},
    {"question": "What is the most common element in the universe?", "answer": "Hydrogen"},
    {"question": "Who wrote 'The Great Gatsby'?", "answer": "F. Scott Fitzgerald"},
    {"question": "What is the main ingredient in traditional Japanese sushi?", "answer": "Rice"},
    {"question": "Which planet is known for having the most moons?", "answer": "Saturn"},
    {"question": "What is the only continent without a desert?", "answer": "Europe"},
    {"question": "What is the name of the first man-made object to orbit the Earth?", "answer": "Sputnik 1"},
    {"question": "In which year did the first man land on the moon?", "answer": "1969"},
    {"question": "What is the main ingredient in tofu?", "answer": "Soybeans"},
    {"question": "What is the largest bone in the human body?", "answer": "Femur"},
    {"question": "Who was the first woman to win a Nobel Prize?", "answer": "Marie Curie"},
    {"question": "What is the longest running Broadway show?", "answer": "The Phantom of the Opera"},
    {"question": "What is the most widely used programming language?", "answer": "JavaScript"},
    {"question": "Who painted 'The Starry Night'?", "answer": "Vincent van Gogh"},
    {"question": "Which ocean is the largest?", "answer": "Pacific Ocean"},
    {"question": "What does DNA stand for?", "answer": "Deoxyribonucleic Acid"},
    {"question": "What is the main ingredient in a traditional pizza Margherita?", "answer": "Tomato"},
    {"question": "What is the largest continent on Earth?", "answer": "Asia"},
    {"question": "What is the capital of Germany?", "answer": "Berlin"},
    {"question": "Which animal is known for its ability to change color?", "answer": "Chameleon"},
    {"question": "What is the fastest land animal?", "answer": "Cheetah"},
    {"question": "What is the name of the fictional detective created by Arthur Conan Doyle?", "answer": "Sherlock Holmes"},
    {"question": "Which planet has the highest mountain in the solar system?", "answer": "Mars (Olympus Mons)"},
    {"question": "What is the primary function of red blood cells?", "answer": "Carrying oxygen"},
    {"question": "Which element is essential for the production of thyroid hormones?", "answer": "Iodine"},
    {"question": "What is the largest type of penguin?", "answer": "Emperor Penguin"},
    {"question": "Who is known as the 'Father of Modern Physics'?", "answer": "Albert Einstein"},
    {"question": "What is the capital of Egypt?", "answer": "Cairo"},
    {"question": "What is the official currency of Japan?", "answer": "Yen"},
    {"question": "Which natural disaster is measured with a Richter scale?", "answer": "Earthquake"},
    {"question": "What is the primary language spoken in Argentina?", "answer": "Spanish"},
    {"question": "What is the most widely used social media platform?", "answer": "Facebook"},
    {"question": "What is the capital city of France?", "answer": "Paris"},
    
  {"question": "What is the largest planet in our solar system?", "answer": "Jupiter"},
  {"question": "Who wrote 'Romeo and Juliet'?", "answer": "William Shakespeare"},
  {"question": "What is the main ingredient in guacamole?", "answer": "Avocado"},
  {"question": "What is the boiling point of water in Celsius?", "answer": "100°C"},
  {"question": "Who painted the Mona Lisa?", "answer": "Leonardo da Vinci"},
  {"question": "What is the currency of Japan?", "answer": "Yen"},
  {"question": "In what country would you find the Eiffel Tower?", "answer": "France"},
  {"question": "What is the tallest mountain in the world?", "answer": "Mount Everest"},
  {"question": "Who is the author of the Harry Potter series?", "answer": "J.K. Rowling"},
  {"question": "What is the smallest planet in our solar system?", "answer": "Mercury"},
  {"question": "In which country is the Great Barrier Reef located?", "answer": "Australia"},
  {"question": "What is the primary language spoken in Brazil?", "answer": "Portuguese"},
  {"question": "What is the chemical symbol for gold?", "answer": "Au"},
  {"question": "Who discovered penicillin?", "answer": "Alexander Fleming"},
  {"question": "What is the longest river in the world?", "answer": "Nile River"},
  {"question": "What is the capital city of Canada?", "answer": "Ottawa"},
  {"question": "Which element has the atomic number 1?", "answer": "Hydrogen"},
  {"question": "What is the main language spoken in Italy?", "answer": "Italian"},
  {"question": "Who was the first President of the United States?", "answer": "George Washington"},
  {"question": "What is the largest ocean on Earth?", "answer": "Pacific Ocean"},
  {"question": "What is the currency of the United Kingdom?", "answer": "Pound Sterling"},
  {"question": "Which animal is known as the 'King of the Jungle'?", "answer": "Lion"},
  {"question": "What is the primary language spoken in Argentina?", "answer": "Spanish"},
  {"question": "Which country is home to the pyramids of Giza?", "answer": "Egypt"},
  {"question": "What is the longest bone in the human body?", "answer": "Femur"},
  {"question": "What is the capital city of Australia?", "answer": "Canberra"},
  {"question": "What is the chemical symbol for water?", "answer": "H2O"},
  {"question": "Who developed the theory of relativity?", "answer": "Albert Einstein"},
  {"question": "Which planet is known as the Red Planet?", "answer": "Mars"},
  {"question": "Who invented the telephone?", "answer": "Alexander Graham Bell"},
  {"question": "What is the largest continent?", "answer": "Asia"},
  {"question": "Which animal is the fastest on land?", "answer": "Cheetah"},
  {"question": "What is the main ingredient in sushi?", "answer": "Rice"},
  {"question": "Who painted the Sistine Chapel ceiling?", "answer": "Michelangelo"},
  {"question": "What is the currency of the United States?", "answer": "Dollar"},
  {"question": "What is the tallest building in the world?", "answer": "Burj Khalifa"},
  {"question": "What is the national sport of Canada?", "answer": "Ice Hockey"},
  {"question": "What is the largest desert in the world?", "answer": "Sahara Desert"},
  {"question": "Who was the first man to step on the moon?", "answer": "Neil Armstrong"},
  {"question": "What is the capital city of Germany?", "answer": "Berlin"},
  {"question": "What is the primary ingredient in hummus?", "answer": "Chickpeas"},
  {"question": "What is the currency of China?", "answer": "Yuan"},
  {"question": "What is the official language of Mexico?", "answer": "Spanish"},
  {"question": "Which country has the most islands?", "answer": "Sweden"},
  {"question": "What is the national animal of Australia?", "answer": "Kangaroo"},
  {"question": "What is the largest island in the world?", "answer": "Greenland"},
  {"question": "What is the main ingredient in a traditional Japanese dish called tempura?", "answer": "Seafood or vegetables"},
  {"question": "What is the capital city of Spain?", "answer": "Madrid"},
  {"question": "What is the currency of Russia?", "answer": "Ruble"},
  {"question": "What is the fastest bird in the world?", "answer": "Peregrine Falcon"},
  {"question": "What is the most widely spoken language in the world?", "answer": "Mandarin Chinese"},
  {"question": "What is the primary language spoken in Germany?", "answer": "German"},
  {"question": "What is the smallest country in the world?", "answer": "Vatican City"},
  {"question": "What is the capital city of Italy?", "answer": "Rome"},
  {"question": "Who was the first woman to fly solo across the Atlantic Ocean?", "answer": "Amelia Earhart"},
  {"question": "What is the hardest natural substance on Earth?", "answer": "Diamond"},
  {"question": "What is the capital city of India?", "answer": "New Delhi"},
  {"question": "What is the chemical symbol for oxygen?", "answer": "O"},
  {"question": "What is the national flower of Japan?", "answer": "Cherry Blossom"},
  {"question": "What is the longest wall in the world?", "answer": "Great Wall of China"},
  {"question": "What is the capital city of Brazil?", "answer": "Brasília"},
  {"question": "What is the main ingredient in a traditional French croissant?", "answer": "Butter"},
  {"question": "What is the largest country by land area?", "answer": "Russia"},
  {"question": "What is the official language of Egypt?", "answer": "Arabic"},
  {"question": "What is the name of the world's largest coral reef system?", "answer": "Great Barrier Reef"},
  {"question": "What is the capital city of Russia?", "answer": "Moscow"},
  {"question": "What is the national animal of India?", "answer": "Bengal Tiger"},
  {"question": "What is the chemical symbol for sodium?", "answer": "Na"},
  {"question": "Who was the first President of France?", "answer": "Louis-Napoléon Bonaparte"},
  {"question": "What is the currency of South Korea?", "answer": "Won"},
  {"question": "Who wrote 'Pride and Prejudice'?", "answer": "Jane Austen"},
  {"question": "What is the capital city of Mexico?", "answer": "Mexico City"},
  {"question": "Which ocean lies to the east of Africa?", "answer": "Indian Ocean"},
  {"question": "What is the capital city of South Korea?", "answer": "Seoul"},
  {"question": "What is the most famous statue in Rio de Janeiro?", "answer": "Christ the Redeemer"},
  {"question": "What is the national dish of Spain?", "answer": "Paella"},
  {"question": "What is the capital city of Egypt?", "answer": "Cairo"},
  {"question": "What is the largest river in South America?", "answer": "Amazon River"},
  {"question": "What is the official language of Brazil?", "answer": "Portuguese"},
  {"question": "Who invented the light bulb?", "answer": "Thomas Edison"},
  {"question": "What is the capital city of Japan?", "answer": "Tokyo"},
  {"question": "What is the currency of Canada?", "answer": "Canadian Dollar"},
  {"question": "What is the deepest part of the world's oceans?", "answer": "Mariana Trench"},
  {"question": "What is the most spoken language in the United States?", "answer": "English"},
  {"question": "Who was the first human in space?", "answer": "Yuri Gagarin"},
  {"question": "What is the currency of Mexico?", "answer": "Peso"},
  {"question": "What is the capital city of Argentina?", "answer": "Buenos Aires"},
  {"question": "What is the primary language spoken in Egypt?", "answer": "Arabic"},
  {"question": "Which animal is the largest mammal on Earth?", "answer": "Blue Whale"},
  {"question": "What is the largest land animal?", "answer": "African Elephant"},
  {"question": "What is the tallest waterfall in the world?", "answer": "Angel Falls"},
  {"question": "What is the chemical symbol for carbon?", "answer": "C"},
  {"question": "What is the smallest ocean on Earth?", "answer": "Arctic Ocean"},
  {"question": "What is the capital city of the United States?", "answer": "Washington, D.C."},
  {"question": "What is the largest city in the world by population?", "answer": "Tokyo"},
  {"question": "What is the main language spoken in Egypt?", "answer": "Arabic"},
  {"question": "Who's Koofy?", "answer": "Kelogish"},
  {"question": "What is the main language spoken in Egypt?", "answer": "Arabic"},
  {"question": "What is the most common blood type in humans?", "answer": "O+"},
  {"question": "What is the longest river in the United States?", "answer": "Missouri River"},
  {"question": "Which planet is closest to the Sun?", "answer": "Mercury"},
  {"question": "What is the capital city of Sweden?", "answer": "Stockholm"},
  {"question": "Who was the first woman to win a Nobel Prize?", "answer": "Marie Curie"},
  {"question": "What is the main ingredient in the Italian dish risotto?", "answer": "Rice"},
  {"question": "What is the capital city of China?", "answer": "Beijing"},
  {"question": "What is the smallest bone in the human body?", "answer": "Stapes (in the ear)"},
  {"question": "Which country is known as the Land of the Rising Sun?", "answer": "Japan"},
  {"question": "What is the currency of Italy?", "answer": "Euro"},
  {"question": "What is the largest lake in Africa?", "answer": "Lake Victoria"},
  {"question": "What is the fastest aquatic animal?", "answer": "Black Marlin"},
  {"question": "What is the capital city of South Africa?", "answer": "Pretoria (administrative), Cape Town (legislative), Bloemfontein (judicial)"},
  {"question": "Which country is known as the Land of the Midnight Sun?", "answer": "Norway"},
  {"question": "What is the national flower of the United States?", "answer": "Rose"},
  {"question": "What is the currency of Switzerland?", "answer": "Swiss Franc"},
  {"question": "Who is known as the Father of the Nation in India?", "answer": "Mahatma Gandhi"},
  {"question": "What is the capital city of Thailand?", "answer": "Bangkok"},
  {"question": "What is the capital city of Kenya?", "answer": "Nairobi"},
  {"question": "Which planet is known for its rings?", "answer": "Saturn"},
  {"question": "What is the tallest tree species in the world?", "answer": "Coast Redwood"},
  {"question": "Who was the first female prime minister of the United Kingdom?", "answer": "Margaret Thatcher"},
  {"question": "Which country has the most volcanoes?", "answer": "Indonesia"},
  {"question": "What is the largest country in Africa?", "answer": "Algeria"},
  {"question": "What is the main language spoken in Spain?", "answer": "Spanish"},
  {"question": "What is the currency of India?", "answer": "Indian Rupee"},
  {"question": "Which animal is known for its black and white stripes?", "answer": "Zebra"},
  {"question": "What is the capital city of Portugal?", "answer": "Lisbon"},
  {"question": "What is the primary language spoken in Colombia?", "answer": "Spanish"},
  {"question": "What is the chemical symbol for silver?", "answer": "Ag"},
  {"question": "Which animal is known for its ability to change color?", "answer": "Chameleon"},
  {"question": "What is the capital city of Finland?", "answer": "Helsinki"},
  {"question": "Which country is the largest producer of coffee?", "answer": "Brazil"},
  {"question": "What is the currency of Australia?", "answer": "Australian Dollar"},
  {"question": "What is the most famous river in Egypt?", "answer": "Nile River"},
  {"question": "Which mountain range is the longest in the world?", "answer": "Andes Mountains"},
  {"question": "What is the currency of France?", "answer": "Euro"},
  {"question": "What is the largest country in South America?", "answer": "Brazil"},
  {"question": "What is the currency of the European Union?", "answer": "Euro"},
  {"question": "Which city is known as the Eternal City?", "answer": "Rome"},
  {"question": "What is the main ingredient in a traditional British dish, shepherd's pie?", "answer": "Ground lamb"},
  {"question": "What is the tallest waterfall in North America?", "answer": "Yosemite Falls"},
  {"question": "What is the national animal of Canada?", "answer": "Beaver"},
  {"question": "What is the capital city of New Zealand?", "answer": "Wellington"},
  {"question": "Who invented the first practical telephone?", "answer": "Alexander Graham Bell"},
  {"question": "What is the national flower of India?", "answer": "Lotus"},
  {"question": "What is the longest mountain range in the world?", "answer": "Andes Mountains"},
  {"question": "Which is the most populous country in Africa?", "answer": "Nigeria"},
  {"question": "What is the national bird of the United States?", "answer": "Bald Eagle"},
  {"question": "What is the name of the Greek goddess of wisdom?", "answer": "Athena"},
  {"question": "What is the national tree of the United States?", "answer": "Oak"},
  {"question": "What is the longest river in Europe?", "answer": "Volga River"},
  {"question": "Who is known as the King of Pop?", "answer": "Michael Jackson"},
  {"question": "What is the chemical symbol for helium?", "answer": "He"},
  {"question": "What is the capital city of the United Arab Emirates?", "answer": "Abu Dhabi"},
  {"question": "What is the currency of Turkey?", "answer": "Turkish Lira"},
  {"question": "What is the most spoken language in the world?", "answer": "Mandarin Chinese"},
  {"question": "Who is the famous inventor of the airplane?", "answer": "Wright Brothers"},
  {"question": "What is the national animal of China?", "answer": "Giant Panda"},
  {"question": "What is the official language of Belgium?", "answer": "Dutch, French, German"},
  {"question": "What is the currency of Argentina?", "answer": "Argentine Peso"},
  {"question": "What is the capital city of Malaysia?", "answer": "Kuala Lumpur"},
  {"question": "What is the main ingredient in French fries?", "answer": "Potatoes"},
  {"question": "What is the currency of South Africa?", "answer": "Rand"},
  {"question": "What is the capital city of Hungary?", "answer": "Budapest"},
  {"question": "What is the official language of Switzerland?", "answer": "German, French, Italian, Romansh"},
  {"question": "What is the longest animal migration?", "answer": "Gray Whale"},
  {"question": "Which country is home to the Eiffel Tower?", "answer": "France"},
  {"question": "What is the name of the first artificial satellite?", "answer": "Sputnik 1"},
]

@bot.command()
async def trivia(ctx):
    question = random.choice(trivia_questions)
    await ctx.send(question["question"])
                                                                                                          def check(msg):
        return msg.author == ctx.author and msg.channel == ctx.channel

    try:
        msg = await bot.wait_for('message', check=check, timeout=15)  
        if msg.content.lower() == question["answer"].lower():
            await ctx.send("Correct! 🎉")
        else:
            await ctx.send(f"Wrong! The correct answer was: {question['answer']}")                        except asyncio.TimeoutError:
        await ctx.send("Time's up! ⏰")

@bot.command()
async def poll(ctx, *, question: str):
    poll_message = await ctx.send(f"**Poll:** {question}")
    await poll_message.add_reaction("👍")                                                                 await poll_message.add_reaction("👎")

@bot.command()
async def remindme(ctx, time: int, *, message: str):
    await ctx.author.send(f"Reminder set for {time} seconds.")
    await asyncio.sleep(time)
    await ctx.author.send(f"⏰ Reminder: {message}")
    
@bot.command()
async def serverinfo(ctx):
    guild = ctx.guild
    embed = discord.Embed(title=f"{guild.name} Info", color=discord.Color.blue())
    embed.add_field(name="Server ID", value=guild.id, inline=False)
    embed.add_field(name="Owner", value=guild.owner.mention, inline=False)
    embed.add_field(name="Member Count", value=guild.member_count, inline=False)
    embed.add_field(name="Creation Date", value=guild.created_at.strftime("%Y-%m-%d"), inline=False)      embed.add_field(name="Region", value=guild.region, inline=False)

    await ctx.send(embed=embed)
    


autobypass_status = {}

bypass_times = []
suji = "<a:1_:1303382705836265503>"  
aji = "<a:arrow4:1303382833208885300>"  
clck = "<a:clock:1303389515691462666>"
key = "<:key:1303387491386130452>"
dance = "<a:RobloxDance:1255905273134977024>"
@bot.tree.command(name="autobypass", description="Toggle autobypass for a channel")
@commands.has_permissions(administrator=True)
async def autobypass(interaction: discord.Interaction, channel: discord.TextChannel, status: bool):
    
    autobypass_status[channel.id] = status
    status_text = "enabled" if status else "disabled"
    await interaction.response.send_message(f"Autobypass has been {status_text} for {channel.mention}.")

@bot.event
async def on_message(message):
    # Avoid processing messages from the bot itself
    if message.author == bot.user:
        return

    
    if message.channel.id in autobypass_status and autobypass_status[message.channel.id]:
        # Check if the message contains a link
        links = [word for word in message.content.split() if word.startswith("http")]
        for link in links:
            await process_link(message, link)

    await bot.process_commands(message)  

async def process_link(message, link):
    # Notify the user that the bypass is starting
    if bypass_times:
        average_time = sum(bypass_times) / len(bypass_times)
        estimated_wait_time = f"{int(average_time)} Seconds"
    else:
        estimated_wait_time = "Might take around 0-10 Seconds"

    if not link.startswith("https://gateway.platoboost.com"):
        return

    waiting_embed = discord.Embed(title="Bypass Status", color=discord.Color.dark_blue())
    waiting_embed.add_field(name="Status:", value="```BYPASSING...```", inline=False)
    waiting_embed.add_field(name="Estimated Wait Time:", value=f"```{estimated_wait_time}```", inline=False)

    wait_message = await message.channel.send(embed=waiting_embed)


    api_url = f"http://de01-2.uniplex.xyz:1575/api/executorbypass?url={link}"

    try:

        start_time = asyncio.get_event_loop().time()


        response = requests.get(api_url)
        response_data = response.json()


        end_time = asyncio.get_event_loop().time()
        time_taken = end_time - start_time 
        bypass_times.append(time_taken)  


        if response_data.get("status") == "success":
            embed_color = discord.Color.green()
            embed_title = f"{dance} Bypass Result"
        else:
            embed_color = discord.Color.red()
            embed_title = "Failed bruh"

        embed = discord.Embed(title=f"{dance} Bypass Result", color=embed_color)
        embed.add_field(name=f"**{key} Key:**", value=f"```{response_data.get('key', 'Error bruh')}```", inline=True)
        embed.add_field(name=f"**{key} Alt Key:**", value=f"{response_data.get('key', 'Error bruh')}", inline=True)
        embed.add_field(name=f"**{suji} Status:**", value=f"```{response_data.get('status', 'idk')}```", inline=True)
        embed.add_field(name=f"**{clck} Time Taken:**", value=f"```{response_data.get('time taken', 'idk')}```", inline=True)


        if link.startswith("https://gateway.platoboost.com"):
            embed.set_thumbnail(url="https://cdn.discordapp.com/attachments/1256248619313401978/1303374851288989776/Untitled30_20241105220528.png?ex=672b85ec&is=672a346c&hm=2dbb6afd80a0eef21d9c7ba7e9ce23e2be81407c27c57b606903c2ec5ef7021f&")  
        
        embed.set_footer(text="Skibidi Hub")
        await wait_message.delete()  
        await message.channel.send(embed=embed)  
    except requests.RequestException as e:
        await wait_message.delete()  
        await message.channel.send(f"An error occurred while making the request: {e}")
    except json.JSONDecodeError:
        await wait_message.delete()  
        await message.channel.send("Failed to decode the response from the server.")

bot.run(TOKEN)
