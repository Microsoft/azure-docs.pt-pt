---
title: Criar uma função Kotlin em Funções Azure usando IntelliJ
description: Aprenda a usar o IntelliJ para criar uma função Kotlin acionada por HTTP simples, que publica para executar num ambiente sem servidor em Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 0207e4af9f845343866714ec207ca306cb327b36
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035177"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Crie a sua primeira função Kotlin em Azure usando IntelliJ

Este artigo mostra-lhe como criar uma função Java desencadeada por HTTP num projeto IntelliJ IDEA, executar e depurar o projeto no ambiente de desenvolvimento integrado (IDE), e finalmente implementar o projeto de função numa aplicação de função em Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para criar e publicar funções Kotlin para a Azure utilizando o IntelliJ, instale o seguinte software:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), versão 8
- [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Versões Comunitárias ou Ultimate com Maven
- [CLI do Azure](/cli/azure)
- [Versão 2.x](functions-run-local.md#v2) das Ferramentas Centrais Azure Functions. Proporciona um ambiente de desenvolvimento local para escrever, executar e depurar funções de Azure.

> [!IMPORTANT]
> A variável ambiente JAVA_HOME deve ser definida para a localização de instalação do JDK para completar os passos deste artigo.

## <a name="create-a-function-project"></a>Criar um projeto de função

1. Intellij IDEA, selecione **Create New Project**.  
1. Na janela **New Project,** selecione **Maven** a partir do painel esquerdo.
1. Selecione a Configuração a partir da caixa de verificação **do arquétipo** e, em seguida, selecione **Adicionar Arquétipo** para o [arquétipo azure-funções-kotlin-arquétipo](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Na janela **Add Archetype,** complete os campos da seguinte forma:
    - _GroupId_: com.microsoft.azure
    - _ArtefactoId_: azure-funções-kotlin-arquétipo
    - _Versão_: Utilize a versão mais recente [do repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ Criar um projeto Maven a partir de arquétipo em IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecione **OK** e, em seguida, selecione **Seguinte**.
1. Insira os seus dados para o projeto atual e **selecione Terminar**.

A Maven cria os ficheiros do projeto numa nova pasta com o mesmo nome que o valor _ArtifactId._ O código gerado do projeto é uma função simples desencadeada por HTTP que [ecoa](./functions-bindings-http-webhook.md) o corpo do pedido HTTP de desencadeamento.

## <a name="run-project-locally-in-the-ide"></a>Executar projeto localmente no IDE

> [!NOTE]
> Para executar e depurar o projeto localmente, certifique-se de que instalou [ferramentas core de funções Azure, versão 2](functions-run-local.md#v2).

1. Importar alterações manualmente ou permitir [a importação automática.](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)
1. Abra a barra de ferramentas **Maven Projects.**
1. Expandir **o Ciclo de Vida** e, em seguida, abrir o **pacote.** A solução é construída e embalada num diretório-alvo recém-criado.
1. Expanda **as**  >  **funções de azul dos Plugins** e abra **as funções azul:executar** para iniciar o tempo de funcionamento local das Funções Azure.  
  ![Barra de ferramentas Maven para Funções Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Feche a caixa de diálogo de execução quando terminar de testar a sua função. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

## <a name="debug-the-project-in-intellij"></a>Depurar o projeto em IntelliJ

1. Para iniciar o anfitrião da função no modo de depurar, adicione **-DenableDebug** como argumento quando executar a sua função. Pode alterar a configuração em [objetivos maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou executar o seguinte comando numa janela terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Este comando faz com que o anfitrião da função abra uma porta de depurado a 5005.

1. No menu **'Executar',** **selecione Configurações de Edição**.
1. Selecione **(+)** para adicionar um **Remote**.
1. Preencha os campos _Nome_ e _Definições_ e, em seguida, selecione **OK** para guardar a configuração.
1. Após a configuração, selecione **Debug < Nome de configuração remota >** ou prima Shift+F9 no seu teclado para começar a depurar.

   ![Projeto Debug em IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Quando terminar, pare o depurar e o processo de corrida. Apenas um hospedeiro de função pode estar ativo e funcionar localmente de cada vez.

## <a name="deploy-the-project-to-azure"></a>Implementar o projeto no Azure

1. Antes de poder implementar o seu projeto numa aplicação de função em Azure, tem de [iniciar sessão utilizando o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implemente o seu código numa nova aplicação de função utilizando o `azure-functions:deploy` alvo Maven. Também pode selecionar as **funções azure:implementar** na janela Maven Projects.

   ```
   mvn azure-functions:deploy
   ```

1. Encontre o URL para a função de gatilho HTTP na saída Azure CLI depois de a aplicação de função ter sido implementada com sucesso.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a sua primeira aplicação de função Kotlin para o Azure, reveja o [guia de desenvolvimento de Java Functions Azure Functions](functions-reference-java.md) para obter mais informações sobre o desenvolvimento das funções de Java e Kotlin.
- Adicione aplicativos de função adicionais com diferentes gatilhos ao seu projeto utilizando o `azure-functions:add` alvo Maven.
