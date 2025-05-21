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
class Paciente {
  private:
    bool internado;

  public:
    Paciente() {
      internado = false;
    }

    void internar() {
      if (internado) {
        Serial.println("Erro: paciente já está internado.");
        return;
      }
      internado = true;
      Serial.println("Paciente internado.");
    }

    void darAlta() {
      if (!internado) {
        Serial.println("Erro: paciente não está internado.");
        return;
      }
      internado = false;
      Serial.println("Alta concedida ao paciente.");
    }

    bool estaInternado() {
      return internado;
    }
};

Paciente paciente;

void setup() {
  Serial.begin(9600);

  paciente.internar();     // Deve internar
  paciente.internar();     // Deve avisar que já está internado
  paciente.darAlta();      // Deve dar alta
  paciente.darAlta();      // Deve avisar que não está internado
}

void loop() {
}
```

Neste caso, a entidade ```Paciente``` sabe se internar ou receber alta, sem depender de uma função externa. A regra está encapsulada onde deveria estar: dentro da própria entidade. Isto é, esse exemplo mantém a essência do DDD: o comportamento relevante está dentro do próprio objeto do domínio — no caso, o paciente — evitando lógica solta em outros lugares.


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


## 🎯 3. Padrões Estratégicos do DDD

Os **padrões estratégicos** do DDD ajudam a lidar com a **complexidade em nível de sistema**. Em vez de pensar apenas em entidades ou regras isoladas, aqui o foco é em **separar contextos**, organizar equipes e garantir que tudo esteja falando a mesma língua.


### 3.1 🧱 Bounded Context (Contexto Delimitado)

Um **Bounded Context** é um **limite bem definido** onde **um modelo de domínio específico** é aplicado e **faz sentido completo** por si só.

#### 3.1.1 ✅ Por que isso é importante?

- Em sistemas grandes, diferentes áreas usam **as mesmas palavras com significados diferentes**.
- Separar contextos evita confusão, retrabalho e código "genérico demais".

#### 📦 Exemplo hospitalar:

- No contexto **Farmácia**, "prescrição" significa uma **lista de medicamentos**.
- No contexto **Faturamento**, "prescrição" pode significar **itens a cobrar**.
- Moral da história: ➡️ Ambos usam o termo "prescrição", mas são contextos distintos.

#### 📌 Pergunta: vocês já viram a palavra 'cliente' significar algo diferente entre setores da mesma empresa?


### 3.2 🗺️ Mapeamento de Contexto (Context Map)

O **Mapeamento de Contexto** mostra **como os diferentes Bounded Contexts se relacionam entre si**. Isso ajuda a organizar responsabilidades e integrações.

| Tipo de Relação          | Explicação Didática                                                                                                                                                                                                                                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Partnership**          | **Parceria total**: os dois contextos trabalham juntos para evoluir e alinhar seus modelos. Equipes têm contato frequente, fazem reuniões conjuntas. É uma relação de confiança mútua. <br>🧠 *Exemplo:* Equipes de Atendimento e Internação decidem juntas como será a passagem de pacientes entre setores.         |
| **Customer/Supplier**    | Um contexto **depende da entrega do outro**. Um fornece dados ou funcionalidades, o outro consome. O consumidor pode dar feedback, mas o fornecedor tem mais controle. <br>🧠 *Exemplo:* O módulo de Faturamento depende do módulo de Internação para saber quantos dias o paciente ficou.                           |
| **Conformist**           | Um contexto precisa **aceitar o modelo de outro**, mesmo que não goste. É comum em sistemas legados ou onde não há poder de negociação. <br>🧠 *Exemplo:* O módulo de Farmácia precisa adaptar-se ao formato de prescrição definido pelo módulo Clínico.                                                             |
| **Anticorruption Layer** | O contexto consumidor **não quer se contaminar** por modelos mal estruturados de outro contexto, então **cria um “tradutor”** para isolar e adaptar os dados. <br>🧠 *Exemplo:* O módulo de Faturamento recebe dados da Farmácia, mas usa uma camada de transformação para adaptá-los ao seu próprio modelo interno. |
| **Shared Kernel**        | Os dois contextos **compartilham uma pequena parte do modelo**, como uma biblioteca comum, e precisam sincronizar mudanças nessa parte. <br>🧠 *Exemplo:* Atendimento e Internação usam o mesmo objeto `Paciente`, e combinam juntos como ele deve ser estruturado.                                                  |

#### 3.2.2 🎓 Exemplo visual:

Imagine o mapa de um hospital (notem os sentidos das setas):

- Atendimento → Internação → Faturamento
- Farmácia ↔ Internação
- Faturamento ⬅ Anticorruption Layer ⬅ Farmácia


### 🗣️ 3. **Linguagem Ubíqua (Ubiquitous Language)**

> Uma **linguagem ubíqua** é um vocabulário **compartilhado entre especialistas do domínio e desenvolvedores**, que guia a modelagem e aparece no código, nos diagramas e nas conversas.

#### ✅ Benefícios:

* Reduz erros de entendimento.
* Aumenta a coesão entre código e negócio.
* Ajuda a documentação ser viva e clara.

#### 🧾 Exemplo em código:

```python
class Prescricao:
    def adicionar_medicamento(self, medicamento):
        # comportamento que o farmacêutico entende
```

➡️ Não usamos `insertItem()` ou `handleList()` — usamos o **termo real** que o farmacêutico usa.

---

### 📌 Dica para discussão com a turma:

> “O que acontece quando o código usa nomes técnicos genéricos (`data`, `item`, `controller`) e ninguém sabe do que se trata?”

👉 Isso quebra a linguagem ubíqua e enfraquece o modelo.

---

### 🧠 Resumo final

| Padrão Estratégico   | O que é           | Por que importa                       |
| -------------------- | ----------------- | ------------------------------------- |
| **Bounded Context**  | Limite do modelo  | Evita confusão e mistura de regras    |
| **Context Map**      | Mapa das relações | Organiza integração entre módulos     |
| **Linguagem Ubíqua** | Vocabulário comum | Código e negócio falam a mesma língua |

---

Se quiser, posso montar um slide com esse quadro final, ou desenhar um mapa visual de contextos em um hospital. Deseja isso?



