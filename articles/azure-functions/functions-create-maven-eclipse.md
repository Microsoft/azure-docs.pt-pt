---
title: Crie uma aplicação de função Azure com Java e Eclipse
description: Como orientar para criar e publicar uma simples aplicação sem servidores EM HTTP, utilizando funções java e eclipse para azure.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 42e9ed7c080c9274fad7eda8e4c8af3631ed41f5
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756480"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Crie a sua primeira função com Java e Eclipse 

Este artigo mostra-lhe como criar um projeto de função [sem servidores](https://azure.microsoft.com/solutions/serverless/) com o Eclipse IDE e Apache Maven, teste e depura-o e, em seguida, implementá-lo para Funções Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma aplicação de funções com Java e Eclipse, deve ter o seguinte instalado:

-  [Kit de Desenvolvimento Java,](https://www.azul.com/downloads/zulu/)versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.
-  [Eclipse,](https://www.eclipse.org/downloads/packages/)com apoio de Java e Maven.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

É altamente recomendado instalar também [ferramentas centrais de funções Azure, versão 2,](functions-run-local.md#v2)que fornecem um ambiente local para executar e depurar funções azure. 

## <a name="create-a-functions-project"></a>Criar um projeto funções

1. No Eclipse, selecione o menu **Ficheiro** e, em seguida, selecione **New -&gt; Maven Project**. 
1. Aceite os incumprimentos no diálogo do **Novo Projeto Maven** e selecione **Next**.
1. **Selecione Adicionar Arquétipo** e adicione as entradas para o [arquétipo](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)de funções azul .
    - Id do Grupo Archetype: com.microsoft.azure
    - Id de artefacto arquétipo: azure-functions-archetype
    - Versão: Verifique e use versão mais recente [do repositório](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![central Eclipse Maven criar](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Clique em **OK** e, em seguida, clique em **Seguinte**.  Certifique-se de preencher valores para `resourceGroup` `appName`todos `appRegion` os campos, incluindo , e (por favor use um nome de aplicação diferente do **fabrikam-function-20170920120101928**), e eventualmente **Terminar**.
    ![Eclipse Maven cria2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

O Maven cria os ficheiros de projeto numa nova pasta com um nome de _artifactId_. O código gerado no projeto é uma função simples [de http desencadeada](/azure/azure-functions/functions-bindings-http-webhook) que ecoa o corpo do pedido HTTP desencadeando.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [Funções Azure Core Tools, a versão 2](functions-run-local.md#v2) deve ser instalada para funcionar e depurar funções localmente.

1. Clique à direita no projeto gerado e, em seguida, escolha **run As** e **Maven build**.
1. No diálogo **de configuração editar,** `package` introduza nos campos **De Objetivos** e **Nomes** e, em seguida, selecione **Executar**. Isto irá construir e embalar o código de função.
1. Uma vez concluída a construção, crie `azure-functions:run` outra configuração run como acima, usando como objetivo e nome. Selecione **Executar** para executar a função no IDE.

Termine o tempo de execução na janela da consola quando terminar de testar a sua função. Apenas um hospedeiro de funções pode estar ativo e a funcionar localmente de cada vez.

### <a name="debug-the-function-in-eclipse"></a>Depurar a função em Eclipse

No seu **Run Como** configuração configurada `azure-functions:run` `azure-functions:run -DenableDebug` no passo anterior, altere e execute a configuração atualizada para iniciar a aplicação de função no modo dedepura.

Selecione o menu **Executar** e abra **as configurações de depuração**. Escolha **a Aplicação Java Remota** e crie uma nova. Dê um nome à sua configuração e preencha as definições. A porta deve ser consistente com a porta de depuração aberta pelo hospedeiro de funções, que por defeito é `5005`. Depois da configuração, clique para `Debug` começar a depurar.

![Funções de depuração no Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Desloque pontos de rutura e inspecione objetos na sua função utilizando o IDE. Quando terminar, pare o debugger e o anfitrião da função de corrida. Apenas um hospedeiro de funções pode estar ativo e a funcionar localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Inicie sessão com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar a utilizar o pedido de comando do computador.

```azurecli
az login
```

Implemente o seu código numa `azure-functions:deploy` nova aplicação Fun utilizando o objetivo Maven numa nova configuração **Run As.**

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
