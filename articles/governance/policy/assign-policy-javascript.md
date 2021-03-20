---
title: 'Quickstart: Nova atribuição de políticas com o JavaScript'
description: Neste quickstart, você usa o JavaScript para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91349097"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando o JavaScript

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste arranque rápido, cria-se uma atribuição de políticas para identificar máquinas virtuais que não estão a usar discos geridos. Quando estiver concluído, identificará máquinas virtuais que _não são compatíveis._

A biblioteca JavaScript é usada para gerir recursos Azure a partir da linha de comando ou em scripts. Este guia explica como usar a biblioteca JavaScript para criar uma atribuição de políticas.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição Azure**: Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Node.js**: [Node.js](https://nodejs.org/) versão 12 ou superior é necessária.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Adicione as bibliotecas de política

Para permitir que o JavaScript funcione com a Política Azure, as bibliotecas devem ser adicionadas. Estas bibliotecas funcionam onde quer que o JavaScript possa ser utilizado, incluindo [a bash no Windows 10](/windows/wsl/install-win10).

1. Crie um novo projeto de Node.js executando o seguinte comando.

   ```bash
   npm init -y
   ```

1. Adicione uma referência à biblioteca de fios.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência às bibliotecas da Política Azure.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Adicione uma referência à biblioteca de autenticação Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verifique empackage.jsem _é a_ versão `@azure/arm-policy` **3.1.0** ou superior, `@azure/arm-policyinsights` é a versão **3.2.0** ou superior, e `@azure/ms-rest-nodeauth` é a versão **3.0.5** ou superior.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui os **VMs de auditoria que não utilizam discos geridos** `06a78e20-9358-41c9-923c-fb736d382a4d` () definição. Esta definição de política identifica recursos que não estão em conformidade com as condições definidas na definição de política.

1. Crie um novo ficheiro chamado _policyAssignment.js_ e introduza o seguinte código.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Introduza o seguinte comando no terminal:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Os comandos anteriores utilizam as seguintes informações:

- **subID** - O ID de subscrição para contexto de autenticação. Certifique-se de que substitui `{subscriptionId}` pela sua assinatura.
- **nome** - O nome único para o objeto de atribuição de políticas. O exemplo acima utiliza os programas de _auditoria-vm-geridos._
- **displayName** - Mostrar o nome para a atribuição de política. Neste caso, está a utilizar _VMs de auditoria sem serviço de discos geridos_.
- **policyDefID** – O caminho de definição de política, baseado no qual está a usar para criar a atribuição. Neste caso, é o ID de definição de política _Audit VMs que não usam discos geridos_.
- **descrição** - Uma explicação mais profunda do que a apólice faz ou por que é atribuída a este âmbito.
- **âmbito** - Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de políticas é executada. Pode ir de um grupo de gestão a um recurso individual. Certifique-se de que `{scope}` substitui por um dos seguintes padrões:
  - Grupo de gestão: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subscrição: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Está agora pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Agora que a sua atribuição política foi criada, pode identificar recursos que não estão em conformidade.

1. Crie um novo ficheiro chamado _policyState.js_ e introduza o seguinte código.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Introduza o seguinte comando no terminal:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

`{subscriptionId}`Substitua-se pela subscrição que pretende ver os resultados de conformidade para a atribuição de políticas denominada "audit-vm-managedisks" que criámos nos passos anteriores. Para obter uma lista de outros âmbitos e formas de resumir os dados, consulte os [métodos PolicyStates*.](/javascript/api/@azure/arm-policyinsights/)

Os resultados assemelham-se ao seguinte exemplo:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Os resultados coincidem com o que vê no **separador** de conformidade de recursos de uma atribuição de políticas na vista do portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

- Eliminar a atribuição de políticas _Audit VMs sem discos geridos Atribuição_ através do portal. A definição de política é uma incorporação, por isso não há definição para remover.

- Se desejar remover as bibliotecas instaladas da sua aplicação, execute o seguinte comando.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de definições políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)