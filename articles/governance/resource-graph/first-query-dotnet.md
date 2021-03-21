---
title: 'Quickstart: A sua primeira consulta .NET Core'
description: Neste arranque rápido, siga os passos para ativar os pacotes De NuGet de Gráfico de Recurso para .NET Core e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917576"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando .NET Core

O primeiro passo para a utilização do Azure Resource Graph é verificar se estão instaladas as embalagens necessárias para o Núcleo .NET. Este quickstart acompanha-o através do processo de adição dos pacotes à sua instalação .NET Core.

No final deste processo, terá adicionado as embalagens à sua instalação .NET Core e executará a sua primeira consulta de Gráfico de Recurso.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um diretor de serviço da Azure, incluindo o _clienteId_ e _o clienteSecret._ Se não tiver um principal de serviço para uso com Gráfico de Recurso ou quiser criar um novo, consulte [as bibliotecas de gestão Azure para autenticação .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Salte o passo para instalar os pacotes .NET Core, pois vamos fazê-lo nos próximos passos.

## <a name="create-the-resource-graph-project"></a>Criar o projeto Gráfico de Recursos

Para permitir que .NET Core questione o Azure Resource Graph, crie uma nova aplicação de consola e instale os pacotes necessários.

1. Verifique se o núcleo .NET mais recente está instalado (pelo menos **3.1.5**). Se ainda não estiver instalado, descarregue-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicialize uma nova aplicação de consola .NET Core chamada "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Altere os diretórios para a nova pasta do projeto e instale os pacotes necessários para o Gráfico de Recursos Azure:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

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
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Construa e publique a aplicação da `argQuery` consola:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com a aplicação de consola .NET Core construída e publicada, é hora de experimentar uma consulta simples de Gráfico de Recurso. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

Em cada chamada `argQuery` para, existem variáveis que são usadas que você precisa substituir por seus próprios valores:

- `{tenantId}` - Substitua-o pela sua ID do seu inquilino
- `{clientId}` - Substitua pelo ID do cliente do seu principal de serviço
- `{clientSecret}` - Substitua pelo segredo do cliente do seu diretor de serviço
- `{subscriptionId}` - substituir pelo ID da subscrição

1. Altere os diretórios para o `{run-folder}` que definiu com o `dotnet publish` comando anterior.

1. Execute a sua primeira consulta de gráfico de recurso Azure utilizando a aplicação compilada da consola .NET Core:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Altere o parâmetro final para `argQuery.exe` alterar a consulta para a propriedade `order by` **Nome:**

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Altere o parâmetro final para `argQuery.exe` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover a aplicação da consola .NET Core e as embalagens instaladas, pode fazê-lo eliminando a pasta do `argQuery` projeto.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma aplicação de consola .NET Core com os pacotes de Gráfico de Recursos necessários e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)