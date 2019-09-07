---
title: Criar uma função do Azure com Java e IntelliJ | Microsoft Docs
description: Saiba como criar e publicar um aplicativo sem servidor simples disparado por HTTP no Azure com Java e IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure functions, funções, processamento de eventos, computação, arquitetura sem servidor, Java
ms.service: azure-functions
ms.topic: conceptual
ms.devlang: java
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 5aa0763206289305e98960b6e03ad903eb4547ae
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70744001"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Criar sua primeira função do Azure com Java e IntelliJ

Este artigo mostra:
- Como criar um projeto de função sem [servidor](https://azure.microsoft.com/overview/serverless-computing/) com o IntelliJ IDEA e o Apache Maven
- Etapas para testar e depurar a função no ambiente de desenvolvimento integrado (IDE) em seu próprio computador
- Instruções para implantar o projeto de função no Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com Java e IntelliJ, instale o seguinte software:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), versão 8
- [Apache Maven](https://maven.apache.org), versão 3,0 ou superior
- [INTELLIJ Idea](https://www.jetbrains.com/idea/download), Comunidade ou versões finais com Maven
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir as etapas neste artigo.

 Recomendamos que você instale o [Azure Functions Core Tools, versão 2](functions-run-local.md#v2). Ele fornece um ambiente de desenvolvimento local para gravação, execução e depuração Azure Functions.

## <a name="create-a-functions-project"></a>Criar um projeto do Functions

1. Em IntelliJ IDEA, selecione **criar novo projeto**.  
1. Na janela **novo projeto** , selecione **Maven** no painel esquerdo.
1. Marque a caixa de seleção **criar do arquétipo** e, em seguida, selecione **Adicionar arquétipo** para o [Azure-Functions-arquétipo](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Na janela **Adicionar arquétipo** , preencha os campos da seguinte maneira:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Versão_: Use a versão mais recente **1,22** do [repositório](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![central criar um projeto Maven do arquétipo em IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Selecione **OK**e, em seguida, selecione **Avançar**.
1. Insira seus detalhes para o projeto atual e selecione **concluir**.

O Maven cria os arquivos de projeto em uma nova pasta com o mesmo nome que o valor de _artefatoid_ . O código gerado do projeto é uma função simples [disparada por http](/azure/azure-functions/functions-bindings-http-webhook) que retorna o corpo da solicitação HTTP de disparo.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> Para executar e depurar funções localmente, verifique se você instalou o [Azure Functions Core Tools, versão 2](functions-run-local.md#v2).

1. Importe alterações manualmente ou habilite a [importação automática](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Abra a barra de ferramentas **projetos do Maven** .
1. Expanda **ciclo de vida**e abra **pacote**. A solução é compilada e empacotada em um diretório de destino criado recentemente.
1. Expanda **plug-ins** > **Azure-Functions** e abra **Azure-Functions: Run** para iniciar o Azure Functions tempo de execução local.  
  ![Barra de ferramentas do Maven para Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Feche a caixa de diálogo Executar quando terminar de testar sua função. Somente um host de função pode estar ativo e em execução localmente por vez.

## <a name="debug-the-function-in-intellij"></a>Depurar a função em IntelliJ

1. Para iniciar o host de função no modo de depuração, adicione **-DenableDebug** como o argumento ao executar a função. Você pode alterar a configuração em [metas do Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou executar o seguinte comando em uma janela de terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Esse comando faz com que o host de função abra uma porta de depuração em 5005.

1. No menu **executar** , selecione **Editar configurações**.
1. Selecione **(+)** para adicionar um **remoto**.
1. Preencha os campos _nome_ e _configurações_ e, em seguida, selecione **OK** para salvar a configuração.
1. Após a instalação, selecione **depurar < nome da configuração remota >** ou pressione Shift + F9 no teclado para iniciar a depuração.

   ![Funções de depuração no IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.png)

1. Quando tiver terminado, pare o depurador e o processo em execução. Somente um host de função pode estar ativo e em execução localmente por vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

1. Para poder implantar sua função no Azure, você deve [entrar usando o CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implante seu código em uma nova função usando o `azure-functions:deploy` destino Maven. Você também pode selecionar a opção **Azure-Functions: Deploy** na janela projetos Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Localize a URL para sua função na saída CLI do Azure depois que a função tiver sido implantada com êxito.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passos seguintes

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicione funções adicionais com diferentes gatilhos ao seu projeto usando o `azure-functions:add` destino Maven.
