---
title: Criar uma função Java utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função Java e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure usando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031723"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função Java em Azure usando Código de Estúdio Visual

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Utilize o Código do Estúdio Visual para criar uma função Java que responda aos pedidos HTTP. Teste o código localmente e, em seguida, insiure-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre um pequeno custo de alguns USD centavo ou menos na sua <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">Conta do Azure</abbr>.

Se o Visual Studio Code não é a sua ferramenta de desenvolvimento preferida, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Maven,](create-first-function-cli-java.md) [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Uma conta Azure com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11.

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ O [pacote de extensão java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> Crie o seu projeto de Funções Locais

1. Escolha o ícone Azure na **barra de Atividade,** em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Escolha uma localização de diretório** para o seu espaço de trabalho do projeto e escolha **Select**.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `Java`.

    + **Selecione uma versão de Java**: Escolha `Java 8` `Java 11` ou, a versão Java em que as suas funções funcionam em Azure. Escolha uma versão Java que verificou localmente.

    + **Fornecer um ID de grupo**: Escolha `com.function` .

    + **Fornecer uma identificação de artefacto:** Escolha `myFunction` .

    + **Fornecer uma versão**: Escolha `1.0-SNAPSHOT` .

    + **Fornecer um nome de pacote**: Escolha `com.function` .

    + **Fornecer um nome de aplicação**: `myFunction-12345` Escolha.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função.

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

<br/>

<details>
<summary><strong>Não pode criar um projeto de função?</strong></summary>

As questões mais comuns a resolver na criação de um projeto de Funções locais são:
* Não tem a extensão Azure Functions instalada. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Executar a função localmente

1. Prima <kbd>F5</kbd> para iniciar o projeto da aplicação de funções.

1. No **Terminal,** consulte o ponto final url da sua função a funcionar localmente.

    ![Função local saída do Código VS](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Com as Ferramentas Core em funcionamento, vá para a área **Azure: Funções.** Em **Funções**, expandir funções **de projeto**  >  **locais.** Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Execute a função agora a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima <kbd>Insira</kbd> para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa localmente e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual. As informações sobre a execução da função são mostradas no painel **terminal.**

1. Prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core e desligar o depurar.

<br/>

<details>
<summary><strong>Não pode executar a função localmente?</strong></summary>

As questões mais comuns a resolver ao executar um projeto local de funções são:
* Não tem as Ferramentas Centrais instaladas. 
*  Se tiver problemas em funcionamento no Windows, certifique-se de que a camada de terminal padrão do Código do Estúdio Visual não está definida para WSL Bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Inscreva-se em Azure

Para publicar a sua aplicação, inscreva-se no Azure. Se já assinou, vá para a próxima secção.

1. Escolha o ícone Azure na barra de atividade, em seguida, na área **Azure: Funções,** escolha **Iniciar súm em Azure...**.

    ![Inscreva-se no Azure dentro do Código VS](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando solicitado no navegador, escolha a **sua conta Azure** e **inscreva-se usando** as suas credenciais de conta Azure.

1. Depois de ter assinado com sucesso, feche a nova janela do navegador e volte ao Código do Estúdio Visual.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto à Azure

A sua primeira implementação do seu código inclui a criação de um recurso função na sua subscrição Azure.

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publicar o projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos pedidos:

    + **Selecione pasta**: Escolha a pasta que contém a sua aplicação de função. 

    + **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione Aplicação de Funções no Azure**: Selecione `Create new Function App`.

    + **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome único em Azure num caminho URL. O nome que digita é validado para garantir a singularidade global.

    - **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si.

1. Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Ver Saída** para ver os resultados da criação e implementação.

    ![Criar notificação completa](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

<br/>

<details>
<summary><strong>Não pode publicar a função?</strong></summary>

Esta secção criou os recursos Azure e implantou o seu código local para a aplicação Função. Se não tivesse sucesso,

* Reveja a Saída para obter informações de erro. O ícone do sino no canto inferior direito é outra forma de ver a saída. 
* Publicou para uma aplicação de função existente? Esta ação substitui o conteúdo da app em Azure.
</details>

<br/>

<details>
<summary><strong>Que recursos foram criados?</strong></summary>

Quando concluídos, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da aplicação da sua função:

* **Grupo de recursos**: Um grupo de recursos é um recipiente lógico para recursos relacionados na mesma região.
* **Conta de Armazenamento Azure**: Um recurso de armazenamento mantém o estado e outras informações sobre o seu projeto.
* **Plano de** consumo : Um plano de consumo define o anfitrião subjacente para a sua aplicação de função sem servidor.
* **Aplicação de função**: Uma aplicação de função fornece o ambiente para executar o seu código de função e funções de grupo como uma unidade lógica.
* **Insights de aplicação**: O Application Insights rastreia o uso da sua função sem servidor.

</details>

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Executar a função em Azure

1. De volta ao **Azure: Funções** na barra lateral, expandir a sua subscrição, a sua nova aplicação de funções e **funções**. Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar função agora em Azure a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Limpar recursos

Se não pretender continuar até ao [passo seguinte,](#next-steps)elimine a aplicação de função e os seus recursos para evitar incorrer em custos adicionais.

1. No Código do Estúdio Visual, selecione o ícone Azure na barra de Atividade e, em seguida, selecione a área de Funções na barra lateral.
1. Selecione a aplicação de função, em seguida, clique com o botão direito e selecione **a aplicação Delete Function...**.

<br/>
<hr/>

## <a name="next-steps"></a>Passos seguintes

Expandir a função adicionando uma <abbr title="No Azure Storage, um meio de associar uma função a uma fila de armazenamento, para que possa criar mensagens na fila.">vinculação de saída</abbr>. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure.

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
