---
title: 'Tutorial: Use as APIs REST'
description: Este tutorial descreve como utilizar as APIs de repouso Azure Purview para aceder ao conteúdo do seu catálogo.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951247"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Use as APIs REST

Neste tutorial, você aprende a usar as APIs de repouso Azure Purview. Quem quiser submeter dados a um Catálogo Azure Purview, incluir o catálogo como parte de um processo automatizado, ou construir a sua própria experiência de utilizador no catálogo pode usar as APIs REST para o fazer.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um principal de serviço (aplicação).
> * Configure o seu catálogo para confiar no principal serviço (aplicação).
> * Consulte a documentação DO REST APIs.
> * Recolher os valores necessários para utilizar as APIs REST.
> * Use o cliente do Carteiro para ligar para as APIs REST.
> * Gere um cliente .NET para chamar as APIs REST.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Para começar, deve ter uma conta Azure Purview existente. Se não tiver um catálogo, consulte o [quickstart para criar uma conta Azure Purview](create-catalog-portal.md).

* Se precisar de adicionar dados ao seu catálogo, consulte o [tutorial para executar o kit de arranque e os dados de digitalização](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Criar um principal serviço (aplicação)

Para que um cliente da REST API aceda ao catálogo, o cliente deve ter um principal serviço (aplicação), e uma identidade que o catálogo reconhece e está configurado para confiar. Quando faz chamadas de API REST para o catálogo, eles usam a identidade do diretor de serviço.

Os clientes que utilizaram os principais de serviço existentes (IDs de aplicação) tiveram uma elevada taxa de falha. Por isso, recomendamos a criação de um novo diretor de serviço para chamar APIs.

Para criar um novo diretor de serviço:

1. A partir do [portal Azure,](https://portal.azure.com)procure e selecione **Azure Ative Directory**.
1. Na página **Azure Ative Directory,** selecione registos de **Aplicações** a partir do painel esquerdo.
1. Selecione **Novo registo**.
1. Na página **de registo de uma candidatura:**
    1. Introduza um **Nome** para a inscrição (nome principal do serviço).
    1. Selecione **Contas apenas neste diretório organizacional (apenas _&lt; o nome &gt; do seu inquilino_ - Inquilino Único)**.
    1. Para **redirecionar URI (opcional)**, selecione **Web** e introduza um valor. Este valor não precisa de ser um URI válido.
    1. Selecione **Registar**.
1. Na nova página principal do serviço, copie os valores do **nome Display** e do **ID da Aplicação (cliente)** para guardar para mais tarde.

   O ID da aplicação é o `client_id` valor no código de amostra.

Para utilizar o principal serviço (aplicação), tem de obter a sua palavra-passe. Eis como:

1. A partir do portal Azure, procure e selecione **O Diretório Ativo Azure**, e, em seguida, selecione **as inscrições** da App a partir do painel esquerdo.
1. Selecione o seu principal serviço (aplicação) da lista.
1. Selecione **Certificados & segredos** do painel esquerdo.
1. Selecione **Novo segredo do cliente**.
1. Na página secreta do Cliente adicionar **um cliente,** introduza uma **Descrição,** selecione um tempo de validade em **Expiração** e, em seguida, selecione **Adicionar**.

   Na página **de segredos** do Cliente, a cadeia na coluna **Valor** do seu novo segredo é a sua palavra-passe. Poupe este valor.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Imagem mostrando o segredo de um cliente.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Configure o seu catálogo para confiar no principal serviço (aplicação)

Para configurar a Azure Purview para confiar no seu novo diretor de serviço:

1. Navegue para a sua conta Desemis

1. Na página da **conta '',',** selecione o **separador Access control (IAM)**

1. Clique **+ Adicionar**

1. Selecione **Adicionar atribuição de função**

1. Para o tipo de papel em **Purview Data Curator**

    > [!Note]
    > Para obter mais informações sobre as permissões do Catálogo de Dados do Azure Purview, consulte [as permissões do Catálogo.](catalog-permissions.md) Por exemplo, se precisar de permissão para ativar a verificação, o tipo de Função deve ser **o Administrador fonte de dados do Purview**.

1. Para **atribuir acesso para** deixar o padrão, **utilizador, grupo ou principal de serviço**

1. Para **Selecionar insira** o nome do principal de serviço criado previosly que deseja atribuir e, em seguida, clique no seu nome no painel de resultados.

1. Clique em **Guardar**

Agora configuraste o diretor de serviço como administrador de aplicações, o que lhe permite enviar conteúdo para o catálogo.

## <a name="view-the-rest-apis-documentation"></a>Ver a documentação DO REST APIs

Para ver a documentação da API Swagger, faça o download [PurviewCatalogAPISwagger.zip, ](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip)extraia os seus ficheiros e abra index.html.

Se quiser saber mais sobre a pesquisa avançada/sugerir API que a Azure Purview fornece, consulte a documentação do Filtro de Pesquisa de API rest. O cliente gerado pelo AutoRes não suporta atualmente parâmetros de pesquisa personalizados. Para encontrar soluções, siga o documento do filtro de pesquisa para definir as classes de filtro no código como parâmetros de chamada API. O documento index.html tem exemplos destas APIs.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Recolher os valores necessários para utilizar as APIs REST

Encontrar e guardar os seguintes valores:

* ID do Inquilino:
  * No [portal Azure,](https://portal.azure.com)procure e selecione **O Diretório Ativo Azure**.
  * Na secção **Gerir** no painel esquerdo, selecione **Propriedades,** encontre o **ID** do Inquilino e, em seguida, selecione o ícone **copy para clipboard** para guardar o seu valor.
* Ponto final do Atlas:
  * A partir da página de [contas Azure Purview](https://aka.ms/purviewportal) no portal Azure, encontre e selecione a sua conta Azure Purview na lista.
  * Selecione **Propriedades**, encontre **o Atlas Endpoint** e, em seguida, selecione o ícone **copy para clipboard** para guardar o seu valor. Retire a *parte https://* da cadeia quando a utilizar mais tarde.
* Nome da conta:
  * Extraia o nome do seu catálogo da cadeia de ponta do Atlas. Por exemplo, se o seu ponto final do Atlas `https://ThisIsMyCatalog.catalog.purview.azure.com` for, o nome da sua conta é `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Use o cliente do Carteiro para ligar para as APIs REST

1. Instale o [cliente carteiro.](https://www.getpostman.com/)
1. A partir do cliente, selecione **Import**, e use [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Selecione **Coleções** e, em seguida, selecione **Teste**.
1. **Selecione Get Token**:
    1. No URL ao lado do POST, substitua *&lt; o seu id &gt; inquilino* pelo iD do inquilino que copiou na secção anterior.
    1. Selecione o **separador Corpo** e substitua os espaços reservados no caminho e no corpo do passo anterior.

       Depois de selecionar **Enviar,** o corpo de resposta contém uma estrutura JSON, incluindo o nome *access_token* e um valor de cadeia citado. 
    1. Copie o valor simbólico do portador (sem cotações), para utilizar no passo seguinte.

1. Selecione **/v2/tipos/typedefs**:
    1. Substitua o espaço reservado no caminho pelo valor do seu ponto final do atlas. Este valor pode ser obtido navegando para a instância do catálogo no portal Ibiza e clicando na visão geral. 

       O token do portador é definido a partir do primeiro passo (ou pode copiá-lo manualmente no separador "Autorização").

    1. **Selecione Enviar** para obter a resposta.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Gere um cliente .NET para ligar para as APIs REST

### <a name="install-autorest"></a>Instalar autorest



1. [Instale Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Abra o PowerShell e execute o seguinte comando:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Faça o download rest-api-specs.zip e crie o cliente

1. Descarregue [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) e extraia os seus ficheiros.
1. Executar o seguinte comando em PowerShell a partir da pasta extraída de restante-api-specs:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   A saída deste processo cria uma pasta PurviewCatalogClient e subfolder CSharp na pasta de restante-api-specs.

### <a name="create-a-sample-net-console-application"></a>Criar uma aplicação de consola .NET amostra .NET

1. Open Visual Studio 2019. Estas instruções foram testadas com a edição comunitária gratuita.
1. A partir da nova página do **projeto,** crie um projeto **de App de Consola (.NET Core)** em C#.
1. Copiar e colar o [código](#sample-code-for-the-console-application)de amostra fornecido .
1. Substitua *a contaName*, *servicePrincipalId,* *servicePrincipalKey*, e *tenantId* pelos valores que recolheu anteriormente.
1. Utilize **o Solution Explorer** para adicionar uma pasta chamada **Gerada** no projeto Visual Studio.
1. Copie o resto-api-specs\PurviewCatalogClient\CSharp que criou anteriormente para a pasta \Gerada. Utilize o File Explorer ou o pedido de linha de comando para a operação de cópia, que irá acionar o Visual Studio para adicionar automaticamente os ficheiros ao projeto.
1. No **Solution Explorer,** clique com o botão direito no projeto e, em seguida, selecione **Gerir pacotes NuGet**.
1. Selecione o **separador Procurar.** Localizar e selecionar **Microsoft.Rest.ClientRuntime**.
1. Certifique-se de que a versão é pelo menos 2.3.21 e, em seguida, **selecione Instalar**.
1. Compile e execute a aplicação.

O código de amostra devolve uma contagem de quantos doses estão no catálogo e mostra como lidar com as atribuições de funções. Para mais detalhes, consulte `DoRoleAssignmentOperations()` o código de amostra. Para obter mais informações sobre o projeto, consulte [a Configuração do Projeto.](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)

### <a name="sample-code-for-the-console-application"></a>Código de amostra para a aplicação da consola

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir origens de dados](manage-data-sources.md)
