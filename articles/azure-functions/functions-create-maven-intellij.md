---
title: Criar uma função Azure com Java e IntelliJ
description: Aprenda a criar e publicar uma simples aplicação http-triggered, sem servidor no Azure com Java e IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 19deaa7656cc86d534278464dba1041267ef15b0
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105044"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Crie a sua primeira função Azure com Java e IntelliJ

Este artigo apresenta o seguinte:
- Como criar um projeto de função [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) com IntelliJ IDEA
- Passos para testar e depurar a função no ambiente de desenvolvimento integrado (IDE) no seu próprio computador
- Instruções para implantar o projeto de função para funções Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para desenvolver uma função com a Java e a IntelliJ, instale o seguinte software:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
+ [Um Azure apoiou o Kit de Desenvolvimento de Java (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) para Java 8
+ As Edições [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate ou Community instaladas
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Ferramentas [core de funções mais recentes](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Instalação e início de sessão

1. Na caixa de diálogo IDEA's Settings/Preferences (Definições/Preferências do IDEA) (Ctrl+Alt+S) do IntelliJ, selecione **Plugins** (Plug-ins). Em seguida, procure o **Azure Toolkit for IntelliJ** no **Marketplace** e clique em **Install** (Instalar). Depois de instalar, clique em **Restart** (Reiniciar) para ativar o plug-in. 

    ![Plug-in do Azure Toolkit for IntelliJ no Marketplace][marketplace]

2. Para iniciar sessão na conta do Azure, abra a barra lateral **Azure Explorer** e clique no ícone **Azure Sign In** (Início de Sessão do Azure) na barra na parte superior (ou no menu do IDEA **Tools/Azure/Azure Sign in**).
    ![O comando de Início de sessão do Azure no IntelliJ][intellij-azure-login]

3. Na janela **Azure Sign In**, selecione **Device Login** (Início de sessão de dispositivo) e clique em **Sign in** (Iniciar sessão) ([other sign in options](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)) (outras opções de início de sessão).

   ![A janela Início de Sessão do Azure com início de sessão de dispositivo selecionado][intellij-azure-popup]

4. Clique em **Copy&Open** (Copiar e Abrir) na caixa de diálogo **Azure Device Login** (Início de Sessão de Dispositivo do Azure).

   ![A janela da caixa de diálogo Início de Sessão do Azure][intellij-azure-copycode]

5. No browser, cole o código do dispositivo (que foi copiado quando clicou em **Copy&Open** no passo anterior) e clique em**Next** (Seguinte).

   ![O browser de início de sessão de dispositivo][intellij-azure-link-ms-account]

6. Na caixa de diálogo **Select Subscriptions** (Selecionar Subscrições), selecione as subscrições que pretende utilizar e clique em **OK**.

   ![A caixa de diálogo Selecionar Subscrições][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Crie o seu projeto local

Nesta secção, utiliza-se o Azure Toolkit para o IntelliJ para criar um projeto local de Funções Azure. Mais tarde neste artigo, publicará o seu código de função para Azure. 

1. Abra o diálogo IntelliJ Welcome, *selecione Criar Novo Projeto* para abrir um novo assistente de projeto, selecione *Azure Functions*.

    ![Criar projeto de funções](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selecione *Http Trigger*e, em seguida, clique em *Seguinte* e siga o assistente para analisar todas as configurações nas páginas seguintes; confirmar a localização do seu projeto e, em seguida, clicar *em Terminar;* A Intellj IDEA abrirá então o seu novo projeto.

    ![Criar funções de acabamento de projeto](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Executar a App de Função localmente

1. Navegue `src/main/java/org/example/functions/HttpTriggerFunction.java` para ver o código gerado. Além da linha *17,* irá notar que existe um botão *de Execução* verde, clique nele e selecione *Executar 'azure-function-exam...'*, verá que a sua aplicação de função está a funcionar localmente com alguns registos.

    ![Projeto de funções de execução local](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Saída de funções de execução local](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Pode experimentar a função acedendo ao ponto final impresso do navegador, como `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Resultado do teste das funções de execução local](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. O registo também está impresso no seu IDEA, agora, pare a função clicando no botão *stop.*

    ![Registo de teste de funções de execução local](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Depurar a App de Função localmente

1. Agora vamos tentar depurar a sua App de função localmente, clicar no botão *Debug* na barra de ferramentas (se não a vir, clique *em Ver -> Aparência -> Barra de Ferramentas* para ativar a barra de ferramentas).

    ![Botão de funções de depuração local](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Clique na linha *20* do ficheiro `src/main/java/org/example/functions/HttpTriggerFunction.java` para adicionar um breakpoint, aceda novamente ao ponto final `http://localhost:7071/api/HttpTrigger-Java?name=Azure` , verá que o breakpoint é atingido, pode tentar mais funcionalidades de depuração como *passo*, *relógio,* *avaliação*. Pare a sessão de depuração clicando no botão stop.

    ![As funções de depuro local quebram](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Implemente a sua App de Função para Azure

1. Clique em clique direito no seu projeto no intelliJ Project explorer, selecione *Azure -> Implementar para Funções Azure*

    ![Implementar funções para Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Se ainda não tiver nenhuma App de Função, clique em *Não ter a função disponível, clique para criar uma nova*.

    ![Implementar funções para a Azure criar app](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Digite o nome da aplicação Function e escolha o plano de subscrição/plataforma/grupo de recursos/app, podendo também criar aqui o plano de grupo/Serviço de Aplicações. Em seguida, mantenha as definições de aplicações inalteradas, clique *em OK* e aguarde alguns minutos para que a nova função seja criada. Depois de *criar uma nova aplicação de funções...* a barra de progresso desaparece.

    ![Implementar funções para Azure criar assistente de aplicativos](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Selecione a aplicação de função para a quais pretende implementar (a nova aplicação de função que acabou de criar será selecionada automaticamente). Clique *em Executar* para implementar as suas funções.

    ![Implementar funções para a execução de Azure](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Implementar funções para log Azure](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Gerir Funções Azure da IDEA

1. Pode gerir as suas funções com *o Azure Explorer* no seu IDEA, clique na *App de Função,* verá todas as suas funções aqui.

    ![Ver funções no explorador](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Clique para selecionar numa das suas funções e clique no botão direito, selecione *Show Properties* para abrir a página de detalhes. 

    ![Mostrar propriedades de funções](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Clique no seu *HttpTrigger-Java*de função e selecione *Função de Gatilho,* verá que o navegador foi aberto com o URL do gatilho.

    ![Implementar funções para a execução de Azure](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Adicione mais funções ao projeto

1. Clique no pacote *org.example.funções* e selecione *Nova -> Classe de Função Azure*. 

    ![Adicione funções à entrada do projeto](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Preencha o nome de classe *HttpTest* e selecione *HttpTrigger* no assistente de classe de função criar, clique em *OK* para criar, desta forma, pode criar novas funções como quiser.

    ![Adicione funções ao gatilho selecionado do projeto](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Adicione funções à saída do projeto](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Funções de limpeza

1. Apagar funções de Azure no Explorador Azure
      
      ![Adicione funções ao gatilho selecionado do projeto](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Passos seguintes

Criou um projeto de funções Java com uma função httpizada, executou-o na sua máquina local e implantou-o para Azure. Agora, aumente a sua função por...

> [!div class="nextstepaction"]
> [Adicionar uma ligação de saída de fila de armazenamento Azure](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png