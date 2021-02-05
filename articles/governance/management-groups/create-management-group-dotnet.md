---
title: 'Quickstart: Criar um grupo de gestão com .NET Core'
description: Neste quickstart, você usa .NET Core para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592590"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Quickstart: Criar um grupo de gestão com .NET Core

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Um diretor de serviço da Azure, incluindo o _clienteId_ e _o clienteSecret._ Se não tiver um principal de serviço para uso com a Política Azure ou quiser criar um novo, consulte [as bibliotecas de gestão Azure para autenticação .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Salte o passo para instalar os pacotes .NET Core, pois vamos fazê-lo nos próximos passos.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Configuração da aplicação

Para permitir que o .NET Core gere grupos de gestão, crie uma nova aplicação de consola e instale os pacotes necessários.

1. Verifique se o núcleo .NET mais recente está instalado (pelo menos **3.1.8**). Se ainda não estiver instalado, descarregue-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializar uma nova aplicação de consola .NET Core denominada "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Mude os diretórios para a nova pasta do projeto e instale os pacotes necessários para a Política Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Construa e publique a aplicação da `mgCreate` consola:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Criar o grupo de gestão

Neste quickstart, cria-se um novo grupo de gestão no grupo de gestão de raiz.

1. Altere os diretórios para o `{run-folder}` que definiu com o `dotnet publish` comando anterior.

1. Introduza o seguinte comando no terminal:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Os comandos anteriores utilizam as seguintes informações:

- `{tenantId}` - Substitua-o pela sua ID do seu inquilino
- `{clientId}` - Substitua pelo ID do cliente do seu principal de serviço
- `{clientSecret}` - Substitua pelo segredo do cliente do seu diretor de serviço
- `{groupID}` - Substitua pelo ID pelo seu novo grupo de gestão
- `{displayName}` - Substitua pelo nome amigável do seu novo grupo de gestão

O resultado é um novo grupo de gestão no grupo de gestão de raiz.

## <a name="clean-up-resources"></a>Limpar os recursos

- Elimine o novo grupo de gestão através do portal.

- Se desejar remover a aplicação da consola .NET Core e as embalagens instaladas, elimine a pasta do `mgCreate` projeto.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)