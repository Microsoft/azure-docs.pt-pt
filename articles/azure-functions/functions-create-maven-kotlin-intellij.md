---
title: Criar uma função Azure com Kotlin e IntelliJ
description: Aprenda a criar e publicar uma simples aplicação http-triggered, sem servidor no Azure com Kotlin e IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 09dd868dc9e05241943899654d7c8bb427a8f268
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104840"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Quickstart: Crie a sua primeira função http desencadeada com Kotlin e IntelliJ

Este artigo mostra-lhe como criar um projeto de função [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) com IntelliJ IDEA e Apache Maven. Também mostra como depurar localmente o seu código de função no ambiente de desenvolvimento integrado (IDE) e, em seguida, implementar o projeto de função para Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com a Kotlin e a IntelliJ, instale o seguinte software:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), versão 8
- [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Versões Comunitárias ou Ultimate com Maven
- [CLI do Azure](/cli/azure)
- [Versão 2.x](functions-run-local.md#v2) das Ferramentas Centrais Azure Functions. Proporciona um ambiente de desenvolvimento local para escrever, executar e depurar funções de Azure.

> [!IMPORTANT]
> A variável ambiente JAVA_HOME deve ser definida para a localização de instalação do JDK para completar os passos deste artigo.

## <a name="create-a-functions-project"></a>Criar um projeto de Funções

1. Intellij IDEA, selecione **Create New Project**.  
1. Na janela **New Project,** selecione **Maven** a partir do painel esquerdo.
1. Selecione a Configuração a partir da caixa de verificação **do arquétipo** e, em seguida, selecione **Adicionar Arquétipo** para o [arquétipo azure-funções-kotlin-arquétipo](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Na janela **Add Archetype,** complete os campos da seguinte forma:
    - _GroupId_: com.microsoft.azure
    - _ArtefactoId_: azure-funções-kotlin-arquétipo
    - _Versão_: Utilize a versão mais recente [do repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ Criar um projeto Maven a partir de arquétipo em IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecione **OK**e, em seguida, selecione **Seguinte**.
1. Insira os seus dados para o projeto atual e **selecione Terminar**.

A Maven cria os ficheiros do projeto numa nova pasta com o mesmo nome que o valor _ArtifactId._ O código gerado do projeto é uma função simples desencadeada por HTTP que [ecoa](./functions-bindings-http-webhook.md) o corpo do pedido HTTP de desencadeamento.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> Para executar e depurar funções localmente, certifique-se de que instalou [ferramentas principais do Azure Functions, versão 2](functions-run-local.md#v2).

1. Importar alterações manualmente ou permitir [a importação automática.](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)
1. Abra a barra de ferramentas **Maven Projects.**
1. Expandir **o Ciclo de Vida**e, em seguida, abrir o **pacote.** A solução é construída e embalada num diretório-alvo recém-criado.
1. Expanda **as**  >  **funções de azul dos Plugins** e abra **as funções azul:executar** para iniciar o tempo de funcionamento local das Funções Azure.  
  ![Barra de ferramentas Maven para Funções Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Feche a caixa de diálogo de execução quando terminar de testar a sua função. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

## <a name="debug-the-function-in-intellij"></a>Depurar a função em IntelliJ

1. Para iniciar o anfitrião da função no modo de depurar, adicione **-DenableDebug** como argumento quando executar a sua função. Pode alterar a configuração em [objetivos maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou executar o seguinte comando numa janela terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Este comando faz com que o anfitrião da função abra uma porta de depurado a 5005.

1. No menu **'Executar',** **selecione Configurações de Edição**.
1. Selecione **(+)** para adicionar um **Remote**.
1. Preencha os campos _Nome_ e _Definições_ e, em seguida, selecione **OK** para guardar a configuração.
1. Após a configuração, selecione **Debug < Nome de configuração remota >** ou prima Shift+F9 no seu teclado para começar a depurar.

   ![Debug funciona em IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Quando terminar, pare o depurar e o processo de corrida. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

1. Antes de poder implantar a sua função no Azure, tem de [iniciar sessão utilizando o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Insiure o seu código numa nova função utilizando o `azure-functions:deploy` alvo Maven. Também pode selecionar as **funções azure:implementar** na janela Maven Projects.

   ```
   mvn azure-functions:deploy
   ```

1. Encontre o URL para a sua função na saída Azure CLI depois de a função ter sido implantada com sucesso.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a sua primeira função Kotlin para a Azure, reveja o [guia de desenvolvimento de Funções de Java](functions-reference-java.md) para obter mais informações sobre o desenvolvimento das funções de Java e Kotlin.
- Adicione funções adicionais com diferentes gatilhos ao seu projeto utilizando o `azure-functions:add` alvo Maven.