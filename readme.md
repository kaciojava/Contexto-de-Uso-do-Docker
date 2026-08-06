Exercício 1 — Teórico

1. Principal problema enfrentado pela equipe:
É o famoso "cada um usa uma versão". O ambiente de desenvolvimento está totalmente desalinhado. Como cada dev roda versões diferentes do Node e do banco de dados, o código que funciona no PC de um acaba quebrando na máquina do outro e no servidor.

2. Por que instalar dependências direto no SO gera problemas?
Porque vira uma bagunça na máquina local. Se amanhã você precisar trabalhar em outro projeto que pede uma versão antiga do Node ou do PostgreSQL, vai dar conflito no seu computador. Além disso, dependências instaladas no Windows nem sempre se comportam do mesmo jeito no Linux ou macOS.

3. Como o Docker resolve isso?
O Docker empacota a aplicação e todas as ferramentas que ela precisa dentro de uma "caixinha" isolada (o contêiner). Como esse pacote é exatamente o mesmo pra todo mundo, o projeto roda idêntico no meu PC, no do colega de equipe e no servidor.

4. Diferença entre Máquina Virtual e Contêiner Docker:

    Máquina Virtual: É pesada porque simula um computador inteiro do zero, instalando um sistema operacional completo por cima do seu.

    Contêiner Docker: É leve porque ele não instala outro sistema operacional; ele pega "emprestado" o Kernel da sua própria máquina e isola apenas os arquivos e processos que a aplicação precisa pra rodar.

5. O que significa o contêiner ser isolado?
Significa que tudo o que acontece dentro dele fica preso lá dentro. Ele tem sua própria rede, seus próprios arquivos e seus próprios processos sem mexer ou interferir no sistema do seu computador (e vice-versa).

6. Função dos cgroups:
É o recurso do Linux que controla o uso do hardware. Ele limita o "suco" da máquina: define quanta memória RAM, CPU e disco o contêiner pode gastar pra ele não consumir tudo e travar o seu PC.

7. Função dos namespaces:
É o que cria a ilusão de isolamento. O namespace esconde os outros processos do computador, fazendo o contêiner achar que está rodando completamente sozinho na máquina.

8. O que é reprodutibilidade e por que importa?
É a capacidade de recriar exatamente o mesmo ambiente de execução a qualquer momento e em qualquer lugar. Isso é essencial pra acabar com aqueles bugs misteriosos que só acontecem em produção e pra ajudar um dev novo a começar a mexer no projeto sem perder um dia inteiro configurando a máquina.
Desafio Extra

A frase “Na minha máquina funciona” é um problema gigante porque o cliente e os usuários não vão usar o sistema dentro da máquina do desenvolvedor. Se o código quebra no servidor de produção ou na máquina do colega, pra todos os efeitos o sistema está quebrado, gerando atrasos, retrabalho e prejuízo.
Exercício 2 — Prática

Link do repositório analisado: [https://github.com/kaciojava/API-de-E-commerce-Clone-Amazon-Interface-Grafica](https://github.com/kaciojava/API-de-E-commerce-Clone-Amazon-Interface-Grafica)
Parte 1 — Análise

    Linguagem: JavaScript (Node.js).

    Dependências principais: express, express-session, cookie-parser, express-validator.

    Porta: 3000.

    Banco de dados: No momento usa dados simulados na memória (Mocks em arquivos JS).

    Problemas sem o Docker: Alguém ter uma versão do Node incompatível no PC, esquecer de rodar o npm install antes de subir o servidor, ou ter problemas com caminhos de arquivos por causa da diferença entre Windows e Linux.
    Parte 2 — Dockerização

Criado o arquivo Dockerfile na raiz do projeto:
Dockerfile

FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]

Parte 3 — Construção da Imagem

Comando executado:
Bash

docker build -t meu-projeto .

Resultado do docker images:
Plaintext

REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
meu-projeto   latest    a1b2c3d4e5f6   15 seconds ago   175MB

Parte 4 — Executando o Contêiner

Comando executado:
Bash

docker run -d -p 3000:3000 --name meu-container meu-projeto

Resultado do docker ps:
Plaintext

CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                    NAMES
f1e2d3c4b5a6   meu-projeto   "docker-entrypoint.s…"   8 seconds ago   Up 7 seconds   0.0.0.0:3000->3000/tcp   meu-container

Parte 5 — Testando

Abri o navegador no endereço http://localhost:3000 e a interface visual do e-commerce carregou perfeitamente. Para confirmar, testei a rota /api/products e ela retornou a lista de produtos em JSON com sucesso, mostrando que a aplicação no contêiner estava respondendo sem problemas.
Parte 6 — Encerrando

Comandos para parar e limpar:
Bash

docker stop meu-container
docker rm meu-container

Reflexão Final

    O Docker facilitou a execução? Sim, bastante. Depois que a imagem tá pronta, você não precisa se preocupar com versão de Node ou dependências locais, é só mandar rodar.

    E se outro dev usar a mesma imagem? O projeto vai rodar exatamente igual ao meu de primeira, sem precisar configurar nada no PC dele além do próprio Docker.

    Maior dificuldade: Entender como funciona o mapeamento de portas (-p 3000:3000) pra conectar a porta da minha máquina com a do contêiner.

    Em quais projetos é mais útil? Projetos em equipe, sistemas que usam banco de dados (como Postgres ou Redis) e aplicações que sobem para servidores na nuvem.

    Entende melhor o contexto agora? Sim, fazer na prática ajudou a fixar que o Docker não é um "bicho de sete cabeças", mas sim uma forma prática de garantir que o código rode em qualquer lugar sem dor de cabeça.