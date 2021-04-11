---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112781"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou de comando, corra `node --version` para verificar se Node.js está instalada.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Primeiro, abra o seu terminal ou janela de comando, crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Corra `npm init -y` para criar umapackage.js **no** ficheiro com definições predefinidos.

```console
npm init -y
```

Crie um ficheiro chamado **phone-numbers-quickstart.js** na raiz do diretório que acabou de criar. Adicione-lhe o seguinte corte:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Phone Numbers para JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

A `--save` opção adiciona a biblioteca como uma dependência do seu **package.jsarquivado.**

## <a name="authenticate-the-client"></a>Autenticar o cliente

Importe o **TelefoneNumbersClient** da biblioteca do cliente e o instantie com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação do seu recurso.](../../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao topo da **phone-numbers-quickstart.js:**

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Gerir números de telefone

### <a name="search-for-available-phone-numbers"></a>Procurar números de telefone disponíveis

Para comprar números de telefone, primeiro deve procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, [as capacidades de número de telefone,](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services) [o tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e a quantidade. Note que para o tipo de número de telefone gratuito, desde que o código de área seja opcional.

Adicione o seguinte corte à sua `main` função:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Número de telefone de compra

O resultado da procura de números de telefone é um `PhoneNumberSearchResult` . Isto contém um `searchId` que pode ser passado para os números de compra API para adquirir os números na pesquisa. Note que ligar para os números de telefone de compra API resultará num encargo para a sua Conta Azure.

Adicione o seguinte corte à sua `main` função:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Atualizar as capacidades dos números de telefone

Com um número de telefone agora adquirido, adicione o seguinte código para atualizar as suas capacidades:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Obter números de telefone comprados

Depois de um número de compra, pode recuperá-lo do cliente. Adicione o seguinte código à sua `main` função para obter o número de telefone que acabou de adquirir:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Também pode recuperar todos os números de telefone adquiridos.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Liberação do número de telefone

Pode agora libertar o número de telefone adquirido. Adicione o corte de código abaixo à sua `main` função:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Executar o código

Utilize o `node` comando para executar o código que adicionou ao ficheiro **phone-numbers-quickstart.js.**

```console
node phone-numbers-quickstart.js
```