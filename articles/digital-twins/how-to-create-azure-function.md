---
title: Criar uma função no Azure para processar dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função no Azure que possa aceder e ser desencadeada por gémeos digitais.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480770"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Conecte aplicações de função em Azure para processamento de dados

Os gémeos digitais podem ser atualizados com base em dados utilizando [rotas de eventos](concepts-route-events.md) através de recursos compute. Por exemplo, uma função que é feita usando [as Funções Azure](../azure-functions/functions-overview.md) pode atualizar um gémeo digital em resposta a:
* Dados de telemetria do dispositivo do Azure IoT Hub.
* Uma alteração de propriedade ou outros dados de outro gémeo digital dentro do gráfico gémeo.

Este artigo mostra-lhe como criar uma função em Azure para uso com Azure Digital Twins. Para criar uma função, seguirá estes passos básicos:

1. Criar um projeto Azure Functions em Estúdio Visual.
2. Escreva uma função que tenha um gatilho [Azure Event Grid.](../event-grid/overview.md)
3. Adicione código de autenticação à função para que possa aceder a Azure Digital Twins.
4. Publique a aplicação de função para a Azure.
5. Configurar [a segurança](concepts-security.md) para a aplicação de função.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Pré-requisito: Criar Gémeos Digitais Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Criar uma aplicação de função no Visual Studio

No Visual Studio 2019, selecione **File**  >  **New**  >  **Project**. Procure o modelo **Azure Functions.** Selecione **Seguinte**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Screenshot do Visual Studio mostrando o novo diálogo do projeto. O modelo de projeto Azure Functions é destacado.":::

Especifique um nome para a aplicação de função e, em seguida, __selecione Criar__.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Screenshot do Visual Studio mostrando o diálogo para configurar um novo projeto. As configurações incluem nome do projeto, localização de poupança, escolha para criar uma nova solução e nome de solução.":::

Selecione o gatilho do tipo de aplicação de função **Event Grid** e, em seguida, selecione __Criar__.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Screenshot do Estúdio Visual mostrando o diálogo para criar uma nova aplicação Azure Functions. A opção de gatilho da Grelha de Eventos está em destaque.":::

Após a criação da aplicação de função, o Visual Studio gera uma amostra de código num ficheiro *.cs Function1* na sua pasta de projeto. Esta função curta é usada para registar eventos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Screenshot do Estúdio Visual. A janela do projeto para o novo projeto é mostrada. O código para uma função de amostra é mostrado num ficheiro chamado Função1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Escreva uma função que tenha um gatilho de Grade de Eventos

Pode escrever uma função adicionando um SDK à sua aplicação de função. A aplicação de função interage com a Azure Digital Twins utilizando o [Azure Digital Twins SDK para .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Para utilizar o SDK, terá de incluir os seguintes pacotes no seu projeto. Instale as embalagens utilizando o gestor de pacotes Visual Studio NuGet. Ou adicione as embalagens utilizando `dotnet` uma ferramenta de linha de comando.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identidade](https://www.nuget.org/packages/Azure.Identity/)
* [System.net.http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Em seguida, no Visual Studio Solution Explorer, abra o ficheiro _Function1.cs_ que inclui o seu código de amostra. Adicione as `using` seguintes declarações para as embalagens.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Adicionar código de autenticação à função

Agora declare variáveis de nível de classe e adicione código de autenticação que permitirá a função aceder a Azure Digital Twins. Adicione as variáveis e o código à sua função no ficheiro _.cs Função1._

* **Código para ler o URL de serviço Azure Digital Twins como uma variável ambiental.** É uma boa prática ler o URL de serviço a partir de uma variável ambiental em vez de codificar duramente na função. Você vai definir o valor desta variável ambiental [mais tarde neste artigo](#set-up-security-access-for-the-function-app). Para obter mais informações sobre variáveis ambientais, consulte [Gerir a sua aplicação de função.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Uma variável estática para manter um exemplo httpClient.** HttpClient é relativamente caro de criar, por isso queremos evitar criá-lo para cada invocação de funções.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Credenciais de identidade geridas.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Uma variável local _DigitalTwinsClient._** Adicione a variável dentro da sua função para manter a sua instância de cliente Azure Digital Twins. *Não* faça esta variável estática dentro da sua classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Um cheque nulo para _adtInstanceUrl_.** Adicione o cheque nulo e, em seguida, enrole a sua lógica de função num bloco de tentativa/captura para apanhar exceções.

Após estas alterações, o seu código de função será o seguinte exemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Agora que a sua candidatura está escrita, pode publicá-la ao Azure.

## <a name="publish-the-function-app-to-azure"></a>Publique a aplicação de funções no Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Verifique a publicação da sua função

1. Inscreva-se utilizando as suas credenciais no [portal Azure](https://portal.azure.com/).
2. Na caixa de pesquisa na parte superior da janela, procure o nome da aplicação da sua função e, em seguida, selecione-a.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Screenshot mostrando o portal Azure. No campo de pesquisa, insira o nome da aplicação de função." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Na página **da aplicação Função** que abre, no menu à esquerda, escolha **Funções**. Se a sua função for publicada com sucesso, o seu nome aparece na lista.

    > [!Note] 
    > Pode ter de esperar alguns minutos ou refrescar a página algumas vezes antes de a sua função aparecer na lista de funções publicadas.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Ver funções publicadas no portal Azure." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Para aceder ao Azure Digital Twins, a sua aplicação de função necessita de uma identidade gerida pelo sistema com permissões para aceder à sua instância Azure Digital Twins. Vais marcar isso a seguir.

## <a name="set-up-security-access-for-the-function-app"></a>Configurar o acesso à segurança para a aplicação de função

Pode configurar o acesso de segurança para a aplicação de função utilizando o CLI Azure ou o portal Azure. Siga os passos para a sua opção preferida.

# <a name="cli"></a>[CLI](#tab/cli)

Executar estes comandos em [Azure Cloud Shell](https://shell.azure.com) ou numa [instalação local do Azure CLI](/cli/azure/install-azure-cli).
Pode utilizar a identidade gerida pelo sistema da aplicação para lhe conferir o papel **de Proprietário de Dados de Gémeos Digitais Azure** para a sua instância Azure Digital Twins. A função dá à aplicação de função permissão no caso de realizar atividades de data plane. Em seguida, torne o URL da instância acessível à sua função definindo uma variável ambiental.

### <a name="assign-an-access-role"></a>Atribuir uma função de acesso

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

O esqueleto da função em exemplos anteriores requer que lhe seja passado um símbolo portador. Se o token do portador não for aprovado, a aplicação de função não pode autenticar-se com a Azure Digital Twins. 

Para garantir que o token do portador é passado, crie [permissões de identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) para que a aplicação de função possa aceder a Azure Digital Twins. Configura estas permissões apenas uma vez para cada aplicação de função.


1. Utilize o seguinte comando para ver os detalhes da identidade gerida pelo sistema para a função. Tome nota do `principalId` campo na saída.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Se o resultado estiver vazio em vez de mostrar detalhes de identidade, crie uma nova identidade gerida pelo sistema para a função utilizando este comando:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > A saída apresenta detalhes da identidade, incluindo o `principalId` valor necessário para o passo seguinte. 

1. Utilize o `principalId` valor no seguinte comando para atribuir a identidade da aplicação de função à função _Azure Digital Twins Data Owner_ para a sua instância Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Configurar definições da aplicação

Torne o URL do seu exemplo acessível à sua função definindo uma variável ambiental para o mesmo. Para obter mais informações sobre variáveis ambientais, consulte [Gerir a sua aplicação de função.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

> [!TIP]
> O URL da instância Azure Digital Twins é feito adicionando *https://* ao início do nome de anfitrião do seu caso. Para ver o nome do anfitrião, juntamente com todas as propriedades do seu caso, corra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Complete os seguintes passos no [portal Azure](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Atribuir uma função de acesso

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Uma identidade gerida atribuída ao sistema permite que os recursos do Azure autentem serviços na nuvem (por exemplo, Azure Key Vault) sem armazenar credenciais em código. Depois de ativar a identidade gerida atribuída pelo sistema, todas as permissões necessárias podem ser concedidas através do controlo de acesso baseado em funções Azure. 

O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida deste recurso. Além disso, cada recurso pode ter apenas uma identidade gerida atribuída ao sistema.

1. No [portal Azure,](https://portal.azure.com/)procure a sua aplicação de função digitando o seu nome na caixa de pesquisa. Selecione a sua aplicação a partir dos resultados. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot do portal Azure. O nome da aplicação da função está na barra de pesquisa do portal, e o resultado da pesquisa é realçado.":::

1. Na página da aplicação de funções, no menu à esquerda, selecione __Identidade__ para trabalhar com uma identidade gerida para a função. Na página atribuída ao __Sistema,__ verifique se o __Estado__ está definido para **On**. Se não for, coloque-o de vez em quando e depois **guarde** a mudança.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Screenshot do portal Azure. Na página Identidade para a aplicação de função, a opção 'Estado' está definida para 'On'." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Selecione __atribuições de funções Azure__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Screenshot do portal Azure. Na página identidade da Função Azure, em Permissões, destacam-se as atribuições de funções Azure do botão." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Selecione __+ Adicionar atribuição de função (Pré-visualização)__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Screenshot do portal Azure. Na página de atribuições de funções Azure, destaca-se a atribuição de função de adicionar botão (Preview)." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Na página __'Adicionar função' (Pré-visualização),__ selecione os seguintes valores:

    * **Âmbito**: _Grupo de recursos_
    * **Subscrição**: selecione a sua subscrição do Azure.
    * **Grupo de recursos**: Selecione o seu grupo de recursos.
    * **Função**: _Azure Digital Twins Data Owner_

    Guarde os detalhes selecionando __Guardar__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Screenshot do portal Azure, mostrando como adicionar uma nova atribuição de funções. O diálogo mostra campos para o Scope, Subscrição, Grupo de Recursos e Função.":::

### <a name="configure-application-settings"></a>Configurar definições da aplicação

Para tornar o URL da sua instância Azure Digital Twins acessível à sua função, pode definir uma variável ambiental. As configurações da aplicação são expostas como variáveis ambientais para permitir o acesso à instância Azure Digital Twins. Para obter mais informações sobre variáveis ambientais, consulte [Gerir a sua aplicação de função.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

Para definir uma variável ambiental com o URL da sua instância, primeiro encontre o nome de anfitrião do seu caso: 

1. Procure o seu exemplo no [portal Azure](https://portal.azure.com). 
1. No menu à esquerda, selecione __Overview__. 
1. Copie o valor __do nome do anfitrião.__

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Screenshot do portal Azure. Na página geral do caso, o valor do nome do anfitrião é realçado.":::

Pode agora criar uma definição de aplicação:

1. Na barra de pesquisa do portal, procure a sua aplicação de função e, em seguida, selecione-a a partir dos resultados.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot do portal Azure. O nome da aplicação de função está a ser pesquisado na barra de pesquisa do portal. O resultado da pesquisa é realçado.":::

1. À esquerda, __selecione Configuração__. Em seguida, no separador __Definições de Aplicação,__ selecione __+ nova definição de aplicação__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Screenshot do portal Azure. No separador Configuração para a aplicação de função, destaca-se o botão para criar uma nova definição de aplicação.":::

1. Na janela que se abre, utilize o valor do nome do anfitrião copiado para criar uma definição de aplicação.
    * **Nome**: ADT_SERVICE_URL
    * **Valor**: https://{your-azure-digital-twins-host-name}
    
    Selecione __OK__ para criar uma definição de aplicação.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Screenshot do portal Azure. Na página de definição de aplicação Add/Edit, os campos Nome e Valor são preenchidos. O botão O K é realçado.":::

1. Depois de criar a definição, deve aparecer no __separador Definições de Aplicação.__ Verifique se **ADT_SERVICE_URL** aparece na lista. Em seguida, guarde a nova definição de aplicação selecionando __Guardar__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Screenshot do portal Azure. No separador de definições de aplicação, destaca-se a nova definição A D T SERVICE U R L. O botão Guardar também está destacado.":::

1. Quaisquer alterações nas definições de aplicação requerem um reinício da aplicação, por isso __selecione Continue__ a reiniciar a sua aplicação quando solicitado.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Screenshot do portal Azure. Uma nota indica que quaisquer alterações às definições da aplicação reiniciarão a sua aplicação. O botão Continuar é realçado.":::

---

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma aplicação de função em Azure para utilização com a Azure Digital Twins. Em seguida, veja como basear-se na sua função básica para [ingerir dados do IoT Hub em Azure Digital Twins](how-to-ingest-iot-hub-data.md).
