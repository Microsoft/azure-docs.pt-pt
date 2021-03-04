---
title: Criar uma função C# utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função C# e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure utilizando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121036"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função C# em Azure utilizando o Código do Estúdio Visual

> [!div class="op_single_selector" title1="Selecione o seu idioma de função: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Outros (Go/Rust)](create-first-function-vs-code-other.md)

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função baseada na biblioteca de classe C# que responda aos pedidos HTTP. Depois de testar o código localmente, você implantá-lo para o <abbr title="Um ambiente de computação em tempo de execução em que todos os detalhes do servidor são transparentes para os desenvolvedores de aplicações, simplificando o processo de implementação e gestão do código.">sem servidor</abbr> ambiente de <abbr title="O serviço da Azure que fornece um ambiente de computação sem servidor de baixo custo para aplicações.">Funções do Azure</abbr>.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em CLI](create-first-function-cli-csharp.md) deste artigo.
    
## <a name="1-configure-your-environment"></a>1. Configurar o seu ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Um Azure <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">conta</abbr> com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#install-the-azure-functions-core-tools)

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para o Visual Studio Code.  

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. Crie o seu projeto local

Nesta secção, você usa Código de Estúdio Visual para criar um local <abbr title="Um recipiente lógico para uma ou mais funções individuais que podem ser implantadas e geridas em conjunto.">Projeto Azure Functions</abbr> em C#. Mais adiante neste artigo, irá publicar o seu código de função no Azure.

1. Escolha o ícone Azure no <abbr title="O grupo vertical de ícones no lado esquerdo da janela Visual Studio Code.">Barra de atividades</abbr>, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Selecione uma localização do diretório para a área de trabalho do seu projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `C#`.

    + **Selecione um modelo para a primeira função do seu projeto**: Selecione `HTTP trigger`.

    + **Forneça um nome de função**: Escreva `HttpExample`.

    + **Forneça um espaço de nomes**: Escreva `My.Functions`.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre os níveis de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um HTTP <abbr title="O tipo de evento que invoca o código da função, como um pedido HTTP, uma mensagem de fila ou uma hora específica.">gatilho</abbr>. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files)

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto à Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na sua subscrição Azure e, em seguida, implementa o seu código. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    

1. Forneça as seguintes informações nos pedidos:

    + **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá isto se já tiver uma aplicação de função válida aberta.

    + **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione Aplicação de Funções no Azure**: Selecione `+ Create new Function App`. (Não escolha a `Advanced` opção, que não está abrangida por este artigo.)

    + **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para ter certeza de que é <abbr title="O nome deve ser único em todos os projetos de Funções usados por todos os clientes da Azure em todo o mundo. Normalmente, você usa uma combinação do seu nome pessoal ou da empresa, nome de aplicação e um identificador numérico, como em contoso-bizapp-func-20">único em Funções Azure</abbr>. 

    + **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si.

    A extensão mostra o estado dos recursos individuais à medida que estão a ser criados em Azure na área de notificação.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação da criação de recursos Azure":::

1. Quando concluídos, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da aplicação da sua função:

    + Um **grupo de recursos**, que é um recipiente lógico para recursos relacionados.
    + Uma **conta padrão de Armazenamento Azure,** que mantém informações estatais e outras sobre os seus projetos.
    + Um **plano de consumo**, que define o anfitrião subjacente para a sua aplicação de função sem servidor. 
    + Uma **aplicação de função**, que fornece o ambiente para executar o seu código de função. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação e partilha de recursos dentro do mesmo plano de hospedagem.
    + Uma **instância de Insights de Aplicação** ligada à aplicação de função, que rastreia o uso da sua função sem servidor.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 

    > [!TIP]
    > Por predefinição, os recursos Azure exigidos pela sua aplicação de função são criados com base no nome da aplicação de função que fornece. Por padrão, também são criados no mesmo novo grupo de recursos com a aplicação de função. Se quiser personalizar os nomes destes recursos ou reutilizar os recursos existentes, tem de [publicar o projeto com opções avançadas de criação.](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)


1. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone da campainha no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Executar a função em Azure

1. De volta ao **Azure: Funções** na barra lateral, expandir a sua subscrição, a sua nova aplicação de funções e **funções**. Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar função agora em Azure a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` .

    Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.

## <a name="5-clean-up-resources"></a>5. Limpar recursos

Quando continuar para o [próximo passo](#next-steps) e adicionar um <abbr title="Um meio de associar uma função a uma fila de armazenamento, para que possa criar mensagens na fila.">Encadernação de saída da fila de armazenamento Azure</abbr> para a sua função, precisa manter todos os seus recursos no lugar para construir o que já fez.

Caso contrário, pode utilizar os seguintes passos para eliminar a aplicação de função e os seus recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Para saber mais sobre os custos das funções, consulte [os custos do plano de consumo](functions-consumption-costs.md)estimado.

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No próximo artigo, expande-se essa função adicionando uma saída <abbr title="Uma ligação declarativa entre uma função e outros recursos. Uma ligação de entrada fornece dados para a função; uma ligação de saída fornece dados da função para outros recursos.">encadernação</abbr>. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
