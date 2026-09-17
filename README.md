# 🚇 MetrôBot SP

Um projeto didático de Inteligência Artificial que simula um assistente
de rotas para a **Linha 1-Azul do Metrô de São Paulo**.

A ideia do projeto é juntar, em um único notebook, conceitos que
normalmente aparecem separados nas aulas: **grafos, BFS, DFS, lógica
proposicional, lógica de primeira ordem, inferência, uso de LLM e uma
interface interativa**.

O ponto principal do projeto é simples:

> **O LLM conversa com o usuário, mas quem decide a rota é o
> algoritmo.**

Isso deixa o sistema mais previsível e evita que o modelo de linguagem
invente estações, caminhos ou informações que não existem na base do
projeto.

------------------------------------------------------------------------

## 📌 Sobre o projeto

O MetrôBot recebe um pedido escrito de forma natural, como:

> "Estou na Catedral da Sé e quero ir para a Pinacoteca."

A partir disso, o sistema tenta entender:

-   onde a pessoa está;
-   para onde ela quer ir;
-   se existe alguma necessidade de acessibilidade;
-   quais estações estão fechadas;
-   quais estações possuem elevador em manutenção;
-   qual algoritmo de busca deve ser usado.

Depois de interpretar o pedido, o sistema transforma locais conhecidos
em estações de metrô, aplica as regras lógicas e procura um caminho no
grafo da Linha 1-Azul.

No final, o resultado é transformado em uma resposta mais natural para o
passageiro.

------------------------------------------------------------------------

## 🎯 Objetivos

O projeto foi pensado principalmente para fins de aprendizado. Entre os
objetivos estão:

1.  Representar uma linha de metrô como um **grafo**.
2.  Entender na prática como funciona a **BFS (Busca em Largura)**.
3.  Implementar a **DFS (Busca em Profundidade)**.
4.  Comparar o comportamento dos dois algoritmos.
5.  Aplicar **lógica proposicional**.
6.  Criar uma pequena base de conhecimento com **lógica de primeira
    ordem**.
7.  Implementar um motor de inferência usando **forward chaining**.
8.  Integrar regras lógicas com algoritmos de busca.
9.  Usar um LLM para interpretar pedidos escritos em linguagem natural.
10. Criar um modo **offline**, sem depender de um LLM.
11. Gerar respostas em linguagem mais humana.
12. Exibir visualmente o resultado da busca.
13. Criar uma interface usando `ipywidgets`.
14. Validar o funcionamento com testes automatizados.

------------------------------------------------------------------------

## 🧠 Como o MetrôBot funciona

O funcionamento pode ser resumido em algumas etapas:

``` text
Pedido do usuário
       ↓
Interpretação do texto
       ↓
Validação dos nomes
       ↓
Base de conhecimento
       ↓
Inferência lógica
       ↓
Origem e destino
       ↓
Busca no grafo
       ↓
Tratamento de bloqueios
       ↓
Resultado da rota
       ↓
Narração da resposta
       ↓
Interface / visualização
```

A separação entre essas etapas é uma das partes mais importantes do
projeto.

O modelo de linguagem não recebe a responsabilidade de "adivinhar" o
caminho. Ele ajuda principalmente na parte de comunicação. A decisão da
rota fica com o código.

------------------------------------------------------------------------

# 🗺️ Modelagem da Linha 1-Azul

A Linha 1-Azul é representada por uma lista com suas 23 estações:

``` text
Tucuruvi
Parada Inglesa
Jardim São Paulo
Santana
Carandiru
Portuguesa-Tietê
Armênia
Tiradentes
Luz
São Bento
Sé
Japão-Liberdade
São Joaquim
Vergueiro
Paraíso
Ana Rosa
Vila Mariana
Santa Cruz
Praça da Árvore
Saúde
São Judas
Conceição
Jabaquara
```

Cada estação funciona como um **nó** do grafo.

As estações vizinhas são conectadas entre si. Como o trem pode seguir
nos dois sentidos, as conexões também são feitas nos dois sentidos.

Por exemplo:

``` text
Sé ↔ Japão-Liberdade ↔ São Joaquim ↔ Vergueiro
```

A função `construir_grafo()` transforma a lista de estações em uma
estrutura de lista de adjacência.

------------------------------------------------------------------------

# 📍 Locais de interesse

O projeto também possui um pequeno cadastro de locais conhecidos.

Por exemplo:

``` python
"Catedral da Sé": "Sé"
"Pinacoteca": "Luz"
"Mosteiro de São Bento": "São Bento"
"Rua 25 de Março": "São Bento"
"Bairro da Liberdade": "Japão-Liberdade"
```

Isso permite que o usuário informe um local em vez de precisar saber o
nome da estação.

Assim, um pedido como:

``` text
Quero ir da Catedral da Sé até a Pinacoteca
```

pode ser convertido internamente para:

``` text
Sé → Luz
```

Essa conversão é feita pelas regras do sistema, e não pelo modelo de
linguagem.

------------------------------------------------------------------------

# 🔎 BFS --- Busca em Largura

A **BFS (Breadth-First Search)** percorre o grafo usando uma fila.

A ideia é visitar primeiro os nós mais próximos da origem, depois os que
estão a duas conexões de distância, depois os de três, e assim por
diante.

No projeto, ela é implementada com `deque`:

``` python
fila = deque([origem])
```

E o primeiro elemento da fila é sempre retirado:

``` python
atual = fila.popleft()
```

O sistema também guarda quem descobriu cada estação usando o dicionário
`pai`.

Isso permite reconstruir o caminho depois que o destino é encontrado.

### Por que usar BFS?

Como o grafo representa trechos entre estações e cada trecho tem o mesmo
peso no modelo didático, a BFS consegue encontrar um caminho com o menor
número de paradas.

------------------------------------------------------------------------

# 🧭 DFS --- Busca em Profundidade

A **DFS (Depth-First Search)** segue uma estratégia diferente.

Em vez de explorar primeiro todos os vizinhos próximos, ela tenta ir o
mais fundo possível por um caminho. Se chegar a um beco sem saída, volta
e tenta outra alternativa.

No projeto, a DFS é implementada usando recursão.

A ideia pode ser visualizada assim:

``` text
Origem
  ↓
Vizinho
  ↓
Vizinho
  ↓
Vizinho
  ↓
Beco sem saída
  ↓
Volta
  ↓
Tenta outro caminho
```

A DFS é útil para entender o conceito de **backtracking**, mas não tem
como objetivo principal encontrar o menor número de paradas.

------------------------------------------------------------------------

# ⚖️ BFS x DFS

O notebook executa algumas viagens usando os dois algoritmos para
comparar:

-   quantidade de paradas;
-   quantidade de estações visitadas;
-   ordem em que as estações são exploradas.

Essa comparação ajuda a visualizar uma diferença importante entre os
algoritmos.

Para o problema de encontrar o caminho com menor quantidade de trechos
em um grafo não ponderado, a BFS possui uma característica que a DFS não
garante.

------------------------------------------------------------------------

# 🚧 Estações bloqueadas

O projeto permite simular estações fechadas.

Exemplo:

``` python
bfs(
    GRAFO,
    "Sé",
    "Vergueiro",
    bloqueadas={"São Joaquim"}
)
```

Quando uma estação está bloqueada, ela não pode ser utilizada pela
busca.

A BFS e a DFS verificam as estações bloqueadas antes de adicioná-las à
exploração.

Também existe uma verificação para o caso em que a própria origem ou o
destino esteja bloqueado.

------------------------------------------------------------------------

# ♿ Acessibilidade

A acessibilidade entra no projeto por meio das regras lógicas.

O sistema considera, de forma didática:

-   se a estação está aberta;
-   se o passageiro precisa de acessibilidade;
-   se existe elevador em manutenção.

A regra proposicional utilizada é:

``` text
P ∧ (¬Q ∨ R)
```

Onde:

-   `P` = estação aberta;
-   `Q` = passageiro precisa de acessibilidade;
-   `R` = elevador funcionando.

O notebook também gera uma tabela-verdade para mostrar todas as
combinações possíveis.

------------------------------------------------------------------------

# 🧩 Lógica de primeira ordem

Além da lógica proposicional, o projeto possui uma pequena base de
conhecimento.

Ela trabalha com fatos como:

``` text
estacao("Sé")
proximo_de("Catedral da Sé", "Sé")
usuario_quer_ir("Pinacoteca")
elevador_em_manutencao("Luz")
```

A partir desses fatos, regras podem produzir novos fatos.

Por exemplo:

``` text
usuario_quer_ir("Pinacoteca")
+
proximo_de("Pinacoteca", "Luz")
↓
destino("Luz")
```

Isso é uma forma simples de demonstrar como um sistema pode tirar
conclusões a partir de informações previamente conhecidas.

------------------------------------------------------------------------

# 🔁 Forward Chaining

O motor de inferência utiliza **encadeamento para frente (forward
chaining)**.

A lógica é parecida com um efeito dominó:

``` text
Fatos iniciais
     ↓
Regra pode ser aplicada
     ↓
Novo fato
     ↓
Outra regra pode ser aplicada
     ↓
Outro fato
     ↓
...
```

O processo continua até que nenhuma regra consiga gerar uma informação
nova.

O notebook ainda registra as justificativas das deduções, permitindo
observar de onde cada fato surgiu.

------------------------------------------------------------------------

# 🧠 O planejador

A função principal que junta a parte lógica e a parte de busca é:

``` python
planejar()
```

Ela faz basicamente três coisas.

### 1. Monta os fatos

Recebe informações como:

-   origem;
-   destino;
-   necessidade de acessibilidade;
-   estações fechadas;
-   elevadores em manutenção.

### 2. Executa a inferência

As regras são aplicadas para descobrir:

-   estação de origem;
-   estação de destino;
-   bloqueios;
-   alertas relacionados à acessibilidade.

### 3. Executa a busca

Depois que a lógica já definiu o problema, a função chama:

``` python
bfs(...)
```

ou:

``` python
dfs(...)
```

dependendo da escolha do usuário.

O resultado final contém informações como:

``` python
{
    "origem": ...,
    "destino": ...,
    "algoritmo": ...,
    "caminho": ...,
    "visitados": ...,
    "bloqueadas": ...,
    "alertas": ...,
    "paradas": ...,
    "tempo_min": ...
}
```

------------------------------------------------------------------------

# 🤖 Uso de LLM

O projeto possui uma camada de interpretação de linguagem natural.

A pessoa pode escrever algo informal, por exemplo:

``` text
to na se, bora pra pinacoteca, tô de cadeira de rodas
```

O módulo de interpretação tenta transformar essa frase em uma estrutura
organizada.

A saída esperada é semelhante a:

``` json
{
  "origem": "Sé",
  "destino": "Pinacoteca",
  "acessibilidade": true
}
```

O LLM é usado como uma ponte entre a linguagem humana e os dados
estruturados que o programa precisa.

------------------------------------------------------------------------

# 🛡️ Validação e guardrails

Uma preocupação importante do projeto é não deixar o modelo inventar
nomes.

Por isso existe a função:

``` python
resolver_nome()
```

Ela compara o nome recebido com as estações e locais conhecidos.

Se o nome não estiver cadastrado, ele é rejeitado.

Isso é especialmente importante quando existe um modelo de linguagem
envolvido, porque o LLM pode produzir uma resposta linguisticamente
plausível mesmo quando a informação não está na base do sistema.

Aqui, a regra é simples:

> **Se o nome não existe na base conhecida, o sistema não aceita como
> válido.**

------------------------------------------------------------------------

# 📴 Modo offline

O projeto também pode funcionar sem um LLM.

Para isso:

``` python
PROVEDOR = "offline"
```

Nesse modo, o sistema usa uma interpretação mais simples, baseada na
procura de nomes conhecidos dentro do texto.

Isso é útil para:

-   testar o projeto sem API;
-   trabalhar sem internet;
-   evitar custos de chamadas;
-   entender a lógica principal sem depender do modelo.

O modo offline também funciona como uma espécie de plano B caso a
chamada ao LLM apresente algum problema.

------------------------------------------------------------------------

# 🔐 Configuração da API

Quando o provedor usado é o Groq, a chave da API não precisa ficar
escrita diretamente no código.

O notebook procura a chave em:

1.  **Colab Secrets**;
2.  arquivo `.env`;
3.  variável de ambiente.

A variável utilizada é:

``` text
GROQ_API_KEY
```

### Exemplo no Google Colab

Adicione a chave aos Secrets do Colab com o nome:

``` text
GROQ_API_KEY
```

Depois, o próprio código tenta recuperar essa informação.

**Nunca coloque uma chave de API diretamente no notebook antes de
compartilhar ou publicar o projeto.**

------------------------------------------------------------------------

# 🗣️ Módulo narrador

Depois que a rota é calculada, existe uma etapa responsável por
transformar os dados em uma resposta mais natural.

Por exemplo, em vez de simplesmente mostrar:

``` text
origem: Sé
destino: Vergueiro
paradas: 3
```

o narrador pode produzir algo como:

``` text
Embarque em Sé e siga pela Linha 1-Azul até Vergueiro:
3 paradas, cerca de 6 minutos.
```

O narrador recebe somente os dados calculados pelo sistema.

Ele não é responsável por escolher a rota.

------------------------------------------------------------------------

# 🎨 Visualização da linha

O notebook também possui uma função para desenhar a linha em HTML:

``` python
desenhar_linha()
```

A visualização diferencia:

-   origem;
-   destino;
-   estações do caminho;
-   estações visitadas durante a busca;
-   estações bloqueadas.

Isso ajuda bastante na parte didática, porque permite enxergar o
comportamento do algoritmo em vez de observar apenas o resultado final
no terminal.

------------------------------------------------------------------------

# 🖥️ Interface interativa

A interface é construída com `ipywidgets`.

Ela permite:

-   escrever um pedido;
-   interpretar a frase;
-   escolher a origem;
-   escolher o destino;
-   marcar necessidade de acessibilidade;
-   selecionar estações fechadas;
-   selecionar estações com elevador em manutenção;
-   escolher entre BFS e DFS;
-   executar a busca;
-   visualizar a resposta.

Tudo isso funciona dentro do próprio notebook.

------------------------------------------------------------------------

# 🧪 Testes automatizados

No final do projeto existe uma função:

``` python
rodar_testes()
```

Ela executa seis verificações.

Entre elas estão:

-   conferir se existem 23 estações;
-   verificar as estações das pontas;
-   comparar BFS e DFS em um caso conhecido;
-   testar uma estação bloqueada;
-   validar a transformação de um local em estação;
-   verificar alertas de acessibilidade;
-   garantir que passar por uma estação sem elevador não gera alerta
    indevido.

Ao final, quando tudo está correto, o notebook informa:

``` text
✔ Todos os 6 testes passaram!
```

Esses testes são importantes porque ajudam a garantir que mudanças no
código não quebrem partes que já estavam funcionando.

------------------------------------------------------------------------

# 📁 Estrutura do notebook

O projeto está organizado em etapas para facilitar o acompanhamento:

``` text
Aula_Pratica01_MetroBot.ipynb
│
├── Instalação das dependências
├── Configuração do LLM
├── Teste de conexão
├── Modelagem da Linha 1-Azul
├── Inspeção do grafo
├── Cadastro de locais
├── BFS passo a passo
├── BFS oficial
├── Testes da BFS
├── DFS
├── Comparação BFS x DFS
├── Lógica proposicional
├── Lógica de primeira ordem
├── Motor de inferência
├── Planejador
├── Intérprete
├── Narrador
├── Visualização HTML
├── Interface com ipywidgets
└── Testes automatizados
```

A divisão em etapas deixa o notebook mais próximo de uma aula prática:
primeiro são apresentados os conceitos individualmente e depois eles são
integrados.

------------------------------------------------------------------------

# 🛠️ Tecnologias utilizadas

O projeto utiliza principalmente:

-   **Python** --- linguagem principal;
-   **Google Colab / Jupyter Notebook** --- ambiente de execução;
-   **Groq** --- acesso ao modelo de linguagem quando configurado;
-   **Ollama** --- opção para execução local de modelo;
-   **ipywidgets** --- interface interativa;
-   **python-dotenv** --- leitura de variáveis de ambiente;
-   **deque** --- implementação da fila da BFS;
-   **HTML** --- visualização da linha dentro do notebook.

------------------------------------------------------------------------

# 📦 Instalação

No Google Colab ou em um ambiente Jupyter com suporte aos comandos
utilizados pelo notebook, execute a primeira célula:

``` python
%pip install -q groq ollama ipywidgets python-dotenv
```

Depois, execute as células do notebook na ordem.

Como várias partes dependem de variáveis e estruturas criadas
anteriormente, é recomendado executar o notebook de cima para baixo.

------------------------------------------------------------------------

# ▶️ Como executar

### 1. Abra o notebook

Abra:

``` text
Aula_Pratica01_MetroBot.ipynb
```

no Google Colab ou em um ambiente Jupyter compatível.

### 2. Instale as dependências

Execute a célula de instalação.

### 3. Escolha o provedor

No código de configuração:

``` python
PROVEDOR = "groq"
```

As opções disponíveis são:

``` python
"groq"
"ollama"
"offline"
```

### 4. Configure a API, se necessário

Para usar Groq, configure:

``` text
GROQ_API_KEY
```

nos Secrets do Colab, no `.env` ou nas variáveis de ambiente.

### 5. Execute as células

Execute as células na ordem em que aparecem.

### 6. Abra a interface

No final do notebook, a interface interativa será exibida.

------------------------------------------------------------------------

# 💬 Exemplos de pedidos

Alguns exemplos utilizados no projeto:

``` text
Estou na Catedral da Sé e quero ir ao Terminal Rodoviário Jabaquara
```

``` text
to na se, bora pra pinacoteca, tô de cadeira de rodas
```

``` text
Preciso sair do Mosteiro de São Bento e chegar na São Judas
```

Também existe um exemplo propositalmente inválido:

``` text
Quero ir da Sé até a Avenida Paulista
```

Nesse caso, o sistema deve perceber que o local informado não está
cadastrado na base conhecida.

------------------------------------------------------------------------

# ⏱️ Cálculo do tempo

Para fins didáticos, o projeto considera:

``` python
TEMPO_POR_TRECHO = 2
```

Ou seja, cada trecho entre estações representa **2 minutos simulados**.

Portanto:

``` text
1 trecho  → 2 minutos
2 trechos → 4 minutos
3 trechos → 6 minutos
```

Esse valor é apenas uma simplificação para o exercício. O projeto não
está consultando o tempo real dos trens.

------------------------------------------------------------------------

# ⚠️ Limitações

É importante entender o que este projeto faz e o que ele não faz.

Ele é uma **simulação didática**, não um sistema oficial de navegação do
Metrô de São Paulo.

Entre as limitações estão:

-   somente a Linha 1-Azul é representada;
-   as estações são tratadas como uma sequência simples;
-   não há integração entre linhas;
-   os tempos são simulados;
-   não há consulta de horários em tempo real;
-   não há informações reais de operação;
-   o cadastro de locais é limitado;
-   bloqueios são informados manualmente;
-   as condições de acessibilidade são simuladas;
-   a disponibilidade dos elevadores não é consultada em tempo real.

Por isso, os resultados não devem ser usados como fonte oficial para uma
viagem real.

------------------------------------------------------------------------

# 🧱 Possíveis melhorias

O projeto pode servir como ponto de partida para várias extensões.

Algumas ideias:

### 🚇 Mais linhas

Adicionar outras linhas do metrô e permitir integrações.

### 🗺️ Grafo mais realista

Representar diferentes pesos para cada trecho, incluindo tempo de viagem
e transferências.

### ♿ Acessibilidade mais detalhada

Cadastrar quais estações possuem elevadores, escadas rolantes e outras
estruturas.

### 📡 Dados em tempo real

Integrar informações de operação, interrupções e manutenção.

### 🧭 Rotas alternativas

Quando uma estação estiver bloqueada, procurar automaticamente uma
alternativa possível.

### 💬 Melhor interpretação

Permitir mais variações de linguagem natural sem perder a validação dos
nomes.

### 🌐 Aplicação web

Transformar o notebook em uma aplicação independente usando, por
exemplo, uma API e uma interface web.

### 🧪 Mais testes

Adicionar testes para diferentes combinações de origem, destino,
bloqueios e necessidades de acessibilidade.

------------------------------------------------------------------------

# 📚 O que este projeto demonstra

Mais do que simplesmente encontrar uma rota, o MetrôBot mostra como
diferentes técnicas podem trabalhar juntas.

A estrutura fica aproximadamente assim:

``` text
                 ┌─────────────────┐
                 │ Linguagem humana│
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │      LLM        │
                 │  interpretação  │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │    Validação    │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │     Lógica      │
                 │   + inferência  │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │ Busca no grafo  │
                 │    BFS / DFS    │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │ Resultado + UI  │
                 └─────────────────┘
```

Essa separação deixa bem claro o papel de cada tecnologia.

------------------------------------------------------------------------

# ⭐ Ideia central

O projeto parte de uma ideia que vale para vários sistemas de IA:

> **Usar o modelo de linguagem para entender e conversar, mas deixar
> decisões estruturadas para algoritmos determinísticos.**

No MetrôBot, isso significa que o LLM pode ajudar a entender o que o
passageiro escreveu, enquanto o grafo, as regras e os algoritmos de
busca cuidam da parte que precisa ser calculada.

Isso torna o projeto mais fácil de testar, explicar e modificar.

------------------------------------------------------------------------

# 👨‍💻 Organização do código

As principais funções do projeto são:

  Função                     Responsabilidade
  -------------------------- -------------------------------------
  `construir_grafo()`        Monta o grafo da linha
  `bfs_passo_a_passo()`      Demonstra a BFS de forma didática
  `bfs()`                    Executa a busca em largura
  `dfs()`                    Executa a busca em profundidade
  `fatos_base()`             Cria a base inicial de conhecimento
  `consultar()`              Consulta fatos por predicado
  `encadear_para_frente()`   Executa as regras de inferência
  `planejar()`               Integra lógica e busca
  `interpretar_pedido()`     Interpreta o pedido do usuário
  `resolver_nome()`          Valida nomes conhecidos
  `narrar()`                 Gera a resposta final
  `desenhar_linha()`         Cria a visualização da linha
  `rodar_testes()`           Executa os testes automatizados

------------------------------------------------------------------------

# 📌 Resumo

O **MetrôBot SP** é um exercício completo de Inteligência Artificial
aplicado a um problema fácil de visualizar: encontrar uma rota de metrô.

Ele combina:

**grafos + BFS + DFS + lógica + inferência + LLM + validação +
interface + testes**

A parte mais importante do projeto não é apenas chegar de uma estação
até outra. É mostrar como diferentes componentes podem ser organizados
para resolver o problema de maneira clara.

O notebook começa com conceitos individuais e termina com um pequeno
sistema integrado capaz de receber um pedido em linguagem natural,
interpretar as informações, aplicar regras, procurar uma rota e
apresentar o resultado ao usuário.

------------------------------------------------------------------------

## 📄 Arquivo principal

``` text
Aula_Pratica01_MetroBot.ipynb
```

Este README acompanha o notebook e serve como documentação geral do
projeto.
