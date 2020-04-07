---
title: 'Quickstart: Criar, treinar e publicar base de conhecimento - QnA Maker'
description: Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de conhecimento qnA Maker a partir de uma simples página web de FAQ, para responder a perguntas QnA Maker.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: 5a3cd4a66ae3a7148b517050c3d1a8c6c8e7a712
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756819"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Quickstart: Criar, treinar e publicar a sua base de conhecimento qnA Maker

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de conhecimento qnA Maker a partir de uma simples página web de FAQ, para responder a perguntas QnA Maker.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
> * Um [recurso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker criado no portal Azure. Lembre-se do id do Diretório Ativo Azure, Subscrição, nome de recurso QnA que selecionou quando criou o recurso.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Crie a sua primeira base de conhecimento qnA Maker

1. Inscreva-se no portal [QnAMaker.ai](https://QnAMaker.ai) com as suas credenciais Azure.

1. No portal QnA Maker, selecione **Criar uma base de conhecimento**.

1. Na página **Criar,** salte o **Passo 1** se já tiver o seu recurso QnA Maker.

    Se ainda não criou o recurso, selecione **Criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Lembre-se do id do Diretório Ativo Azure, Subscrição, nome de recurso QnA que selecionou quando criou o recurso.

    Quando terminar de criar o recurso no portal Azure, volte ao portal QnA Maker, refresque a página do navegador e continue a **passo 2**.

1. No **Passo 3,** selecione o seu diretório Ativo, subscrição, serviço (recurso) e o idioma para todas as bases de conhecimento criadas no serviço.

   ![Screenshot de selecionar uma base de conhecimento de serviço QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. No **passo 3,** nomeie a sua base de conhecimentos **My Sample QnA KB**.

1. No **passo 4,** configure as definições com a tabela seguinte:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração de várias voltas a partir de URLs, ficheiros .pdf ou .docx.**|Assinalado|
    |**Texto de resposta padrão**| `Quickstart - default answer not found.`|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selecionar **Profissional**|

1. No **passo 5,** selecione **Criar o seu KB**.

    O processo de extração demora alguns momentos a ler o documento e a identificar perguntas e respostas.

    Depois da QnA Maker criar com sucesso a base de conhecimentos, a página base do **Conhecimento** abre. Pode editar o conteúdo da base de conhecimentos nesta página.

## <a name="add-a-new-question-and-answer-set"></a>Adicione um novo conjunto de perguntas e respostas

1. No portal QnA Maker, na página **Editar,** selecione + Adicione o **par QnA** da barra de ferramentas de contexto.
1. Adicione a seguinte pergunta:

    `How many Azure services are used by a knowledge base?`

1. Adicione a resposta formatada com _marcação:_

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Adicione a pergunta como texto e a resposta formatada com marcação.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    O símbolo de `*`marcação, é usado para pontos de bala. É `\n` usado para uma nova linha.

    A página **edita** mostra o markdown. Quando utilizar o painel **de teste** mais tarde, verá a marcação corretamente exibida.

## <a name="save-and-train"></a>Guardar e preparar

Na parte superior direita, selecione **Guardar e treinar** para salvar as suas edidas e treinar o Fabricante QnA . As edições só são mantidas se tiverem sido guardadas.

## <a name="test-the-knowledge-base"></a>Testar a base de conhecimento

1. No portal QnA Maker, na parte superior direita, selecione **Teste** para testar que as alterações que fez produziram efeito.
1. Introduza uma consulta de utilizador exemplo na caixa de texto.

    `How many Azure services are used by a knowledge base?`

    ![ Introduza uma consulta de utilizador exemplo na caixa de texto. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é utilizada para testar as suas alterações na base de conhecimentos antes de publicar a sua base de conhecimentos.

1. Selecione **Test** novamente para fechar o painel **de teste.**

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Ao publicar uma base de conhecimento, o conteúdo `test` da `prod` sua base de conhecimentos passa do índice para um índice na pesquisa Azure.

![Screenshot de mover o conteúdo da sua base de conhecimento](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal QnA Maker, selecione **Publicar**. Depois, para confirmar, selecione **Publicar** na página.

    O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Screenshot de publicação bem sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, pode criar um bot a partir da página **Publicar:**

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de conhecimento para diferentes regiões ou planos de preços para os bots individuais.
* Se quiser apenas um bot para a base de conhecimento, utilize todos **os seus bots no link** do portal Azure para ver uma lista dos seus bots atuais.

Quando fazalterações na base de conhecimentos e republique, não precisa de tomar mais medidas com o bot. Já está configurado para trabalhar com a base de conhecimento, e trabalha com todas as mudanças futuras na base de conhecimento. Sempre que publica uma base de conhecimento, todos os bots ligados a ela são automaticamente atualizados.

1. No portal QnA Maker, na página **Publicar,** selecione **Create bot**. Este botão só aparece depois de publicar a base de conhecimento.

    ![Screenshot de criar um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Abre-se um novo separador de navegador para o portal Azure, com a página de criação do Serviço Bot Azure. Configure o serviço de bots Azure. O bot e o QnA Maker podem partilhar o plano de serviço de aplicações web, mas não podem partilhar a aplicação web. Isto significa que o nome da **aplicação** para o bot deve ser diferente do nome da aplicação para o serviço QnA Maker.

    * **O que deve fazer**
        * Mude a pega do bot - se não for única.
        * Selecione linguagem SDK. Assim que o bot for criado, pode transferir o código para o seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **O que não deve fazer**
        * alterar as seguintes definições no portal Azure ao criar o bot. São pré-povoados para a sua base de conhecimento existente:
           * QnA Auth Key
           * Plano de serviço de aplicativos e localização


1. Depois de criado o bot, abra o recurso **de serviço Bot.**
1. Sob **gestão de Bot,** selecione **Teste no Chat Web**.
1. Ao chat prompt de **Digite a sua mensagem,** introduza:

    `Azure services?`

    O chat bot responde com uma resposta da sua base de conhecimento.

    ![Introduza uma consulta de utilizador no chat web de teste.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>O que conseguiu?

Criou uma nova base de conhecimento, adicionou uma URL pública à base de conhecimentos, adicionou o seu próprio par QnA, treinou, testou e publicou a base de conhecimento.

Depois de publicar a base de conhecimento, criou um bot e testou o bot.

Tudo isto foi feito em poucos minutos sem ter que escrever qualquer código ou limpar o conteúdo.

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar para o próximo arranque rápido, elimine os recursos-quadro da QnA Maker e bot no portal Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar perguntas com metadados](add-question-metadata-portal.md)

Para obter mais informações:

* [Formato de marcação em respostas](../reference-markdown-format.md)
* Fontes de [dados](../concepts/knowledge-base.md)do Fabricante QnA .


