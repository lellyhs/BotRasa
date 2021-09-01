---------------------------------- * DESENVOLVIMENTO DE FAQ - COVID EM RASA OPEN SOURCE * -------------------------------------

Desenvolver um chatbot especializado em COVID-19, que reproduza o FAQ da Fiocruz existente no 
endereço https://mooc.campusvirtual.fiocruz.br/rea/coronavirus/faq.html

PASSO A PASSO:

1) Instalação do Anaconda
2) Instalação do Visual C++

- Criação da pasta botrasa
- Acessar a pasta do projeto via prompt do Anaconda
- Criando ambiente virtual: conda create --name rasaenv
- Entrando no ambiente virtual: conda activate rasaenv

3) Instalando o rasa open source: (seguindo a orientação da documentação Rasa)
	- conda install ujson
	- conda install tensorflow
	- pip install rasa

- rasa init (para iniciar o projeto)

Nesse ponto, precisamos inserir as intenções(intent - arquivo nlu.yml), frases de treinamento(examples - arquivo nlu.yml), 
repostas(utter - arquivo domain.yml) e as conversas(stories e rules - stories.yml) a fim de construir efetivamente nosso Bot. 
Ao iniciar o rasa com o comando 'rasa init', foram criados os arquivos necessários e toda estrutura do bot, de forma que 
precisamos apenas alterá-los conforme as ações desejadas.

No arquivo 'config.yml' temos as configurações do bot, que vem inteiro comentado da incialização. Basta descomentar, alterando
o idioma de 'en' para 'pt'.

Após as alterações, usamos o comando 'rasa train' para treinar o bot com o fluxo conversacional criado. Lembrando que a cada 
alteração, se deve repetir esse comando.

Para testá-lo, usamos o comando 'rasa shell', qua abre um prompt de conversa com o bot. Para sair, basta digitar '/stop'.

Um arquivo 'index.html' foi criado para que se pudesse conversar com o bot de forma mais iterativa e visual, fazendo uma
integração web. 

No arquivo 'credentials.yml' temos que descomentar o seguinte bloco, alterando os nomes de user_message e bot_message, 
deixando com 'true' em session_persistence:

  socketio:
  user_message_evt: user_uttered
  bot_message_evt: bot_uttered
  session_persistence: true

Para executar, temos que usar o comando: 
  
  rasa run -m models --enable-api --cors "*"

Esse comando vai inicializar com servidor rasa baseado nos modelos treinados. Quando o servidor estiver rodando, basta 
abrir o arquivo 'index.hml' no navegador e iniciar a conversa com o bot.

4) Instalação do Ngrok (para integração com Telegram)

- Abrir o arquivo 'credentials.yml' e criar um novo bloco para o Telegram:

  telegram:
    access_token: "1933624058:AAFD21Pyc9Ijf3GXreSjVmEJ1yMRYOoHxrc"
    verify: "Compasso_bot"
    webhook_url: "https://6b2b-179-190-124-227.ngrok.io/webhooks/telegram/webhook"

O token e o nome do bot foram obtidos do BotFather do Telegram, através do comando /newbot. Uma vez escolhido o nome de acordo com
as orientações, a confirmação de criação informa o token que deve ser inserido no bloco acima, assim como o seu nome.

Ele pode ser acessado aqui: t.me/Compasso_bot

O endereço wehook_url foi obtido com o comando 'ngrok http 5005'. Devemos usar para o telegram o endereço com https fornecido. 
Como padrão, mantemos na sequencia '/webhooks/telegram/webhook'. Importante: deixar o console do ngrok aberto e rodando.

Para finalizar o processo e conversar com o bot no app, devemos treiná-lo e rodar os seguintes comandos, em consoles separados:

  rasa run --enable-api --cors "*"
  rasa run actions

OBS: o webhook_url deve ser alterado a cada vez que rodar o ngrok, pois uma vez interrompida a execução, o túnel de conexão se fecha.

5) Instalação do Docker para windows
6) Instalando e configurando o docker-compose: (seguindo a documentação do Rasa)

- Criar um arquivo docker-compose.yml dentro da pasta do projeto (botrasa)
- Configurar a execução:

  version: '3.0'
  services:
    rasa:
      image: rasa/rasa:2.8.3-full
      ports:
        - 5005:5005
      volumes:
        - ./:/app
      command:
        - run
    action-server:
      image: rasa/rasa-sdk:2.8.1
      volumes:
        - ./actions:/app/actions
      ports:
        - 5055:5055

- No arquivo endpoint.yml alterar o caminho para se conectar com o servidor Rasa:
action_endpoint:
  url: "http://action-server:5055/webhook"

- Executar o comando: docker-compose up

FINALIZANDO PARA TESTE DE AVALIAÇÃO:

O arquivo docker-compose.yml foi ajustado para iniciar automaticamente o treinamento em primeira ação
e então depois o action-server e o rasa-webchat onde vai rodar o comando que permite executar a integração web
e Telegram do bot, de forma que, para testar cada forma, basta seguir os seguintes passos:

- entrar no ambiente virtual na pasta onde está o projeto

Versão Web: 
- executar: docker-compose up
- abrir o arquivo index.hmtl
- iniciar a conversa

Telegram:
- abrir um novo console do prompt Anaconda e entrar no ambiente virtual
- executar 'ngrok http 5005' 
  (lembrando de manter rodando enquanto testa o bot telegram)
- copiar o link https fornecido
- colar no arquivo 'credentials.yml' no webhook_url como já feito anteriormente 
  (lembrando de manter /webhooks/telegram/webhook)
- salvar (ctrl+s)
- voltar ao console livre e executar: docker-compose up
- acessar o bot no app Telegram e inciar a conversa

--------------------------------------------------- * * * * * -------------------------------------------------------








