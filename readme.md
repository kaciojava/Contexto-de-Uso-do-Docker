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