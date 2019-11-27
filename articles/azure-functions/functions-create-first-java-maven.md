---
title: Usar Java e Maven para publicar uma função no Azure
description: Crie e publique uma função disparada por HTTP no Azure com Java e Maven.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cb43f558a5c983a8a4cc3823b278b75cb8cde78d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230750"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Início rápido: usar Java e Maven para criar e publicar uma função no Azure

Este artigo mostra como criar e publicar uma função Java para Azure Functions com a ferramenta de linha de comando do Maven. Quando você terminar, o código de função será executado no Azure em um [plano de hospedagem sem servidor](functions-scale.md#consumption-plan) e será disparado por uma solicitação HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3,0 ou superior
- [CLI do Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior
- Uma subscrição do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto das Funções

Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Se você estiver tendo problemas com a execução do comando, dê uma olhada no que `maven-archetype-plugin` versão é usada. Como você está executando o comando em um diretório vazio sem `.pom` arquivo, ele pode estar tentando usar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` se você tiver atualizado seu Maven de uma versão mais antiga. Nesse caso, tente excluir o diretório `maven-archetype-plugin` e executar novamente o comando.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

O Maven solicita os valores necessários para concluir a geração do projeto na implantação. Forneça os seguintes valores quando solicitado:

| Valor | Descrição |
| ----- | ----------- |
| **groupId** | Um valor que identifica exclusivamente o projeto em todos os projetos, seguindo as [regras de nomenclatura de pacote](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) para Java. Os exemplos neste guia de início rápido usam `com.fabrikam.functions`. |
| **artifactId** | Um valor que é o nome do jar, sem um número de versão. Os exemplos neste guia de início rápido usam `fabrikam-functions`. |
| **Versão** | Escolha o valor padrão de `1.0-SNAPSHOT`. |
| **agrupa** | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. Os exemplos neste guia de início rápido usam `com.fabrikam.functions`. |
| **appName** | Nome global exclusivo que identifica seu novo aplicativo de funções no Azure. Use o padrão, que é o _artefatoid_ acrescentado com um número aleatório. Anote esse valor, você precisará dele mais tarde. |
| **appRegion** | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. A predefinição é `westus`. Execute este [CLI do Azure] comando para obter uma lista de todas as regiões:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Nome do novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) no qual criar seu aplicativo de funções. Use `myResourceGroup`, que é usado por exemplos neste guia de início rápido. Um grupo de recursos deve ser exclusivo para sua assinatura do Azure.|

Digite `Y` ou pressione ENTER para confirmar.

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artefatoid_, que neste exemplo é `fabrikam-functions`. 

Abra o novo arquivo function. java do caminho *src/main/java* em um editor de texto e examine o código gerado. Esse código é uma função [disparada por http](functions-bindings-http-webhook.md) que retorna o corpo da solicitação. 

## <a name="run-the-function-locally"></a>Executar localmente a função

Execute o comando a seguir, que altera o diretório para a pasta de projeto recém-criada e, em seguida, compila e executa o projeto de função:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Você verá uma saída semelhante à seguinte de Azure Functions Core Tools quando você executar o projeto localmente:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Dispare a função na linha de comando usando a ondulação em uma nova janela de terminal:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
A [chave de função](functions-bindings-http-webhook.md#authorization-keys) não é necessária ao executar localmente. Utilize `Ctrl+C` no terminal para parar o código de função.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

Um aplicativo de funções e recursos relacionados são criados no Azure quando você implanta o aplicativo de funções pela primeira vez. Antes de implantar o, use o comando [AZ login](/cli/azure/authenticate-azure-cli) CLI do Azure para entrar em sua assinatura do Azure. 

```azurecli
az login
```

> [!TIP]
> Se sua conta puder acessar várias assinaturas, use [AZ Account Set](/cli/azure/account#az-account-set) para definir a assinatura padrão para esta sessão. 

Use o seguinte comando do Maven para implantar seu projeto em um novo aplicativo de funções. 

```azurecli
mvn azure-functions:deploy
```

Esse `azure-functions:deploy` destino Maven cria os seguintes recursos no Azure:

+ Grupo de recursos. Chamado com o _resourcegroup_ que você forneceu.
+ conta de armazenamento. Exigido por funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano do serviço de aplicativo. Hospedagem sem servidor para seu aplicativo de funções no _appRegion_especificado. O nome é gerado aleatoriamente.
+ Aplicativo de funções. Um aplicativo de funções é a unidade de implantação e execução para suas funções. O nome é seu _AppName_, acrescentado com um número gerado aleatoriamente. 

A implantação também empacota os arquivos de projeto e os implanta no novo aplicativo de funções usando a [implantação de zip](functions-deployment-technologies.md#zip-deploy), com o modo de execução do pacote habilitado.

Após a conclusão da implantação, você verá a URL que pode usar para acessar os pontos de extremidade do aplicativo de funções. Como o gatilho HTTP que publicamos usa `authLevel = AuthorizationLevel.FUNCTION`, você precisa obter a chave de função para chamar o ponto de extremidade da função por HTTP. A maneira mais fácil de obter a chave de função é a partir da [portal do Azure].

## <a name="get-the-http-trigger-url"></a>Obter a URL do gatilho HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Você pode obter a URL necessária para disparar sua função, com a tecla de função, da portal do Azure. 

1. Navegue até o [portal do Azure], entre, digite o _AppName_ do seu aplicativo de funções em **Pesquisar** na parte superior da página e pressione Enter.
 
1. Em seu aplicativo de funções, expanda **funções (somente leitura)** , escolha sua função e, em seguida, selecione **</> obter URL da função** no canto superior direito. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Escolha **padrão (chave de função)** e selecione **copiar**. 

Agora você pode usar a URL copiada para acessar sua função.

## <a name="verify-the-function-in-azure"></a>Verificar a função no Azure

Para verificar se o aplicativo de funções em execução no Azure está usando `cURL`, substitua a URL do exemplo abaixo pela URL que você copiou do Portal.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Isso envia uma solicitação POST para o ponto de extremidade da função com `AzureFunctions` no corpo da solicitação. Você verá a resposta a seguir.

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Passos seguintes

Você criou um projeto de funções Java com uma função disparada por HTTP, a executa em seu computador local e a implantou no Azure. Agora, estenda sua função por...

> [!div class="nextstepaction"]
> [Adicionando uma associação de saída da fila de armazenamento do Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Portal do Azure]: https://portal.azure.com