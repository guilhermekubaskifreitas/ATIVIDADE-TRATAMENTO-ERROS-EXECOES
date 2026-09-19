## O que é tratamento de erros?
É o conjunto de práticas usadas para prever, detectar e responder a situações que podem impedir o funcionamento correto de um programa.
## O que é uma exceção?
Basicamente é um evento que interrompe o fluxo normal de execução de um programa, geralmente lançado quando algo inesperado acontece, como por exemplo uma divisão por zero, um valor inválido, uma falha de rede, etc. Ela pode ser "capturada" e tratada em tempo de execução.
## Diferença entre erro e exceção:
* O erro é um termo mais amplo, pode ser associado a erro de sintaxe, erro lógico ou erro de sistema. Nem todo erro pode ser capturado durante a execução do código, como por exemplo erro de sintaxe, que impede a execução do código.
* Exceção é um tipo específico de erro que ocorre em tempo de execução e que pode ser lançado e capturado pelo próprio código, permitindo que o programa continue rodando de forma controlada.
  ## Por que é importante tratar erros e exceções?
* Evita que a aplicação pare de funcionar de forma abrupta.
* Permite exibir mensagens claras ao usuário em vez de mensagens técnicas confusas.
* Facilita a manutenção e a depuração do código.
* Garante que recursos (conexões, arquivos, etc.) sejam liberados corretamente mesmo quando algo dá errado.
* Aumenta a confiabilidade e a segurança do sistema.
## **Exemplo em TypeScript:**
```typescript
function dividir(a: number, b: number): number {
  if (b === 0) {
    throw new Error("Não é possível dividir por zero.");
  }
  return a / b;
}

try {
  const resultado = dividir(10, 0);
  console.log(resultado);
} catch (erro) {
  console.error("Ocorreu um erro:", (erro as Error).message);
}
```
## 2.Tratamento de exceções

Finalidade do tratamento de exceções:
O tratamento de exceções serve para capturar situações inesperadas durante a execução do programa e decidir o que fazer com elas — exibir uma mensagem, tentar novamente, registrar em log, encerrar uma operação com segurança — em vez de deixar o programa travar (crash).

## **Exemplo com try e catch:**
```typescript
function buscarUsuario(id: number): string {
  if (id <= 0) {
    throw new Error("ID de usuário inválido.");
  }
  return `Usuário ${id}`;
}

try {
  const usuario = buscarUsuario(-1);
  console.log(usuario);
} catch (erro) {
  console.log("Erro capturado:", (erro as Error).message);
}
```
## Funcionamento:
O bloco `try` contém o código que pode gerar uma exceção. Se uma exceção for lançada dentro dele (nesse caso, via `throw`), a execução do `try` é imediatamente interrompida e o controle passa para o bloco `catch`, que recebe o objeto de erro e decide como tratá-lo. Se nenhuma exceção ocorrer, o `catch` simplesmente não é executado.
## 3. `try`, `catch` e `finally`
* `try`: delimita o trecho de código que pode gerar uma exceção. É o código "sob observação".
* `catch`: é executado somente se uma exceção for lançada dentro do try. Recebe o erro como parâmetro e permite tratá-lo (exibir mensagem, logar, etc.).
* `finally`: é executado sempre, independentemente de ter ocorrido exceção ou não. É usado para código de limpeza (fechar conexões, liberar recursos, etc.).
* ## Exemplo com as três estruturas:
```typescript
 function processarArquivo(nome: string): void {
  console.log(`Abrindo arquivo: ${nome}`);

  try {
    if (nome === "") {
      throw new Error("Nome de arquivo vazio.");
    }
    console.log("Processando arquivo...");
  } catch (erro) {
    console.error("Erro ao processar:", (erro as Error).message);
  } finally {
    console.log("Fechando arquivo.");
  }
}

processarArquivo("");
processarArquivo("dados.txt");
```
Nesse exemplo, o `finally` garante que "Fechando arquivo." seja exibido nas duas chamadas, tenha havido erro ou não — simulando a liberação de um recurso que precisa acontecer sempre.
## 4. `throw`
Para que serve o `throw`:
O `throw` é usado para lançar manualmente uma exceção quando o código identifica uma situação inválida ou inesperada. Ele interrompe o fluxo normal de execução e transfere o controle para o bloco `catch` mais próximo capaz de tratar aquele erro.
## **Exemplo:**
```typescript
function validarIdade(idade: number): void {
  if (idade < 0 || idade > 120) {
    throw new Error("Idade inválida.");
  }
  console.log(`Idade válida: ${idade}`);
}

try {
  validarIdade(-5);
} catch (erro) {
  console.error("Falha na validação:", (erro as Error).message);
}
```
Aqui, `validarIdade` identifica que -5 não é uma idade válida e usa `throw` para sinalizar isso. O `try/catch` no ponto de chamada intercepta a exceção e trata o problema sem interromper o restante do programa.
## **5. Aplicação prática — transferência bancária**
```typescript
class SaldoInsuficienteError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "SaldoInsuficienteError";
  }
}

class ValorInvalidoError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "ValorInvalidoError";
  }
}

function transferir(saldo: number, valor: number): number {
  if (valor <= 0) {
    throw new ValorInvalidoError("O valor da transferência deve ser maior que zero.");
  }

  if (valor > saldo) {
    throw new SaldoInsuficienteError("Saldo insuficiente para realizar a transferência.");
  }

  const novoSaldo = saldo - valor;
  console.log(`Transferência de R$${valor.toFixed(2)} realizada com sucesso.`);
  return novoSaldo;
}

// --- Testes ---

let saldoConta = 1000;

// Situação 1: valor inválido (negativo)
try {
  saldoConta = transferir(saldoConta, -50);
} catch (erro) {
  if (erro instanceof ValorInvalidoError) {
    console.error("Erro de valor:", erro.message);
  } else {
    console.error("Erro inesperado:", erro);
  }
}

// Situação 2: saldo insuficiente
try {
  saldoConta = transferir(saldoConta, 5000);
} catch (erro) {
  if (erro instanceof SaldoInsuficienteError) {
    console.error("Erro de saldo:", erro.message);
  } else {
    console.error("Erro inesperado:", erro);
  }
}

// Situação 3: transferência válida
try {
  saldoConta = transferir(saldoConta, 200);
  console.log("Saldo atual:", saldoConta);
} catch (erro) {
  console.error("Erro inesperado:", erro);
}
```
## **Como funciona:**
* **1.** Foram criadas duas classes de exceção personalizadas (`ValorInvalidoError` e `SaldoInsuficienteError`), que estendem `Error`. Isso permite diferenciar o tipo de problema ocorrido, em vez de tratar tudo como um erro genérico.
* **2.** A função `transferir` valida as duas regras de negócio antes de executar a operação:
Se `valor <= 0`, lança `ValorInvalidoError`.
Se `valor > saldo`, lança `SaldoInsuficienteError`.
* **3.** Cada chamada da função é envolvida em um `try/catch`. Dentro do `catch`, o uso de `instanceof` permite identificar exatamente qual tipo de erro ocorreu e reagir de forma específica a cada caso.
* **4.Situação 1** `(valor -50)` dispara `ValorInvalidoError` — a transferência não é realizada e o saldo permanece 1000.
* **5.Situação 2** `(transferir 5000 de um saldo de 1000)` dispara `SaldoInsuficienteError` — novamente a operação é bloqueada.
* **6.Situação 3** `(transferir 200)` é válida, é executada com sucesso e o saldo é atualizado para 800.
 
  Essa abordagem evita que o programa quebre diante de uma entrada inválida e ainda comunica de forma clara e específica qual foi o problema em cada tentativa de transferência.
