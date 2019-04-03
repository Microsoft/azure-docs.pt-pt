---
title: Criar uma função do Azure com Java e IntelliJ | Documentos da Microsoft
description: Saiba como criar e publicar uma aplicação simples acionada por HTTP, sem servidor no Azure com Java e IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure funções, funções, processamento de eventos, computação, arquitetura sem servidor, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877811"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Criar a sua primeira função do Azure com Java e o IntelliJ

Este artigo mostra-lhe:
- Como criar uma [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) projeto de função com o IntelliJ IDEA e o Apache Maven
- Passos para testar e depurar a função no ambiente de desenvolvimento integrado (IDE) no seu computador
- Instruções para implementar o projeto de função para as funções do Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com o Java e IntelliJ, instale o seguinte software:

- [Java Development Kit](https://www.azul.com/downloads/zulu/) (JDK), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versões da Comunidade ou Ultimate com o Maven
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir os passos neste artigo.

 Recomendamos que instale [ferramentas de núcleo de funções do Azure, versão 2](functions-run-local.md#v2). Ele fornece um ambiente de desenvolvimento local para escrever, executar e depurar funções do Azure.

## <a name="create-a-functions-project"></a>Criar um projeto de funções

1. No IntelliJ IDEA, selecione **criar novo projeto**.  
1. Na **novo projeto** janela, selecione **Maven** no painel à esquerda.
1. Selecione o **criar do mvn** caixa de verificação e, em seguida, selecione **adicionar Mvn** para o [azure-funções-mvn](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Na **Mvn adicionar** janela, preencha os campos da seguinte forma:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Versão_: Utilizar a versão mais recente a partir [o repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![criar um projeto Maven a partir de mvn no IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Selecione **OK**e, em seguida, selecione **próxima**.
1. Introduza os detalhes do seu projeto atual e selecione **concluir**.

Maven cria os arquivos do projeto numa nova pasta com o mesmo nome que o _ArtifactId_ valor. Código gerado do projeto é um simples [acionada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) função que ecoa o corpo da solicitação HTTP acionadora.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> Para executar e depurar funções localmente, certifique-se de que instalou [ferramentas de núcleo de funções do Azure, versão 2](functions-run-local.md#v2).

1. Importar as alterações manualmente ou ativar [automaticamente importação](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Abra o **projetos do Maven** barra de ferramentas.
1. Expanda **ciclo de vida**e, em seguida, abra **pacote**. A solução é criada e empacotada num diretório de destino recentemente criado.
1. Expanda **plug-ins** > **funções do azure** e abra **azure-funções: run** para iniciar o tempo de execução local do funções do Azure.  
  ![Barra de ferramentas do maven para as funções do Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Fechar a caixa de diálogo de execução quando tiver terminado a testar a sua função. Anfitrião de apenas uma função pode ser ativa e em execução localmente ao mesmo tempo.

## <a name="debug-the-function-in-intellij"></a>Depurar a função no IntelliJ

1. Para iniciar o host de função no modo de depuração, adicione **- DenableDebug** como argumento ao executar a sua função. Pode alterar a configuração no [objetivos de maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou execute o seguinte comando numa janela do terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Este comando faz com que o anfitrião de função abrir uma porta de depuração em 5005.

1. Sobre o **execute** menu, selecione **editar configurações**.
1. Selecione **(+)** para adicionar um **remoto**.
1. Concluir o _Name_ e _definições_ campos e, em seguida, selecione **OK** para guardar a configuração.
1. Após a configuração, selecione **depurar < nome da configuração remota >** ou prima Shift + F9 no teclado para iniciar a depuração.

   ![Depurar funções no IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Quando tiver terminado, pare o depurador e o processo em execução. Anfitrião de apenas uma função pode ser ativa e em execução localmente ao mesmo tempo.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

1. Antes de poder implementar a sua função para o Azure, tem [inicie sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implementar o seu código numa nova função utilizando o `azure-functions:deploy` destino do Maven. Também pode selecionar o **funções do azure: implementar** opção na janela de projetos do Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Encontre o URL para a função na saída da CLI do Azure após a função ter sido implementada com êxito.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passos Seguintes

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicionar funções adicionais com diferentes acionadores ao seu projeto com o `azure-functions:add` destino do Maven.
