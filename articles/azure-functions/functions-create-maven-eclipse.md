---
title: Criar uma aplicação de função do Azure com Java e o Eclipse | Documentos da Microsoft
description: Guia de procedimentos para criar e publicar um pedido de HTTP simple acionada por aplicações sem servidor com Java e o Eclipse para as funções do Azure.
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
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881431"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Criar a primeira função com o Java e o Eclipse 

Este artigo mostra-lhe como criar uma [sem servidor](https://azure.microsoft.com/solutions/serverless/) projeto de função com o IDE do Eclipse e o Apache Maven, testar e depurá-lo, em seguida, implementá-la para as funções do Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma aplicação de funções do Azure com Java e o Eclipse, tem de ter o seguinte instalado:

-  [Java Development Kit](https://www.azul.com/downloads/zulu/), versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), com Java e Maven suportar.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

É altamente recomendável a instalação também [ferramentas de núcleo de funções do Azure, versão 2](functions-run-local.md#v2), que proporcionam um ambiente local para executar e depurar funções do Azure. 

## <a name="create-a-functions-project"></a>Criar um projeto de funções

1. No Eclipse, selecione o **arquivo** menu, em seguida, selecione **projeto**. 
1. Abra o **projeto Java** pasta na **novo projeto** janela e selecione **projeto Maven**, em seguida, selecione **seguinte**.
1. Aceite as predefinições no **novo projeto Maven** de diálogo e selecione **próxima**.
1. Selecione **adicionar Mvn** e adicione as entradas para o [do azure-funções-mvn](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - ID do grupo de mvn: com.microsoft.azure
    - ID do artefacto mvn: azure-funções-mvn
    - Versão: Utilizar a versão mais recente do [o repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![criar Eclipse Maven](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Clique em **OK** e introduza os detalhes do projeto atual e, eventualmente, **concluir**.

O Maven cria os ficheiros de projeto numa nova pasta com um nome de _artifactId_. O código gerado no projeto é um simples [acionada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) função que ecoa o corpo da solicitação HTTP acionadora.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [Ferramentas de núcleo das funções do Azure, versão 2](functions-run-local.md#v2) tem de estar instalado para executar e depurar funções localmente.

1. Com o botão direito no projeto gerado, em seguida, escolha **Run As** e **compilação Maven**.
1. No **Edit Configuration** caixa de diálogo, introduza `package` no **objetivos** e **nome** campos, em seguida, selecione **executar**. Isto irá criar e empacotar o código de função.
1. Assim que a compilação estiver concluída, criar outra configuração de execução como acima, usando `azure-functions:run` como o objetivo e o nome. Selecione **executar** para executar a função no IDE.

Terminar o tempo de execução na janela da consola, quando tiver terminado a testar a sua função. Anfitrião de apenas uma função pode ser ativa e em execução localmente ao mesmo tempo.

### <a name="debug-the-function-in-eclipse"></a>Depurar a função no Eclipse

No seu **Run As** definida no passo anterior, alteração de configuração `azure-functions:run` para `mvn azure-functions:run -DenableDebug` e execute a configuração atualizada para iniciar a aplicação de funções no modo de depuração.

Selecione o **execute** menu e abra **configurações de depuração**. Escolher **aplicação Java remota** e criar um novo. Dê um nome de sua configuração e preencha as definições. A porta deve ser consistente com a porta de depuração aberta pelo anfitrião de função, o que, por predefinição, é `5005`. Após a configuração, clique em `Debug` para iniciar a depuração.

![Depure funções no Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Defina pontos de interrupção e Inspecione os objetos na sua função usando o IDE. Quando terminar, pare o depurador e o anfitrião de função em execução. Anfitrião de apenas uma função pode ser ativa e em execução localmente ao mesmo tempo.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Inicie sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar a utilizar a linha de comandos do seu computador.

```azurecli
az login
```

Implementar o seu código numa nova aplicação de funções com o `azure-functions:deploy` objetivo Maven numa nova **Run** configuração.

Quando a implementação estiver concluída, verá o URL que pode utilizar para aceder à sua aplicação de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Passos Seguintes

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicione mais funções com diferentes acionadores ao seu projeto com o destino do Maven `azure-functions:add`.
