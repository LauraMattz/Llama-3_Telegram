
# Llama 3 Telegram Bot

Este projeto configura e executa um bot de Telegram utilizando o modelo Llama 3 da Meta.

## Requisitos

- Conta no Google Drive
- Token de acesso do bot do Telegram
- `transformers` da biblioteca Hugging Face
- `python-telegram-bot` biblioteca para criação de bots no Telegram

## Configuração

### Montar Google Drive

Antes de carregar o modelo, é necessário montar o Google Drive para acessar os arquivos necessários.

```python
from google.colab import drive

# Montar Google Drive
drive.mount('/content/drive', force_remount=True)
```

### Carregar o Modelo

Para carregar o modelo Llama 3 da Meta, use o seguinte código:

```python
from transformers import AutoTokenizer, AutoModelForCausalLM
from pathlib import Path

# Definindo o ID do modelo e o caminho no Google Drive
model_id = "meta-llama/Meta-Llama-3-8B"
drive_path = "/content/drive/MyDrive/Modelos_Meta_Llama"  # Ajuste o caminho conforme sua estrutura de diretórios
model_path = Path(drive_path) / model_id.replace("/", "_")  # Nome de pasta válido

# Carregar tokenizer e modelo
tokenizer = AutoTokenizer.from_pretrained(str(model_path))
model = AutoModelForCausalLM.from_pretrained(str(model_path))

# Configurando o tokenizer para usar o mesmo token para PAD e EOS se necessário
tokenizer.pad_token = tokenizer.eos_token
```

### Configurar o Bot do Telegram

Para configurar o bot do Telegram, é necessário definir o token de acesso e criar funções para responder às mensagens:

```python
import os
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters

# Token do bot do Telegram
TOKEN = 'SEU_TOKEN_AQUI'

# Função para iniciar o bot
async def start(update: Update, context):
    await update.message.reply_text("Olá! Eu sou um bot alimentado pelo modelo Llama 3. Pergunte-me qualquer coisa!")

# Função para responder às mensagens
async def answer(update: Update, context):
    pergunta = update.message.text
    print(f"Recebeu pergunta: {pergunta} do usuário: {update.effective_user.id}")
    resposta = text_generator(pergunta, max_length=100)
    print(f"Resposta gerada: {resposta}")
    await update.message.reply_text(resposta)
    print("Respondeu à mensagem do usuário")

# Função principal para iniciar o bot
def main():
    print("Iniciando o bot...")
    # Criar o objeto Application
    application = Application.builder().token(TOKEN).build()

    # Adicionar manipuladores
    application.add_handler(CommandHandler("start", start))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, answer))

    # Iniciar polling
    application.run_polling()
    print("O bot está em execução...")

# Ponto de entrada do script
if __name__ == '__main__':
    main()
```

## Execução

Para executar o bot, monte o Google Drive, carregue o modelo e execute a função `main()`.

Certifique-se de substituir `'SEU_TOKEN_AQUI'` pelo token do seu bot do Telegram. 

## Contribuição

Sinta-se à vontade para contribuir com melhorias e novas funcionalidades. Faça um fork do repositório, crie uma branch para sua funcionalidade, e envie um pull request com suas alterações.

## Licença

Este projeto está licenciado sob a licença MIT.
