## 🏥 Cenário: Internação de Pacientes em Leitos Hospitalares

---

### 1. 🧬 **Entidade: Paciente**

```cpp
class Paciente {
  private:
    int id;
    String nome;

  public:
    Paciente(int id, String nome) {
      this->id = id;
      this->nome = nome;
    }

    int getId() { return id; }
    String getNome() { return nome; }
};
```

➡️ **Possui identidade única** (`id`). Mesmo que o nome mude, o paciente continua sendo o mesmo.

---

### 2. 🧱 **Objeto de Valor: Período de Internação**

```cpp
class Periodo {
  private:
    String dataEntrada;
    String dataSaida;

  public:
    Periodo(String entrada, String saida) {
      dataEntrada = entrada;
      dataSaida = saida;
    }

    String getPeriodo() {
      return dataEntrada + " até " + dataSaida;
    }
};
```

➡️ **Imutável** e **comparado por valor**, representa o conceito de tempo sem identidade própria.

---

### 3. 🧩 **Agregado: Internacao**

```cpp
class Internacao {
  private:
    Paciente paciente;
    int leitoId;
    Periodo periodo;

  public:
    Internacao(Paciente p, int leitoId, Periodo per)
      : paciente(p), leitoId(leitoId), periodo(per) {}

    void mostrarResumo() {
      Serial.println("Paciente: " + paciente.getNome());
      Serial.println("Leito: " + String(leitoId));
      Serial.println("Período: " + periodo.getPeriodo());
    }

    int getPacienteId() { return paciente.getId(); }
};
```

➡️ O **agregado** é `Internacao`, pois reúne:

* Uma **entidade raiz** (`Paciente`)
* Um **objeto de valor** (`Periodo`)
* E a **relação com o leito**.

---

### 4. 💾 **Repositório: InternacaoRepository**

```cpp
class InternacaoRepository {
  public:
    void salvar(Internacao internacao) {
      Serial.println("Internação salva no sistema.");
      internacao.mostrarResumo();
    }

    Internacao buscarPorPacienteId(int id) {
      // Simulação de busca
      Paciente p(id, "Paciente Exemplo");
      Periodo per("10/04/2025", "15/04/2025");
      return Internacao(p, 3, per);
    }
};
```

➡️ O **repositório** simula uma base de dados. Oculta a persistência e expõe apenas o domínio.

---

### 5. 🧠 **Serviço de Domínio: InternacaoService**

```cpp
class InternacaoService {
  private:
    InternacaoRepository repo;

  public:
    void registrarInternacao(Paciente paciente, int leitoId, String entrada, String saida) {
      Periodo periodo(entrada, saida);
      Internacao internacao(paciente, leitoId, periodo);
      repo.salvar(internacao);
    }
};
```

➡️ A lógica de **criar uma internação** envolve múltiplos elementos e não pertence exclusivamente a `Paciente` ou `Leito`, então está no serviço de domínio.

---

### 6. 🔁 Exemplo no `setup()` do Arduino

```cpp
void setup() {
  Serial.begin(9600);

  Paciente paciente1(101, "Maria da Silva");
  InternacaoService service;
  service.registrarInternacao(paciente1, 7, "20/05/2025", "25/05/2025");
}

void loop() {
  // vazio neste exemplo
}
```

---

## ✅ Resultado: Tudo modelado de forma orientada ao domínio

| Elemento               | Representa...                                    |
| ---------------------- | ------------------------------------------------ |
| `Paciente`             | **Entidade** com identidade                      |
| `Periodo`              | **Objeto de Valor**, imutável                    |
| `Internacao`           | **Agregado** com raiz e consistência             |
| `InternacaoRepository` | **Repositório** para persistência                |
| `InternacaoService`    | **Serviço de Domínio** para registrar internação |
