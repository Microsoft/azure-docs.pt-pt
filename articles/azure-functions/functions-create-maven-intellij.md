---
title: Criar uma função Azure com Java e IntelliJ
description: Aprenda a criar e publicar uma simples aplicação em Http-triggered, sem servidores no Azure com Java e IntelliJ.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7003dc19a7bfc405809de91534028aba8e0416c5
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136855"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Crie a sua primeira função Azure com Java e IntelliJ

Este artigo mostra-lhe:
- Como criar um projeto de função [sem servidores](https://azure.microsoft.com/overview/serverless-computing/) com IntelliJ IDEA e Apache Maven
- Passos para testar e depurar a função no ambiente de desenvolvimento integrado (IDE) no seu próprio computador
- Instruções para a implementação do projeto de função para funções azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com a Java e a IntelliJ, instale o seguinte software:

- [Kit de Desenvolvimento Java](https://www.azul.com/downloads/zulu/) (JDK), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Versões Comunitárias ou Ultimate com Maven
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A variável ambiente JAVA_HOME deve ser definida para a localização de instalação do JDK para completar as etapas deste artigo.

 Recomendamos que [instale ferramentas centrais de funções Azure, versão 2](functions-run-local.md#v2). Proporciona um ambiente de desenvolvimento local para a escrita, execução e depuração das funções azure.

## <a name="create-a-functions-project"></a>Criar um projeto funções

1. No IntelliJ IDEA, selecione **Create New Project**.  
1. Na janela **new project,** selecione **Maven** do painel esquerdo.
1. Selecione o Create a partir da caixa de verificação **do arquétipo** e, em seguida, selecione **Adicionar Arquétipo** para o [arquétipo de funções azuis](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Na janela **Add Archetype,** complete os campos da seguinte forma:
    - _GroupId_: com.microsoft.azure
    - _ArtefactoId_: azure-functions-archetype
    - _Versão_: Verifique e utilize a versão mais recente [do repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Criar um projeto Maven a partir do arquétipo no IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Selecione **OK**, e, em seguida, selecione **Next**.
1. Insira os seus dados para o projeto atual e selecione **Terminar**.

A Maven cria os ficheiros do projeto numa nova pasta com o mesmo nome do valor _ArtifactId._ O código gerado do projeto é uma função simples [desencadeada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) que ecoa o corpo do pedido HTTP desencadeado.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> Para executar e depurar funções localmente, certifique-se de que instalou [ferramentas core funções do Azure, versão 2](functions-run-local.md#v2).

1. A importação muda manualmente ou permite a [importação de automóveis.](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)
1. Abra a barra de **ferramentas maven Projects.**
1. Expandir o Ciclo de **Vida,** e depois abrir **o pacote.** A solução é construída e embalada num diretório alvo recém-criado.
1. Expandir **os Plugins** > **funções azure** e **funções azure abertas:executar** para iniciar o tempo de funcionamento local das Funções Azure.  
  ![barra de ferramentas Maven para](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png) de Funções Azure  

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
1. Depois da configuração, selecione **Debug < Nome de Configuração Remota >** ou prima Shift+F9 no teclado para começar a depurar.

1. Quando terminar, pare o desbugger e o processo de execução. Apenas um hospedeiro de funções pode estar ativo e a funcionar localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

1. Antes de poder implementar a sua função para o Azure, deve [iniciar sessão utilizando o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implemente o seu código numa nova função utilizando o alvo `azure-functions:deploy` Maven. Também pode selecionar as **funções azure:implementar** a opção na janela Maven Projects.

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

## <a name="next-steps"></a>Passos Seguintes

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicione funções adicionais com diferentes gatilhos ao seu projeto utilizando o `azure-functions:add` alvo Maven.
