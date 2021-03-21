---
title: Criar uma função Python utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função Python e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure usando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031740"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Quickstart: Criar uma função em Azure com Python usando Código de Estúdio Visual

> [!div class="op_single_selector" title1="Selecione o seu idioma de função: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Outros (Go/Rust)](create-first-function-vs-code-other.md)

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função Python que responde aos pedidos HTTP. Depois de testar o código localmente, você implantá-lo para o <abbr title="Um ambiente de computação em tempo de execução em que todos os detalhes do servidor são transparentes para os desenvolvedores de aplicações, o que simplifica o processo de implementação e gestão do código.">sem servidor</abbr> ambiente de <abbr title="Um serviço Azure que fornece um ambiente de computação sem servidor de baixo custo para aplicações.">Funções do Azure</abbr>.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em CLI](create-first-function-cli-python.md) deste artigo.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Um Azure <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">conta</abbr> com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#install-the-azure-functions-core-tools)

+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) são suportados por Azure Functions (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Código de Estúdio Visual.  

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> Crie o seu projeto local

1. Escolha o ícone Azure no <abbr title="O grupo vertical de ícones no lado esquerdo da janela Visual Studio Code.">Barra de atividades</abbr>, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Selecione uma localização do diretório para a área de trabalho do seu projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `Python`.

    + **Selecione um pseudónimo Python para criar um ambiente virtual**: Escolha a localização do seu intérprete Python. Se a localização não for mostrada, escreva em todo o caminho para o binário Python.  

    + **Selecione um modelo para a primeira função do seu projeto**: Selecione `HTTP trigger`.

    + **Forneça um nome de função**: Escreva `HttpExample`.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre os níveis de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

<br/>
<details>
<summary><strong>Não pode criar um projeto de função?</strong></summary>

As questões mais comuns a resolver na criação de um projeto de Funções locais são:
* Não tem a extensão Azure Functions instalada. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Prima <kbd>F5</kbd> para iniciar o projeto da aplicação de funções.

1. No painel **Terminal,** consulte o ponto final url da sua função a funcionar localmente.

    ![Função local saída do Código VS](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Com as Ferramentas Core em funcionamento, vá para a área **Azure: Funções.** Em **Funções**, expandir funções **de projeto**  >  **locais.** Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Execute a função agora a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa localmente e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual. As informações sobre a execução da função são mostradas no painel **terminal.**

1. Prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core e desligar o depurar.

<br/>
<details>
<summary><strong>Não pode executar a função localmente?</strong></summary>

As questões mais comuns a resolver ao executar um projeto local de funções são:
* Não tem as Ferramentas Centrais instaladas. 
*  Se tiver problemas em funcionamento no Windows, certifique-se de que a camada de terminais padrão do Código do Estúdio Visual não está definida para **WSL Bash**. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Inscreva-se em Azure

Para publicar a sua aplicação, inscreva-se no Azure. Se já assinou, vá para a próxima secção.

1. Escolha o ícone Azure na barra de atividade, em seguida, na área **Azure: Funções,** escolha **Iniciar súm em Azure...**.

    ![Inscreva-se no Azure dentro do Código VS](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando solicitado no navegador, escolha a **sua conta Azure** e **inscreva-se usando** as suas credenciais de conta Azure.

1. Depois de ter assinado com sucesso, feche a nova janela do navegador e volte ao Código do Estúdio Visual. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto à Azure

A sua primeira implementação do seu código inclui a criação de um recurso função na sua subscrição Azure.

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publicar o projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos pedidos:

    + **Selecione pasta**: Escolha a pasta que contém a sua aplicação de função.

    + **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione Aplicação de Funções no Azure**: Selecione `+ Create new Function App`.

    + **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para ter certeza de que é <abbr title="O nome deve ser único em todos os clientes da Azure a nível global. Por exemplo, você pode usar uma combinação do seu nome pessoal ou de organização, nome de aplicação e um identificador numérico, como em contoso-bizapp-func-20.">único em Azure</abbr>. 

    + **Selecione um tempo de execução**: Escolha a versão de Python que tem vindo a executar localmente. Pode utilizar o `python --version` comando para verificar a sua versão.

    + **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si.

    A extensão mostra o estado dos recursos individuais à medida que estão a ser criados em Azure na área de notificação.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação da criação de recursos Azure":::

1. Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Ver Saída** para ver os resultados da criação e implementação. 

    ![Criar notificação completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

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

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Executar a função em Azure

1. De volta ao **Azure: Funções** barra lateral, expandir a nova aplicação de função.
1. Expandir **funções**, em seguida, clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha Executar **Função Agora...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar função agora em Azure a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` .

    Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.

## <a name="7-clean-up-resources"></a>7. Limpar recursos

Quando continuar para o [próximo passo](#next-steps) e adicionar um <abbr title="Um meio de associar uma função a uma fila de armazenamento, para que possa criar mensagens na fila.">Encadernação de saída da fila de armazenamento Azure</abbr> para a sua função, precisa manter todos os seus recursos no lugar para construir o que já fez.

Caso contrário, pode utilizar os seguintes passos para eliminar a aplicação de função e os seus recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Para saber mais sobre os custos das funções, consulte [os custos do plano de consumo](functions-consumption-costs.md)estimado.

## <a name="next-steps"></a>Passos seguintes

Expandir essa função adicionando uma saída <abbr title="Uma ligação declarativa entre uma função e outros recursos. Uma ligação de entrada fornece dados para a função; uma ligação de saída fornece dados da função para outros recursos.">encadernação</abbr>. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
