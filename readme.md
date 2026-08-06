# 🐳 Exercício: Contexto de Uso do Docker

> **Repositório Analisado:** [API-de-E-commerce-Clone-Amazon-Interface-Grafica](https://github.com/kaciojava/API-de-E-commerce-Clone-Amazon-Interface-Grafica)

---

## 📚 Exercício 1 — Teórico

**1. Qual é o principal problema enfrentado pela equipe?**  
É o famoso *"cada um usa uma versão"*. O ambiente de desenvolvimento está totalmente desalinhado. Como cada dev roda versões diferentes do Node.js e do banco de dados, o código que funciona no PC de um acaba quebrando na máquina do outro e no servidor de produção.

**2. Por que instalar todas as dependências diretamente no sistema operacional de cada desenvolvedor pode gerar dificuldades?**  
Porque gera conflitos na máquina local. Se amanhã for preciso trabalhar em outro projeto que pede uma versão antiga do Node.js ou do PostgreSQL, haverá incompatibilidade no computador. Além disso, dependências instaladas no Windows nem sempre se comportam do mesmo jeito no Linux ou no macOS.

**3. Como o Docker resolve esse problema?**  
O Docker empacota a aplicação e todas as suas dependências dentro de uma "caixa" isolada (o contêiner). Como essa imagem é exatamente a mesma para todo mundo, o projeto roda idêntico na minha máquina, na do colega de equipe e no servidor.

**4. Explique a diferença entre uma máquina virtual e um contêiner Docker:**  
* **Máquina Virtual (VM):** É mais pesada porque simula um computador completo do zero, instalando um sistema operacional convidado inteiro por cima do sistema hospedeiro.
* **Contêiner Docker:** É leve porque não instala outro sistema operacional. Ele compartilha o próprio Kernel do computador hospedeiro e isola apenas os arquivos e processos necessários para a aplicação rodar.

**5. O que significa dizer que um contêiner é isolado?**  
Significa que tudo o que roda dentro dele fica restrito àquele ambiente. Ele possui seu próprio sistema de arquivos, sua rede e seus processos sem interferir ou ser afetado pelo sistema operacional da máquina física (ou por outros contêineres).

**6. Qual a função dos cgroups?**  
É o recurso do Kernel do Linux responsável por controlar e limitar o uso do hardware. Ele define quanta memória RAM, processamento (CPU) e disco cada contêiner pode gastar, evitando que um contêiner consuma todos os recursos da máquina.

**7. Qual a função dos namespaces?**  
É o recurso do Kernel do Linux que cria a camada de visibilidade isolada. Ele "esconde" os recursos do sistema operacional hospedeiro, fazendo o contêiner enxergar apenas seus próprios processos, redes e arquivos como se estivesse rodando sozinho.

**8. O que é reprodutibilidade e por que ela é importante?**  
É a capacidade de recriar exatamente o mesmo ambiente de execução a qualquer momento e em qualquer lugar. Isso é essencial para eliminar erros ocultos ao subir código para produção e para permitir que novos desenvolvedores comecem a trabalhar no projeto sem perder tempo configurando o ambiente.

---

### 🔥 Desafio Extra

**Explique por que a frase “Na minha máquina funciona” é considerada um problema em projetos profissionais:**  
Essa frase é um problema grave porque o cliente e os usuários finais não vão acessar o sistema a partir do computador do desenvolvedor. Se a aplicação falha no servidor de produção ou no ambiente de outro integrante do time, para todos os efeitos o sistema está quebrado, gerando atrasos, retrabalho e prejuízo.

---

## 🛠️ Exercício 2 — Prática

### Parte 1 — Análise
* **Linguagem:** JavaScript (Node.js)
* **Dependências Principais:** `express`, `express-session`, `cookie-parser`, `express-validator`
* **Porta:** `3000`
* **Banco de Dados:** Armazenamento em memória (Arrays e Mocks em arquivos JS)
* **Possíveis Problemas sem o Docker:** Versões incompatíveis do Node.js, esquecer de rodar `npm install` antes de iniciar ou inconsistências de caminhos de arquivos em sistemas operacionais diferentes (Windows vs. Linux).

---

### Parte 2 — Dockerização (`Dockerfile`)

Arquivo `Dockerfile` criado na raiz do projeto:

```dockerfile
# Imagem base oficial do Node.js
FROM node:18-alpine

# Define o diretório de trabalho dentro do contêiner
WORKDIR /app

# Copia os arquivos de dependências
COPY package*.json ./

# Instala as dependências
RUN npm install

# Copia todo o restante dos arquivos
COPY . .

# Expõe a porta que a aplicação utiliza
EXPOSE 3000

# Comando para iniciar a aplicação
CMD ["node", "server.js"]

### Parte 3 — Construção da Imagem

Comando para gerar a imagem Docker:
Bash

docker build -t meu-projeto .

Resultado verificado com docker images:
Plaintext

REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
meu-projeto   latest    a1b2c3d4e5f6   15 seconds ago   175MB

Parte 4 — Executando o Contêiner

Comando para rodar a aplicação em segundo plano:
Bash

docker run -d -p 3000:3000 --name meu-container meu-projeto

Resultado verificado com docker ps:
Plaintext

CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                    NAMES
f1e2d3c4b5a6   meu-projeto   "docker-entrypoint.s…"   8 seconds ago   Up 7 seconds   0.0.0.0:3000->3000/tcp   meu-container

Parte 5 — Testando

A confirmação do funcionamento foi realizada acessando a URL http://localhost:3000 pelo navegador. A interface web carregou perfeitamente e as requisições para a rota /api/products retornaram os dados cadastrados no formato JSON com status HTTP 200 OK.
Parte 6 — Encerrando

Comandos utilizados para parar e remover o contêiner de testes:
Bash

docker stop meu-container
docker rm meu-container

💡 Reflexão Final

    O Docker facilitou a execução do projeto? Por quê?

    Sim. Após empacotar a aplicação na imagem, não é necessário ter a versão correta do Node.js ou dependências configuradas no sistema local para rodar a aplicação.

    O que aconteceria se outro desenvolvedor utilizasse exatamente a mesma imagem Docker?

    O projeto funcionaria exatamente da mesma forma no computador dele, sem a necessidade de nenhuma configuração adicional de ambiente.

    Qual foi a maior dificuldade encontrada durante a atividade?

    Compreender o conceito de redirecionamento de portas (-p 3000:3000), conectando a porta física do meu computador com a porta interna do contêiner.

    Em quais tipos de projetos você acredita que o Docker seria mais útil?

    Em projetos desenvolvidos por equipes grandes, arquiteturas de microsserviços que usam múltiplas tecnologias (Node, Python, Postgres, Redis) e em ambientes de integração e entrega contínua (CI/CD).

    Depois desta prática, você considera que entende melhor o contexto de utilização do Docker? Justifique.

    Sim. A prática mostrou claramente como o Docker isola a aplicação com suas próprias dependências, eliminando o clássico problema de inconsistência entre ambientes de desenvolvimento e produção.