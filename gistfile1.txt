#!/bin/bash

# ChatGPT Command Line Utility
# Usage:
# chatgpt.sh "how do I show the last 5 commits in git?"
#
# To use without quotes, create an alias in your ~/.zshrc file as follows:
# alias chatgpt='noglob ~/scripts/chatgpt.sh'
#
# Now you can ask questions like this:
# chatgpt how do I show the last 5 commits in git?
#
# Set your OpenAI API key here, or as an environment variable
# OPENAI_API_KEY=

QUESTION="$*"

PROMPT="$QUESTION\n\nMake your answer as concise as possible.  If it includes code or commands, only respond with that information."

REQUEST='{"model":"gpt-4o", "messages":[{"role":"user", "content":"'$PROMPT'"}]}'

URL="https://api.openai.com/v1/chat/completions"

RESPONSE=$(curl -s -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $OPENAI_API_KEY" -d "$REQUEST" $URL)
CONTENT=$(echo $RESPONSE | jq '.choices[0].message.content')

# Remove backticks and quotes
SANITIZED=$(echo $CONTENT | sed 's/`//g' | sed 's/\"//g')

INPUT_TOKENS=$(echo $RESPONSE | jq '.usage.prompt_tokens')
OUTPUT_TOKENS=$(echo $RESPONSE | jq '.usage.completion_tokens')
echo -e $SANITIZED