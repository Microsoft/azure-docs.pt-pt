---
title: Use Java e Maven para publicar uma função no Azure
description: Crie e publique uma função desencadeada pelo HTTP para Azure com Java e Maven.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: f226736050319d57cd0bc123fdb2211e0faeae11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208851"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Quickstart: Use Java e Maven para criar e publicar uma função no Azure

Este artigo mostra-lhe como construir e publicar uma função Java para funções Azure com a ferramenta de linha de comando Maven. Quando terminar, o seu código de função funciona em Azure num plano de [hospedagem sem servidores](functions-scale.md#consumption-plan) e é desencadeado por um pedido http.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções utilizando Java, deve ter o seguinte instalado:

- [Kit de Desenvolvimento Java](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [CLI do Azure]
- [Funções Azure Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior

Também precisa de uma subscrição azure ativa. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


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
> Se está a ter problemas com o comando, veja qual é a versão `maven-archetype-plugin` usada. Como está a executar o comando num diretório vazio sem `.pom` ficheiro, pode estar a tentar utilizar um plugin da versão mais antiga de `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` se atualizou o seu Maven a partir de uma versão mais antiga. Em caso afirmativo, tente apagar o diretório `maven-archetype-plugin` e reexecutar o comando.

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

Maven pede-lhe valores necessários para terminar gerando o projeto na implantação. Forneça os seguintes valores quando solicitado:

| Valor | Descrição |
| ----- | ----------- |
| **groupId** | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as regras de [nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) do pacote para a Java. Os exemplos desta utilização rápida `com.fabrikam.functions`. |
| **artefactoId** | Um valor que é o nome do jarro, sem um número de versão. Os exemplos desta utilização rápida `fabrikam-functions`. |
| **Versão** | Escolha o valor padrão de `1.0-SNAPSHOT`. |
| **pacote** | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. Os exemplos desta utilização rápida `com.fabrikam.functions`. |
| **nome de app** | Nome globalmente único que identifica a sua nova aplicação de funções no Azure. Utilize o padrão, que é o _artefactoId_ anexado com um número aleatório. Tome nota deste valor, vai precisar mais tarde. |
| **appRegi** | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. A predefinição é `westus`. Executar este comando [CLI do Azure] para obter uma lista de todas as regiões:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **recursosGroup** | Nome para o novo [grupo de recursos](../azure-resource-manager/management/overview.md) para criar a sua aplicação de função. Utilize `myResourceGroup`, que é utilizado por exemplos neste arranque rápido. Um grupo de recursos deve ser exclusivo da sua subscrição Azure.|

Digite `Y` ou prima Introduza para confirmar.

A Maven cria os ficheiros do projeto numa nova pasta com um nome de _artefactoId_, que neste exemplo é `fabrikam-functions`. 

Abra o novo ficheiro Function.java a partir do caminho *src/main/java* num editor de texto e reveja o código gerado. Este código é uma função desencadeada em [HTTP](functions-bindings-http-webhook.md) que ecoa o corpo do pedido. 

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Executar localmente a função

Executar o seguinte comando, que muda o diretório para a pasta do projeto recém-criada, em seguida, constrói e executa o projeto de função:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Você vê saída como o seguinte das Ferramentas Core funções azure quando executa o projeto localmente:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Desencadear a função a partir da linha de comando utilizando cURL numa nova janela de terminais:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
A chave de [função](functions-bindings-http-webhook-trigger.md#authorization-keys) não é necessária quando se corre localmente. Utilize `Ctrl+C` no terminal para parar o código de função.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

Uma aplicação de função e recursos relacionados são criados no Azure quando você implementa a sua aplicação de funções pela primeira vez. Antes de ser implementado, utilize o comando [az login](/cli/azure/authenticate-azure-cli) Azure CLI para iniciar sessão na sua subscrição Azure. 

```azurecli
az login
```

> [!TIP]
> Se a sua conta conseguir aceder a várias subscrições, utilize o [conjunto de conta Az](/cli/azure/account#az-account-set) para definir a subscrição predefinida para esta sessão. 

Utilize o seguinte comando Maven para implementar o seu projeto numa nova aplicação de funções. 

```azurecli
mvn azure-functions:deploy
```

Este `azure-functions:deploy` alvo Maven cria os seguintes recursos em Azure:

+ Grupo de recursos. Nomeado com o _recursoGroup_ que forneceu.
+ Conta de armazenamento. Exigido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de serviço de aplicativos. Hospedagem sem servidor estoque para a sua aplicação de funções na _aplicação_especificada Region . O nome é gerado aleatoriamente.
+ App de funções. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é o seu nome de _aplicação,_ anexado com um número gerado aleatoriamente. 

A implementação também embala os ficheiros do projeto e os implanta na nova aplicação de funções utilizando [a implementação](functions-deployment-technologies.md#zip-deploy)de zip , com o modo de execução do pacote ativado.

Após a implementação estar concluída, vê o URL que pode utilizar para aceder aos pontos finais da sua aplicação de função. Como o gatilho HTTP que publicámos utiliza `authLevel = AuthorizationLevel.FUNCTION`, é necessário obter a chave de função para chamar o ponto final da função em HTTP. A maneira mais fácil de obter a chave de função é a partir do [Portal do Azure].

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obtenha o URL do gatilho HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Pode obter o URL necessário para acionar a sua função, com a chave de função, a partir do portal Azure. 

1. Navegue no [Portal do Azure]inscreva-se, escreva o nome da _sua_ aplicação de função em **Search** no topo da página e prima o enter.
 
1. Na sua aplicação de funções, expandir **funções (Ler Apenas)** , escolha a sua função e, em **seguida, selecione <> Obtenha URL de função** na parte superior direita. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Escolha **o predefinido (Chave de Função)** e selecione **Copiar**. 

Agora pode utilizar o URL copiado para aceder à sua função.

## <a name="verify-the-function-in-azure"></a>Verifique a função em Azure

Para verificar a aplicação de funções em funcionamento no Azure utilizando `cURL`, substitua o URL da amostra abaixo com o URL que copiou do portal.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Isto envia um pedido de CORREIO para o ponto final da função com `AzureFunctions` no corpo do pedido. Vê a seguinte resposta.

```Output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Passos seguintes

Criou um projeto de funções Java com uma função ativada http, executa-o na sua máquina local e implantou-o para o Azure. Agora, prolongue a sua função por...

> [!div class="nextstepaction"]
> [Adicionar uma ligação de saída de fila de armazenamento Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Portal do Azure]: https://portal.azure.com
