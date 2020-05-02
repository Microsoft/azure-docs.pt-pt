---
title: Criar uma função Azure com Kotlin e IntelliJ
description: Aprenda a criar e publicar uma simples aplicação em Http-triggered, sem servidores no Azure com Kotlin e IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80674098"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Quickstart: Crie a sua primeira função em HTTP desencadeada com a Kotlin e a IntelliJ

Este artigo mostra-lhe como criar um projeto de função [sem servidores](https://azure.microsoft.com/overview/serverless-computing/) com intelliJ IDEA e Apache Maven. Também mostra como depurar localmente o seu código de função no ambiente de desenvolvimento integrado (IDE) e, em seguida, implementar o projeto de função para O Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com a Kotlin e a IntelliJ, instale o seguinte software:

- [Kit de Desenvolvimento Java](https://aka.ms/azure-jdks) (JDK), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Versões Comunitárias ou Ultimate com Maven
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [Versão 2.x](functions-run-local.md#v2) das Ferramentas Core funções do Azure. Proporciona um ambiente de desenvolvimento local para a escrita, execução e depuração das funções azure.

> [!IMPORTANT]
> A variável ambiente JAVA_HOME deve ser definida para a localização de instalação do JDK para completar as etapas deste artigo.

## <a name="create-a-functions-project"></a>Criar um projeto funções

1. No IntelliJ IDEA, selecione **Create New Project**.  
1. Na janela **new project,** selecione **Maven** do painel esquerdo.
1. Selecione o Create a partir da caixa de verificação **do arquétipo** e, em seguida, selecione **Adicionar Arquétipo** para o [arquétipo de funções azuis-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Na janela **Add Archetype,** complete os campos da seguinte forma:
    - _GroupId_: com.microsoft.azure
    - _ArtefactoId_: azure-functions-kotlin-archetype
    - _Versão_: Use a versão mais recente [do repositório](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![central Criar um projeto Maven a partir do arquétipo em IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecione **OK**, e, em seguida, selecione **Next**.
1. Insira os seus dados para o projeto atual e selecione **Terminar**.

A Maven cria os ficheiros do projeto numa nova pasta com o mesmo nome do valor _ArtifactId._ O código gerado do projeto é uma função simples [desencadeada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) que ecoa o corpo do pedido HTTP desencadeado.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> Para executar e depurar funções localmente, certifique-se de que instalou [ferramentas core funções do Azure, versão 2](functions-run-local.md#v2).

1. A importação muda manualmente ou permite a [importação de automóveis.](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)
1. Abra a barra de **ferramentas maven Projects.**
1. Expandir o Ciclo de **Vida,** e depois abrir **o pacote.** A solução é construída e embalada num diretório alvo recém-criado.
1. Expandir > **as funções de** **plugins**em azul e **funções azure abertas:executar** para iniciar o tempo de funcionamento local das Funções Azure.  
  ![Barra de ferramentas Maven para funções azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Feche a caixa de diálogo de execução quando terminar de testar a sua função. Apenas um hospedeiro de funções pode estar ativo e a funcionar localmente de cada vez.

## <a name="debug-the-function-in-intellij"></a>Desinvejo a função no IntelliJ

1. Para iniciar o hospedeiro de funções no modo de depuração, adicione **-DenableDebug** como argumento quando executar a sua função. Pode alterar a configuração em [objetivos maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou executar o seguinte comando numa janela terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Este comando faz com que o hospedeiro de funções abra uma porta de depuração a 5005.

1. No menu **Executar,** selecione **Configurações de Edição**.
1. **Selecione (+)** para adicionar um **Controlo Remoto**.
1. Complete os campos _nome_ e _definições_ e, em seguida, selecione **OK** para salvar a configuração.
1. Depois da configuração, selecione Debug < Nome de **Configuração Remota >** ou prima Shift+F9 no teclado para começar a depurar.

   ![Funções de depuração no IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Quando terminar, pare o desbugger e o processo de execução. Apenas um hospedeiro de funções pode estar ativo e a funcionar localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

1. Antes de poder implementar a sua função para o Azure, tem de [iniciar sessão utilizando o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implemente o seu código numa `azure-functions:deploy` nova função utilizando o alvo Maven. Também pode selecionar as **funções azure:implementar** a opção na janela Maven Projects.

   ```
   mvn azure-functions:deploy
   ```

1. Encontre o URL para a sua função na saída Azure CLI depois de a função ter sido implementada com sucesso.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a sua primeira função Kotlin para o Azure, reveja o guia de desenvolvimento de [Funções Java](functions-reference-java.md) para obter mais informações sobre o desenvolvimento das funções java e Kotlin.
- Adicione funções adicionais com diferentes gatilhos ao seu projeto utilizando o `azure-functions:add` alvo Maven.
