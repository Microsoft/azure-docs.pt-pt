---
title: Criar um aplicativo de funções do Azure com Java e Eclipse
description: Guia de instruções para criar e publicar um aplicativo sem servidor simples disparado por HTTP usando Java e Eclipse para Azure Functions.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: d2d353e6ccd7dad7be302a5f40c65012f32deba7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227129"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Criar sua primeira função com Java e Eclipse 

Este artigo mostra como criar um projeto de função sem [servidor](https://azure.microsoft.com/solutions/serverless/) com o IDE do Eclipse e o Apache Maven, testá-lo e depurá-lo, em seguida, implantá-lo no Azure functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver um aplicativo do Functions com Java e Eclipse, você deve ter o seguinte instalado:

-  [Java Development Kit](https://www.azul.com/downloads/zulu/), versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), com suporte para Java e Maven.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

É altamente recomendável instalar também [Azure Functions Core Tools, versão 2](functions-run-local.md#v2), que fornece um ambiente local para executar e depurar Azure functions. 

## <a name="create-a-functions-project"></a>Criar um projeto do Functions

1. No Eclipse, selecione o menu **arquivo** e, em seguida, selecione **novo-&gt; projeto Maven**. 
1. Aceite os padrões na caixa de diálogo **novo projeto Maven** e selecione **Avançar**.
1. Selecione **Adicionar arquétipo** e adicione as entradas para o [Azure-Functions-arquétipoe](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - ID do grupo de arquétipo: com. Microsoft. Azure
    - ID do artefato de arquétipo: Azure-Functions-arquétipo
    - Versão: Use a versão mais recente **1,22** do [repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven criar](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Clique em **OK** e em **Avançar**.  Certifique-se de preencher valores para todos os campos, incluindo `resourceGroup`, `appName`e `appRegion` (use um appName diferente de **Fabrikam-function-20170920120101928**) e, eventualmente, **concluir**.
    ![Eclipse Maven create2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

O Maven cria os ficheiros de projeto numa nova pasta com um nome de _artifactId_. O código gerado no projeto é uma função [disparada por http](/azure/azure-functions/functions-bindings-http-webhook) simples que retorna o corpo da solicitação HTTP de disparo.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [Azure Functions Core Tools, a versão 2](functions-run-local.md#v2) deve ser instalada para executar e depurar funções localmente.

1. Clique com o botão direito do mouse no projeto gerado e escolha **Executar como** e **Build do Maven**.
1. Na caixa de diálogo **Editar configuração** , insira `package` nos campos **metas** e **nome** e, em seguida, selecione **executar**. Isso criará e empacotará o código de função.
1. Quando a compilação for concluída, crie outra configuração de execução como acima, usando `azure-functions:run` como a meta e o nome. Selecione **executar** para executar a função no IDE.

Encerre o tempo de execução na janela do console quando terminar de testar sua função. Somente um host de função pode estar ativo e em execução localmente por vez.

### <a name="debug-the-function-in-eclipse"></a>Depurar a função no Eclipse

Na configuração de **Executar como** configurada na etapa anterior, altere `azure-functions:run` para `azure-functions:run -DenableDebug` e execute a configuração atualizada para iniciar o aplicativo de funções no modo de depuração.

Selecione o menu **executar** e abra **configurações de depuração**. Escolha **aplicativo Java remoto** e crie um novo. Dê um nome à sua configuração e preencha as configurações. A porta deve ser consistente com a porta de depuração aberta pelo host de função, que por padrão é `5005`. Após a instalação, clique em `Debug` para iniciar a depuração.

![Funções de depuração no Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Defina pontos de interrupção e inspecione objetos em sua função usando o IDE. Quando terminar, pare o depurador e o host de função em execução. Somente um host de função pode estar ativo e em execução localmente por vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Faça logon com o CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar usando o prompt de comando do computador.

```azurecli
az login
```

Implante seu código em um novo aplicativo de funções usando a meta `azure-functions:deploy` Maven em uma nova configuração **de executar como** .

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
