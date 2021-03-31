---
title: 'Quickstart: Nova atribuição de política com .NET Core'
description: Neste arranque rápido, você usa .NET Core para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91604691"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes com .NET Core

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste arranque rápido, cria-se uma atribuição de políticas para identificar máquinas virtuais que não estão a usar discos geridos. Quando estiver concluído, identificará máquinas virtuais que _não são compatíveis._

A biblioteca .NET Core é utilizada para gerir os recursos do Azure. Este guia explica como utilizar a biblioteca .NET Core para a Política de Azure para criar uma atribuição de políticas.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um diretor de serviço da Azure, incluindo o _clienteId_ e _o clienteSecret._ Se não tiver um principal de serviço para uso com a Política Azure ou quiser criar um novo, consulte [as bibliotecas de gestão Azure para autenticação .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Salte o passo para instalar os pacotes .NET Core, pois vamos fazê-lo nos próximos passos.

## <a name="create-the-azure-policy-project"></a>Criar o projeto Azure Policy

Para ativar o .NET Core para gerir a Política Azure, crie uma nova aplicação de consola e instale os pacotes necessários.

1. Verifique se o núcleo .NET mais recente está instalado (pelo menos **3.1.8**). Se ainda não estiver instalado, descarregue-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializar uma nova aplicação de consola .NET Core denominada "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Mude os diretórios para a nova pasta do projeto e instale os pacotes necessários para a Política Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Substitua o predefinição `program.cs` pelo seguinte código e guarde o ficheiro atualizado:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Construa e publique a aplicação da `policyAssignment` consola:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui os **VMs de auditoria que não utilizam discos geridos** `06a78e20-9358-41c9-923c-fb736d382a4d` () definição. Esta definição de política identifica recursos que não estão em conformidade com as condições definidas na definição de política.

1. Altere os diretórios para o `{run-folder}` que definiu com o `dotnet publish` comando anterior.

1. Introduza o seguinte comando no terminal:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Os comandos anteriores utilizam as seguintes informações:

- `{tenantId}` - Substitua-o pela sua ID do seu inquilino
- `{clientId}` - Substitua pelo ID do cliente do seu principal de serviço
- `{clientSecret}` - Substitua pelo segredo do cliente do seu diretor de serviço
- `{subscriptionId}` - substituir pelo ID da subscrição
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

1. Inicializar uma nova aplicação de consola .NET Core denominada "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Mude os diretórios para a nova pasta do projeto e instale os pacotes necessários para a Política Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Substitua o predefinição `program.cs` pelo seguinte código e guarde o ficheiro atualizado:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Construa e publique a aplicação da `policyAssignment` consola:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Altere os diretórios para o `{run-folder}` que definiu com o `dotnet publish` comando anterior.

1. Introduza o seguinte comando no terminal:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Os comandos anteriores utilizam as seguintes informações:

- `{tenantId}` - Substitua-o pela sua ID do seu inquilino
- `{clientId}` - Substitua pelo ID do cliente do seu principal de serviço
- `{clientSecret}` - Substitua pelo segredo do cliente do seu diretor de serviço
- `{subscriptionId}` - substituir pelo ID da subscrição
- **nome** - O nome único para o objeto de atribuição de políticas. O exemplo acima utiliza os programas de _auditoria-vm-geridos._

Os resultados `response` correspondem ao que vê no **separador** de conformidade de recursos de uma atribuição de políticas na vista do portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

- Eliminar a atribuição de políticas _Audit VMs sem discos geridos Atribuição_ através do portal. A definição de política é uma incorporação, por isso não há definição para remover.

- Se desejar remover as aplicações de consola .NET Core e pacotes instalados, elimine as `policyAssignment` pastas e `policyCompliance` as pastas do projeto.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de definições políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
