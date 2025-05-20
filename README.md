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
