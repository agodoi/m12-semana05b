# DDD - Domain-Driven Design

## 1. 🗺️ Contextualização

Ao desenvolver sistemas para domínios complexos — como saúde, bancos, logística ou educação — os desafios vão muito além do código: eles envolvem regras de negócio que mudam com frequência, detalhes operacionais difíceis de modelar, e um grande risco de comunicação falha entre o time técnico e os especialistas do negócio.

### 1.1❗ Problemas comuns:

- Equipes de TI não entendem bem o negócio.
- Modelos de dados são genéricos e rasos em significado.
- As regras ficam espalhadas em diversos serviços, dificultando a manutenção.

### 1.2 🧠 Definição de DDD

Domain-Driven Design (DDD) é uma abordagem de desenvolvimento de software proposta por [Eric Evans](https://www.amazon.com.br/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215/ref=asc_df_0321125215?mcid=c33bad49cbdb37e9a17c02d039215fa7&tag=googleshopp00-20&linkCode=df0&hvadid=709883381671&hvpos=&hvnetw=g&hvrand=4661023997498018340&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9196692&hvtargid=pla-449269547899&psc=1&language=pt_BR&gad_source=1) que coloca o domínio do negócio no centro da modelagem. O foco principal é colaborar com especialistas do domínio para criar um modelo de software expressivo e alinhado à realidade da organização.

Em outras palavras, DDD é quando você projeta seu sistema entendendo profundamente as regras do negócio e modelando o código com nomes, estruturas e comportamentos que fazem sentido tanto para quem programa quanto para quem trabalha na área (como médicos, bancários, gestores, etc).

Então, podemos traduzir DDD para algo do tipo:

- Modelagem Focada no Negócio
- Desenho Centrado no Domínio
- Linguagem do Próprio Domínio

### 1.3 💬 Conceito-chave: Linguagem Ubíqua

A equipe técnica e os especialistas do negócio devem usar a mesma linguagem para descrever processos, regras e entidades. Isso significa que:

- O vocabulário usado nas reuniões é o mesmo dos nomes de classes, métodos e objetos no código.
- Evita-se o "telefone sem fio" entre analistas e desenvolvedores.
- O sistema vira um modelo vivo do negócio real.
- **Exemplo:** em vez de chamar uma classe de UserDTO ou ClienteDAO, usa-se Paciente, Leito, AltaMedica — com comportamentos reais, como admitir(), prescrever(), autorizarAlta().


| Conceito             | Explicação                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------- |
| **Domínio**          | Conjunto de regras e conhecimento sobre um problema específico da organização                            |
| **Modelagem rica**   | Representar o negócio com entidades e comportamentos fiéis à realidade                                   |
| **Linguagem ubíqua** | Vocabulário comum entre desenvolvedores e especialistas do negócio                                       |
| **DDD**              | Estratégia para criar software que reflete profundamente o negócio, com código expressivo e colaborativo |


### Pergunta:

a) Você já trabalhou ou viu um sistema em que ninguém sabia explicar direito para que serviam os nomes das tabelas ou classes no código? 

b) Qual o impacto disso?

## 2. 🔍 Modelo de Domínio Anêmico vs. Modelo Rico

### 2.1 💡 O que é um Modelo de Domínio?

Antes de comparar os dois modelos, vale lembrar: o **modelo de domínio** é a representação do conhecimento de negócio dentro do código.
É como seu sistema “entende” o mundo real — por exemplo, o que é um Paciente, uma Consulta, uma Alta Médica.

### 2.2 ❌ Modelo de Domínio Anêmico

Um **modelo anêmico** é aquele em que os objetos do domínio (como ```Paciente```, ```Pedido```, ```Produto```) têm **apenas atributos e métodos get/set**, mas **nenhum comportamento relevante**. Exemplos:

- As classes são **estruturas de dados passivas**.
- Toda a lógica de negócio fica espalhada em **serviços externos** (ex: `PacienteService`, `PedidoService`, `FaturamentoService`).
- Parece um modelo orientado a banco de dados e não ao negócio.

O nome **anêmico** é uma metáfora que quer dizer **fora do padrão**, sem capacidade de transportar oxigênio entre os órgãos, nesse caso, entre as entidades.

#### 2.2.1 📉 Problemas e Críticas:

- **Quebra o encapsulamento**: os dados ficam expostos e a lógica fica solta.
- **Baixa coesão**: a regra de negócio não está próxima dos dados que ela usa.
- **Pouca expressividade**: o código não “conversa” com o domínio.
- Dificulta testes e manutenção.

🛑 É considerado um **anti-padrão** no DDD, ou seja, **algo a ser evitado**, pois **o código deixa de refletir o domínio real**.

### 2.3 ✅ Modelo de Domínio Rico

Um **modelo rico** tem objetos do domínio que **além de dados, também contêm os comportamentos que fazem sentido para aquele conceito**.

- Exemplo:

```
class Paciente: # Aqui estamos criando a classe Paciente, que representa uma entidade do domínio hospitalar.
    def __init__(self):
        self._internado = False # O atributo _internado indica se o paciente está ou não internado.

    def internar(self): # O método internar() representa a regra de negócio para iniciar a internação de um paciente.
        if self._internado: # Se o paciente já estiver internado, ele não pode ser internado novamente — por isso a verificação com if self._internado.
            print("Erro: paciente já está internado.")
            return
        self._internado = True #Caso não esteja internado, o método atualiza o estado e emite uma mensagem.
        print("Paciente internado.")

    def dar_alta(self): # O método dar_alta() representa a regra de negócio para liberar o paciente da internação.
        if not self._internado: #Se o paciente não estiver internado, não se pode dar alta — e isso é tratado pela verificação.
            print("Erro: paciente não está internado.")
            return
        self._internado = False # Se estiver internado, ele é liberado com sucesso.
        print("Alta concedida ao paciente.")

    def esta_internado(self): #Esse método expõe de forma segura o estado de internação, sem permitir que outras partes do sistema modifiquem o valor diretamente.
        return self._internado


paciente = Paciente() #Primeiro o paciente é internado com sucesso.
paciente.internar() 
paciente.internar() #Tentar interná-lo de novo gera uma mensagem de erro.
paciente.dar_alta() #Ele recebe alta com sucesso.
paciente.dar_alta() #Tentar dar alta novamente gera outro erro.
```

Neste caso, a entidade ```Paciente``` controla seu próprio estado de internação, com os métodos ```internar()``` e ```dar_alta()``` encapsulando a lógica necessária. Não é preciso recorrer a funções externas para validar ou alterar seu comportamento. Isso torna o código mais coeso, expressivo e alinhado ao domínio real.

➡️ Em termos de DDD, este é um exemplo de modelo de domínio rico, onde os dados e comportamentos vivem juntos, refletindo com fidelidade as regras do negócio dentro da própria entidade.

### 2.4 📖 Exemplo didático de Modelo Anêmico: O caso do *Paperboy* (TripleD.io)

O artigo da [Triple D](https://www.tripled.io/25/08/2016/The-anemic-domain-model) conta o caso clássico do **entregador de jornais (paperboy)**.

#### Cenário:

- Um menino entrega jornais nas casas da vizinhança.
- Ele cobra pelo serviço de entrega.

#### 🧱 Modelo anêmico:

- ```Casa``` só tem atributos: endereço, número, nome do morador.
- A lógica de “pagar o menino”, “reclamar do jornal atrasado”, lidar com inadimplência etc., fica toda espalhada em serviços soltos e externos (`PagamentoService`, `ReclamacaoService`, etc).
- As casas e o entregador são apenas "estruturas de dados" com `get` e `set`.
- O código não **conta uma história**; ele apenas manipula dados.

```python
# Exemplo anêmico
class Casa:
    def __init__(self, nome, endereco):
        self.nome = nome
        self.endereco = endereco

# Lógica está em outro lugar
def cobrar(casa):
    # lógica de cobrança externa
```

- A classe Casa não sabe fazer nada — ela apenas contém dados (nome, endereço).
- Toda a lógica de negócio, como cobrança, reclamação ou registro de entrega, é implementada fora da entidade, em funções externas como cobrar().
- Isso quebra o encapsulamento e distribui regras importantes fora do domínio.
- Em vez de deixar cobrar() fora da classe, o ideal seria que a própria casa soubesse se deve ou não pagar, ou ao menos que o Paperboy interagisse com a casa de forma clara:

```
class Casa:
    def __init__(self, nome, endereco):
        self.nome = nome
        self.endereco = endereco

    def reclamar(self):
        print(f"{self.nome} reclamou da entrega!")

class Paperboy:
    def cobrar(self, casa):
        print(f"Cobrando {casa.nome} na rua {casa.endereco}.")
```
➡️ Agora a Casa tem comportamento, e o código começa a "contar uma história" do domínio real, como o DDD defende.

O exemplo original é anêmico porque:

- A entidade é apenas um saco de dados.
- As regras do domínio não estão associadas aos objetos que elas afetam.
- O código fica pouco expressivo e mais difícil de evoluir.

🛑 No DDD, dizemos que “os objetos devem carregar tanto os dados quanto o comportamento”.
O modelo anêmico vai contra essa ideia, e por isso é considerado um anti-padrão.

#### ✅ No modelo rico:

- ```Casa``` **sabe reclamar**.
- ```Paperboy``` **sabe registrar entrega**, **calcular valor**, **cobrar**.
- O código **reflete o mundo real** e é mais fácil de entender, manter e evoluir.

```
# Modelo rico
class Casa:
    def reclamar(self):
        print("Reclamação registrada.")

class Paperboy:
    def cobrar(self, casa):
        print(f"Cobrando {casa} pela entrega.")
```

Então, entender o exemplo do **paperboy** é entende a **diferença entre programar objetos e programar comportamentos** — que é exatamente o que o DDD defende. As classes não dizem nada sobre o domínio. Você lê `Casa` e não entende que ela está relacionada ao pagamento, à reclamação ou à entrega.

🔁 **Resultado:** O código vira uma história: `paperboy.registrarEntregaPara(casa)`, `casa.reclamar()`, `paperboy.cobrar(casa)`.

📣 **Quando o código “conta uma história do mundo real”, ele provavelmente está bem modelado.**


#### 🎓 Resumo final

| Modelo Anêmico                          | Modelo Rico                      |
| --------------------------------------- | -------------------------------- |
| Dados com pouco ou nenhum comportamento | Dados com comportamento e regras |
| Lógica espalhada em serviços externos   | Lógica encapsulada nas entidades |
| Frágil, difícil de manter               | Robusto, expressivo e coeso      |
| Anti-padrão no DDD                      | Padrão recomendado               |


## 🧩 3. Padrões Táticos do DDD

Os **padrões táticos** do DDD nos ajudam a **modelar o domínio com clareza e organização**, definindo **como representar o negócio no código** por meio de classes bem estruturadas e responsabilidades bem definidas.

### 3.1 🧬 Entidades

São objetos que **têm identidade própria**, ou seja, o que importa é “quem são” e **não apenas seus atributos**.

- Possuem **um identificador único (ID)**.
- Podem **mudar ao longo do tempo**, mas continuam sendo o mesmo objeto.

#### 🏥 Exemplo hospitalar:

```
class PacienteEntidade:
    def __init__(self, id, nome):
        self.id = id
        self.nome = nome
```

Mesmo que o nome do paciente mude (ex: após casamento), **ele continua sendo o mesmo paciente** no sistema por causa do seu ID.

**O que esse código representa?**

A classe PacienteEntidade representa uma entidade do domínio hospitalar. Em DDD, uma entidade é um objeto que:

- Tem identidade própria, ou seja, pode ser distinguido dos demais mesmo que seus atributos mudem.
- Possui ciclo de vida dentro do sistema.
- Pode ter atributos mutáveis, mas seu ID nunca muda — é isso que define quem ele é.

**Por que isso é uma entidade?**

| Característica                            | Aplicação no código                                                        |
| ----------------------------------------- | -------------------------------------------------------------------------- |
| 🔑 **Identidade única**                   | Atributo `id` representa o identificador exclusivo do paciente             |
| 🔄 **Possível mutabilidade**              | O nome do paciente pode mudar, mas o ID permanece o mesmo                  |
| 💡 **Persistência baseada em identidade** | Quando buscamos o paciente no sistema, buscamos pelo `id`, não pelo `nome` |

**Diferença entre Entidade e Objeto de Valor**

| Conceito               | Entidade                   | Objeto de Valor                          |
| ---------------------- | -------------------------- | ---------------------------------------- |
| **Possui identidade?** | ✅ Sim, via ID              | ❌ Não tem identidade                     |
| **Mutável?**           | ✅ Pode mudar com o tempo   | ❌ Sempre imutável                        |
| **Comparação**         | Feita por ID               | Feita por conteúdo dos atributos         |
| **Exemplo típico**     | Paciente, Produto, Usuário | Endereço, Período, Coordenada geográfica |


### 3.2 🧱 Objetos de Valor (Value Objects)

São objetos **sem identidade própria**, usados para **expressar um conceito com dados imutáveis**.

- Comparados por seus **atributos** e **não por identidade**.
- São **imutáveis**: se algo mudar, um novo objeto deve ser criado.

#### 🏥 Exemplo:

```
class Endereco:
    def __init__(self, rua, cidade):
        self.rua = rua
        self.cidade = cidade

    def __eq__(self, other):
        return self.rua == other.rua and self.cidade == other.cidade
```

Dois endereços com os mesmos dados são considerados **iguais**.
Você não se refere ao "endereço nº 123", mas sim ao conteúdo dele.

### 3.3 🧩 Agregados (Aggregates)

Um **agregado** é um **conjunto de entidades e objetos de valor** que forma uma **unidade de consistência**.

- Possui uma **entidade raiz**, chamada de **Aggregate Root**, que controla o acesso ao restante do agregado.
- Garante que as **regras de integridade** sejam respeitadas.

#### 🏥 Exemplo:

**Paciente Internado** pode ser o agregado, composto por:

- Entidade raiz: ```Paciente```
- Objetos de valor: ```Endereco```, ```PeriodoDeInternacao```
- Subentidades: ```Prescricao`, ```HistoricoDeAlta```

➡️ Todas as alterações devem passar **pela entidade raiz**, evitando manipulação indevida de partes isoladas.

```
# Objeto de Valor
class Endereco:
    def __init__(self, rua, cidade):
        self.rua = rua
        self.cidade = cidade

# Objeto de Valor
class PeriodoDeInternacao:
    def __init__(self, data_entrada, data_saida):
        self.data_entrada = data_entrada
        self.data_saida = data_saida

# Subentidade
class Prescricao:
    def __init__(self, descricao):
        self.descricao = descricao

# Subentidade
class HistoricoDeAlta:
    def __init__(self, data, resumo):
        self.data = data
        self.resumo = resumo

# Entidade Raiz (Aggregate Root)
class PacienteInternado:
    def __init__(self, id, nome, endereco, periodo):
        self.id = id  # Identidade da entidade raiz
        self.nome = nome
        self.endereco = endereco
        self.periodo = periodo
        self.prescricoes = []
        self.historico_de_alta = None

    def adicionar_prescricao(self, prescricao):
        self.prescricoes.append(prescricao)

    def conceder_alta(self, data, resumo):
        if len(self.prescricoes) == 0:
            print("Erro: paciente não pode ter alta sem prescrições.")
            return
        self.historico_de_alta = HistoricoDeAlta(data, resumo)
        print("Alta concedida ao paciente.")

    def exibir_resumo(self):
        print(f"Paciente: {self.nome}")
        print(f"Endereço: {self.endereco.rua}, {self.endereco.cidade}")
        print(f"Período: {self.periodo.data_entrada} até {self.periodo.data_saida}")
        print(f"Prescrições: {[p.descricao for p in self.prescricoes]}")
        if self.historico_de_alta:
            print(f"Alta em {self.historico_de_alta.data}: {self.historico_de_alta.resumo}")
        else:
            print("Paciente ainda internado.")
```

**Um agregado é uma unidade de consistência composta por:**

- Uma entidade raiz (chamada de Aggregate Root) — é por onde todas as operações devem passar.
- Outras entidades ou objetos de valor, que fazem sentido apenas dentro do contexto do agregado.
- Regras de negócio que garantem integridade e validação centralizada.

Explicação do exemplo:

| Elemento                       | Classe em Python                  | Papel no DDD                                 |
| ------------------------------ | --------------------------------- | -------------------------------------------- |
| Entidade raiz (Aggregate Root) | `PacienteInternado`               | Controla todo o agregado                     |
| Objetos de valor               | `Endereco`, `PeriodoDeInternacao` | Sem identidade própria, usados como partes   |
| Subentidades                   | `Prescricao`, `HistoricoDeAlta`   | Entidades com significado dentro do agregado |
| Regras de negócio              | `conceder_alta()`                 | Alta só é concedida se houver prescrição     |

**Por que o PacienteInternado é agregado do root?**

- Porque é por ele que você interage com todo o restante do agregado.
- Exemplo: não se pode manipular diretamente o HistoricoDeAlta ou a Prescricao — tudo passa pela entidade raiz, o que garante controle total e consistência.

**O que seria errado?**

- Permitir que outra parte do sistema altere diretamente Prescricao ou HistoricoDeAlta.
- Criar Paciente, Endereco, Prescricao como objetos separados sem relação formal — isso quebra a consistência do domínio.

### 3.4 💾 Repositórios (Repositories)

São **interfaces de acesso ao agregado**, simulando o comportamento de uma **coleção em memória**, mas que **acessam o banco de dados por trás**.

- Servem para **buscar, salvar e remover agregados**.
- **Não expõem a infraestrutura** (ex: SQL, Mongo, etc.).

#### 🏥 Exemplo:

```
class InternacaoRepository:
    def salvar(self, internacao):
        print("Internação salva no sistema.")
        internacao.mostrar_resumo()

    def buscar_por_paciente_id(self, id):
        paciente = PacienteEntidade(id, "Paciente Exemplo")
        periodo = PeriodoDeInternacao("10/04/2025", "15/04/2025")
        return Internacao(paciente, 3, periodo)
```

O desenvolvedor que usa o repositório **não precisa saber** como os dados são persistidos — só precisa pensar em "domínio".

O ```InternacaoRepository``` é um repositório, um padrão usado para isolar o domínio da lógica de persistência de dados. Em DDD, o repositório atua como uma ponte entre o domínio e a infraestrutura (como banco de dados, APIs, arquivos).

**Método salvar(self, internacao)**

- Simula salvar uma internação em um sistema (ex: banco de dados).
- Em um sistema real, isso envolveria uma chamada ao banco, mas no DDD essa parte é escondida da lógica de negócio.
- O método confirma o sucesso e imprime um resumo, reforçando que estamos tratando o agregado como uma unidade coesa.

**Método buscar_por_paciente_id(self, id)**

- Simula uma consulta ao sistema para recuperar uma internação com base no ID do paciente.
- Retorna uma nova instância do agregado Internacao, composta por:
  - Uma entidade PacienteEntidade
  - Um objeto de valor PeriodoDeInternacao
- Isso mostra como o repositório "reconstrói" um agregado inteiro, com seus dados e dependências.

### 3.5 🧠 Serviços de Domínio

Representam **operações importantes do negócio** que **não pertencem diretamente a uma única entidade ou objeto de valor**.

- São **sem estado** (stateless).
- Contêm **regras de negócio que cruzam múltiplas entidades**.

#### 🏥 Exemplo:

```
class InternacaoService:
    def __init__(self):
        self.repo = InternacaoRepository()

    def registrar_internacao(self, paciente, leito_id, entrada, saida):
        periodo = PeriodoDeInternacao(entrada, saida)
        internacao = Internacao(paciente, leito_id, periodo)
        self.repo.salvar(internacao)


paciente_exemplo = PacienteEntidade(101, "Maria da Silva")
servico = InternacaoService()
servico.registrar_internacao(paciente_exemplo, 7, "20/05/2025", "25/05/2025")

```

Essa operação envolve **Paciente + Leito**, mas não pertence exclusivamente a nenhum dos dois.
➡️ Então é melhor colocá-la em um **Serviço de Domínio**.

### 3.6 🧠 Resumo

| Padrão                 | Para que serve                                               |
| ---------------------- | ------------------------------------------------------------ |
| **Entidade**           | Representar um objeto com identidade única                   |
| **Objeto de Valor**    | Representar conceito imutável (endereços, datas, medidas)    |
| **Agregado**           | Organizar entidades e valores como uma unidade de negócio    |
| **Repositório**        | Acessar agregados sem expor a persistência                   |
| **Serviço de Domínio** | Modelar ações de negócio que não pertencem a um único objeto |

[AQUI](https://github.com/agodoi/m12-semana05b/blob/main/code/readme.md_) você encontra um exemplo completo **padrões táticos** de C++ para um hospital


## 🎯 4. Padrões Estratégicos do DDD

Os **padrões estratégicos** do DDD ajudam a lidar com a **complexidade em nível de sistema**. Em vez de pensar apenas em entidades ou regras isoladas, aqui o foco é em **separar contextos**, organizar equipes e garantir que tudo esteja falando a mesma língua.


### 4.1 🧱 Bounded Context (Contexto Delimitado)

Um **Bounded Context** é um **limite bem definido** onde **um modelo de domínio específico** é aplicado e **faz sentido completo** por si só.

#### 4.1.1 ✅ Por que isso é importante?

- Em sistemas grandes, diferentes áreas usam **as mesmas palavras com significados diferentes**.
- Separar contextos evita confusão, retrabalho e código "genérico demais".

#### 📦 Exemplo hospitalar:

- No contexto **Farmácia**, "prescrição" significa uma **lista de medicamentos**.
- No contexto **Faturamento**, "prescrição" pode significar **itens a cobrar**.
- Moral da história: ➡️ Ambos usam o termo "prescrição", mas são contextos distintos.

#### 📌 Pergunta: vocês já viram a palavra 'cliente' significar algo diferente entre setores da mesma empresa?


### 4.2 🗺️ Mapeamento de Contexto (Context Map)

O **Mapeamento de Contexto** mostra **como os diferentes Bounded Contexts se relacionam entre si**. Isso ajuda a organizar responsabilidades e integrações.

| Tipo de Relação          | Explicação Didática                                                                                                                                                                                                                                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Partnership**          | **Parceria total**: os dois contextos trabalham juntos para evoluir e alinhar seus modelos. Equipes têm contato frequente, fazem reuniões conjuntas. É uma relação de confiança mútua. <br>🧠 *Exemplo:* equipes de Atendimento e Internação decidem juntas como será a passagem de pacientes entre setores.         |
| **Customer/Supplier**    | Um contexto **depende da entrega do outro**. Um fornece dados ou funcionalidades, o outro consome. O consumidor pode dar feedback, mas o fornecedor tem mais controle. <br>🧠 *Exemplo:* o módulo de Faturamento depende do módulo de Internação para saber quantos dias o paciente ficou.                           |
| **Conformist**           | Um contexto precisa **aceitar o modelo de outro**, mesmo que não goste. É comum em sistemas legados ou onde não há poder de negociação. <br>🧠 *Exemplo:* O módulo de Farmácia precisa adaptar-se ao formato de prescrição definido pelo módulo Clínico.                                                             |
| **Anticorruption Layer** | O contexto consumidor **não quer se contaminar** por modelos mal estruturados de outro contexto, então **cria um “tradutor”** para isolar e adaptar os dados. <br>🧠 *Exemplo:* o módulo de Faturamento recebe dados da Farmácia, mas usa uma camada de transformação para adaptá-los ao seu próprio modelo interno. |
| **Shared Kernel**        | Os dois contextos **compartilham uma pequena parte do modelo**, como uma biblioteca comum, e precisam sincronizar mudanças nessa parte. <br>🧠 *Exemplo:* atendimento e Internação usam o mesmo objeto `Paciente`, e combinam juntos como ele deve ser estruturado.                                                  |


#### 4.2.1 🎯 Quando usar cada um?

- Use **Anticorruption Layer** quando for integrar com sistemas legados ou APIs que não seguem boas práticas.
- Use **Partnership** se as equipes estiverem próximas e colaborativas.
- Use **Customer/Supplier** quando um time depende de outro, mas não precisa se envolver no desenvolvimento dele.
- Use **Shared Kernel** apenas para partes realmente imutáveis e comuns entre contextos — senão, vira acoplamento disfarçado.
- Use **Conformist** como última opção, quando não há como negociar com o sistema fornecedor.

#### 🎓 Exemplo visual:

Imagine o mapa de um hospital (notem os sentidos das setas):

- Atendimento ➡️ Internação ➡️ Faturamento  
- Internação 🔁 Farmácia  
- Farmácia ⬅️ Anticorruption Layer ⬅️ Faturamento


### 4.3 🗣️ Linguagem Ubíqua (Ubiquitous Language)

Uma **linguagem ubíqua** é um vocabulário **compartilhado entre especialistas do domínio e desenvolvedores**, que guia a modelagem e aparece no código, nos diagramas e nas conversas.

#### 4.3.1 ✅ Benefícios:

- Reduz erros de entendimento.
- Aumenta a coesão entre código e negócio.
- Ajuda a documentação ser viva e clara.

#### 4.3.2 🧾 Exemplo em código:

```
class Prescricao:
    def adicionar_medicamento(self, medicamento):
        # comportamento que o farmacêutico entende
```

➡️ Não usamos `insertItem()` ou `handleList()` — usamos o **termo real** que o farmacêutico usa.

#### 4.3.3 📌 Pergunta

O que acontece quando o código usa nomes técnicos genéricos (`data`, `item`, `controller`) e ninguém sabe do que se trata?

👉 Isso quebra a linguagem ubíqua e enfraquece o modelo.

### 4.4 🧠 Resumo final

| Padrão Estratégico   | O que é           | Por que importa                       |
| -------------------- | ----------------- | ------------------------------------- |
| **Bounded Context**  | Limite do modelo  | Evita confusão e mistura de regras    |
| **Context Map**      | Mapa das relações | Organiza integração entre módulos     |
| **Linguagem Ubíqua** | Vocabulário comum | Código e negócio falam a mesma língua |

### 4.5 📊 Visualizando os Padrões Estratégicos com o Modelo C4

Para conectar os conceitos aprendidos sobre **Bounded Context** e **Mapeamento de Contexto**, vamos representar visualmente:

#### 🧱 Nível 1 – System Context Diagram
Mostra quem são os usuários e sistemas externos que interagem com o Sistema Hospital Vida Plena.

#### 🧱 Nível 2 – Container Diagram
Mostra como o sistema hospitalar é dividido em containers (módulos), cada um representando um Bounded Context com responsabilidades específicas.

👉 Esses dois níveis ajudam a traduzir o pensamento estratégico do DDD para uma visão arquitetural prática.

### Ferramentas:

* [https://c4model.com/](https://c4model.com/)
* [https://structurizr.com/dsl](https://structurizr.com/dsl)


## Hands On

### 🏥 **CASE PRÁTICO: Reestruturação de Arquitetura no Hospital Vida Plena**

#### 🎯 Contexto Geral

O **Hospital Vida Plena**, um dos maiores centros de saúde do Brasil, enfrenta atualmente sérios desafios de **governança de sistemas**. A instituição é composta por diversas áreas críticas e interdependentes, como:

* Clínica Geral
* Pronto-Atendimento (PA)
* Laboratório de Análises
* Internação
* Farmácia Hospitalar
* Financeiro e Faturamento
* Recursos Humanos
* Fornecedores e Compras

Cada uma dessas áreas opera com **sistemas próprios, não integrados**, resultando em retrabalho, inconsistências, atrasos em atendimentos, falhas de comunicação e dificuldades em consolidar dados gerenciais. Diante disso, a **diretoria executiva contratou um grupo de estudantes especializados em Engenharia de Software e Arquitetura de Sistemas** para propor uma **nova arquitetura de software** baseada em boas práticas modernas, especialmente **Domain-Driven Design (DDD)**.

---

#### 📌 Desafio do Grupo

Formem grupos simulando consultorias de tecnologia para:

1. **Modelar uma nova arquitetura de software baseada em DDD**, identificando claramente os *Bounded Contexts*.
2. **Criar um mapa de contexto** mostrando as integrações entre os módulos.
3. **Propor soluções para garantir escalabilidade, governança, e flexibilidade no longo prazo**.
4. **Exemplificar ao menos um domínio rico**, com entidades, objetos de valor e regras de negócio encapsuladas.

---

#### 📋 Requisitos do Projeto

1. **Identificação dos Bounded Contexts**

   * Separar os contextos de negócio (ex: Atendimento, Internação, Faturamento, etc.)
   * Evitar a criação de um modelo anêmico: cada contexto deve conter comportamentos e não apenas atributos.

2. **Mapeamento Estratégico**

   * Criar um **Context Map** com tipos de relacionamento entre os contextos: *Shared Kernel, Customer/Supplier, Conformist, Anticorruption Layer*.

3. **Exemplo Prático de um Contexto**

   * Modelar, por exemplo, o *contexto de Internação*, contendo:

     * Entidades como `Paciente`, `Leito`, `AltaMédica`
     * Objetos de valor como `PeríodoDeInternação`
     * Regras como validação de alta apenas com exames e medicação concluídos

4. **Governança de Dados**

   * Propor estratégias de orquestração ou coreografia entre os contextos
   * Apontar onde usar eventos de domínio, APIs REST, filas (mensageria), etc.

5. **Tecnologias Sugeridas**

   * O grupo pode propor a aplicação dos conceitos usando qualquer linguagem.

---

#### 📦 Entregáveis Esperados

* Documento técnico (PDF) contendo:

  * Diagrama dos *Bounded Contexts* **[15min]**
  * Descrição de pelo menos 3 contextos com exemplos de entidades, objetos de valor e serviços de domínio. **[15min]**
  * Diagrama do mapa de contextos com explicações sobre os contratos entre os contextos. **Usar ferramenta. [30min]**
  * Discussão sobre o modelo de dados anêmico e justificativa da abordagem DDD adotada. **[30min]**

* Apresentação de pitch (5 a 8 slides) com: **[10min por grupo]**

  * Visão geral da arquitetura
  * Justificativas técnicas
  * Benefícios esperados para a governança do hospital

---

#### ✅ Avaliação

| Critério                                       | Peso |
| ---------------------------------------------- | ---- |
| Identificação clara dos contextos e fronteiras | 2,0  |
| Modelagem rica de pelo menos 1 contexto        | 2,0  |
| Coerência do mapa de contexto e contratos      | 2,0  |
| Justificativa do uso de DDD vs alternativas    | 2,0  |
| Clareza, organização e viabilidade da proposta | 2,0  |

---

#### 💡 Dicas

* **Evitem modelos anêmicos**. Entidades com apenas atributos e getters/setters são um sinal de má modelagem.
* **Não confundam DAO/DTO com entidades de domínio**.
* **Use o vocabulário real da área de saúde**: falem com especialistas da área se possível.
* **Nem tudo precisa ser microserviço** — foquem na separação conceitual, não apenas técnica.
