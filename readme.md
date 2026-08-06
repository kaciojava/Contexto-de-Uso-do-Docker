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