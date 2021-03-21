---
title: Crie uma app de função Azure com Java e Eclipse
description: Como guiar para criar e publicar uma simples aplicação http sem servidor usando Java e Eclipse para Funções Azure.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 7dd881d130b9df19335ac64be501553af99d58d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179549"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Crie a sua primeira função com Java e Eclipse 

Este artigo mostra-lhe como criar um projeto de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) com o Eclipse IDE e Apache Maven, testá-lo e depurá-lo, em seguida, implantá-lo para as Funções Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma aplicação de funções com Java e Eclipse, tem de ter o seguinte instalado:

-  [Java Developer Kit,](https://www.azul.com/downloads/zulu/)versão 8.
-  [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.
-  [Eclipse,](https://www.eclipse.org/downloads/packages/)com apoio de Java e Maven.
-  [CLI do Azure](/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

É altamente recomendado também instalar [ferramentas core de funções Azure, versão 2](functions-run-local.md#v2), que fornecem um ambiente local para executar e depurar funções Azure. 

## <a name="create-a-functions-project"></a>Criar um projeto de Funções

1. No Eclipse, selecione o menu **'Ficheiro'** e, em seguida, selecione **New - &gt; Maven Project**. 
1. Aceite os predefinidos no diálogo do **Projeto New Maven** e selecione **Next**.
1. Encontre e selecione o [arquétipo de funções azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) e clique em **Seguinte**.
1. Certifique-se de preencher valores para todos os `resourceGroup` campos, `appName` incluindo, e `appRegion` (por favor, use uma app diferenteName diferente do **fabrikam-function-20170920120101928),** e, eventualmente, **Termine.**
    ![Eclipse Maven criar2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

O Maven cria os ficheiros de projeto numa nova pasta com um nome de _artifactId_. O código gerado no projeto é uma função simples DETON que [ecoa](./functions-bindings-http-webhook.md) o corpo do pedido HTTP de desencadeamento.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [Azure Functions Core Tools, a versão 2](functions-run-local.md#v2) deve ser instalada para executar e depurar funções localmente.

1. Clique com o botão direito no projeto gerado e, em seguida, escolha **Run As** e **Maven build**.
1. No diálogo de configuração de **edição,** introduza `package` nos campos **Objetivos** e **Nomes** e, em seguida, selecione **Executar**. Isto irá construir e embalar o código de função.
1. Uma vez concluída a construção, crie outra configuração Run como acima, utilizando `azure-functions:run` como objetivo e nome. Selecione **Executar** para executar a função no IDE.

Termine o tempo de funcionamento na janela da consola quando terminar de testar a sua função. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

### <a name="debug-the-function-in-eclipse"></a>Depurar a função em Eclipse

Na configuração **run As** configurada no passo anterior, altere `azure-functions:run` e `azure-functions:run -DenableDebug` execute a configuração atualizada para iniciar a aplicação de função no modo depurado.

Selecione o menu **'Executar'** e abra **as configurações do Debug**. Escolha **a Aplicação Java Remota** e crie uma nova. Dê à sua configuração um nome e preencha as definições. A porta deve ser consistente com a porta de depuração aberta pelo hospedeiro de função, que por defeito é `5005` . Após a configuração, clique `Debug` para começar a depurar.

![Funções de depurar em Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Desaponte os pontos de rutura e inspecione os objetos na sua função utilizando o IDE. Quando terminar, pare o depurar e o anfitrião da função de funcionamento. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Inicie sessão com o CLI Azure](/cli/azure/authenticate-azure-cli) antes de continuar a utilizar o pedido de comando do computador.

```azurecli
az login
```

Implemente o seu código numa nova aplicação Function utilizando o `azure-functions:deploy` objetivo Maven numa nova configuração **Run As.**

Quando a implementação estiver concluída, verá o URL que pode utilizar para aceder à sua aplicação de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Passos seguintes

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicione mais funções com diferentes acionadores ao seu projeto com o destino do Maven `azure-functions:add`.
