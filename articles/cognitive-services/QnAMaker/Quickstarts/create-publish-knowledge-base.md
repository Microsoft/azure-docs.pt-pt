---
title: 'Quickstart: Criar, treinar e publicar base de conhecimento - QnA Maker'
description: Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de conhecimento QnA Maker a partir de uma página web simples de FAQ, para responder a perguntas QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 68cde1be5f1a1cb0e8757f3143cac9363c6d9ddd
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376045"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Quickstart: Crie, treine e publique a sua base de conhecimentos QnA Maker

Pode criar uma Base de Dados de Conhecimento do Criador de FAQ a partir dos seus próprios conteúdos, como as FAQ ou os manuais de produtos. Este artigo inclui um exemplo de criação de uma base de conhecimento QnA Maker a partir de uma página web simples de FAQ, para responder a perguntas QnA Maker.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
> * Um [recurso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker criado no portal Azure. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Crie a sua primeira base de conhecimento qnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

1. Inscreva-se no portal [QnAMaker.ai](https://QnAMaker.ai) com as suas credenciais Azure.

2. No portal QnA Maker, selecione **Criar uma base de conhecimento.**

3. Na página **Criar,** salte **o Passo 1** se já tiver o seu recurso QnA Maker.

    Se ainda não criou o recurso, selecione **Criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

    Quando terminar de criar o recurso no portal Azure, volte ao portal QnA Maker, refresque a página do navegador e continue até ao **Passo 2**.

4. No **Passo 2,** selecione o seu diretório Ative, subscrição, serviço (recurso) e o idioma para todas as bases de conhecimento criadas no serviço.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Screenshot de selecionar uma base de conhecimento de serviço QnA Maker":::

5. No **passo 3** , nomeie a sua base de conhecimento **My Sample QnA KB**.

6. No **passo 4** , configurar as definições com a seguinte tabela:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração em várias voltas a partir de ficheiros URLs, .pdf ou .docx.**|Marcado|
    |**Texto predefinido de várias voltas**| Selecione e opção|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Conversa**|Selecione **Profissional**|

7. No **passo 5** , selecione **Criar o seu KB**.

    O processo de extração leva alguns momentos para ler o documento e identificar perguntas e respostas.

    Depois de o QnA Maker criar com sucesso a base de conhecimento, abre-se a página base do **Conhecimento.** Pode editar o conteúdo da base de conhecimento nesta página.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

1. Inscreva-se no portal [QnAMaker.ai](https://QnAMaker.ai) com as suas credenciais Azure.

2. No portal QnA Maker, selecione **Criar uma base de conhecimento.**

3. Na página **Criar,** salte **o Passo 1** se já tiver o seu recurso QnA Maker.

    Se ainda não criou o recurso, selecione **Criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

    Quando terminar de criar o recurso no portal Azure, volte ao portal QnA Maker, refresque a página do navegador e continue até ao **Passo 2**.

4. No **Passo 2,** selecione o seu diretório Ative, subscrição, serviço (recurso) e o idioma para todas as bases de conhecimento criadas no serviço.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Screenshot de selecionar uma base de conhecimento de serviço do QnA Maker gerida pré-visualização":::

5. No **Passo 2** , se estiver a criar a primeira base de conhecimento para o seu serviço, terá a opção de tornar a definição de linguagem específica para cada base de conhecimento. Uma vez definida a definição de idioma para a primeira base de conhecimento, não poderá modificar as definições do serviço mais tarde.

6. No **passo 3** , nomeie a sua base de conhecimento **My Sample QnA KB**. 

7. No **passo 4** , configurar as definições com a seguinte tabela:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração em várias voltas a partir de ficheiros URLs, .pdf ou .docx.**|Marcado|
    |**Texto predefinido de várias voltas**| Selecione e opção|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Conversa**|Selecione **Profissional**|

8. No **passo 5** , selecione **Criar o seu KB**.

    O processo de extração leva alguns momentos para ler o documento e identificar perguntas e respostas.

    Depois de o QnA Maker criar com sucesso a base de conhecimento, abre-se a página base do **Conhecimento.** Pode editar o conteúdo da base de conhecimento nesta página.

---

## <a name="add-a-new-question-and-answer-set"></a>Adicione um novo conjunto de perguntas e respostas

1. No portal QnA Maker, na página **Editar,** selecione **+ Adicionar par QnA** da barra de ferramentas de contexto.
1. Adicione a seguinte pergunta:

    `How many Azure services are used by a knowledge base?`

1. Adicione a resposta formatada com _marcação:_

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Adicione a pergunta como texto e a resposta formatada com marcação.":::

    O símbolo de `*` marcação, é usado para pontos de bala. É `\n` usado para uma nova linha.

    A página **editar** mostra o ponto de vista. Quando utilizar o painel **de teste** mais tarde, verá a marcação corretamente visualizada.

## <a name="save-and-train"></a>Guardar e preparar

No canto superior direito, **selecione Save and train** para guardar as suas edições e treinar o QnA Maker. As edições só são mantidas se tiverem sido guardadas.

## <a name="test-the-knowledge-base"></a>Testar a base de conhecimento

# <a name="qnamaker-v1"></a>[QnAMaker V1](#tab/v1)

1. No portal QnA Maker, no canto superior direito, selecione **Teste** para testar que as alterações que fez fizeram fizeram efeito.
2. Introduza um exemplo de consulta do utilizador na caixa de texto.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="Introduza um exemplo de consulta do utilizador na caixa de texto.":::

3. Selecione **Inspecionar** para analisar a resposta de forma mais detalhada. A janela de teste é utilizada para testar as suas alterações na base de conhecimento antes de publicar a sua base de conhecimento.

4. Selecione **testar** novamente para fechar o painel **de teste.**

# <a name="qnamaker-v2-preview"></a>[QnAMaker V2 (Pré-visualização)](#tab/v2)

1. No portal QnA Maker, no canto superior direito, selecione **Teste** para testar que as alterações que fez fizeram fizeram efeito.
2. Introduza um exemplo de consulta do utilizador na caixa de texto.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/enable-short-answer.png" alt-text="Screenshot da pergunta de diálogo de chat UI: quantas páginas haverá o passaporte de um menor? e a resposta:Apenas 36 páginas do livro de passaportes são emitidas a menores.":::

3. Se ativar a função MRC para a sua base de conhecimentos, selecionando a **resposta curta Enable (experimental),** verá também uma resposta precisa, se disponível, juntamente com a passagem de resposta no painel de teste. 

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/enable-short-answer-checked.png" alt-text="Screenshot da pergunta de diálogo de chat UI: quantas páginas haverá o passaporte de um menor? e a resposta: 36. numa única linha seguida de uma segunda linha indicando: Apenas 36 páginas de livro de passaportes são emitidos a menores.":::

4. Selecione Inspecionar para examinar a resposta mais detalhadamente. A janela de teste é utilizada para testar as suas alterações na base de conhecimento antes de publicar a sua base de conhecimento. 
5. Selecione **testar** novamente para fechar o painel **de teste.**
---

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Ao publicar uma base de conhecimento, o conteúdo da sua base de conhecimento passa do `test` índice para um índice na pesquisa do `prod` Azure.

![Screenshot de mover o conteúdo da sua base de conhecimentos](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal QnA Maker, **selecione Publicar**. Depois, para confirmar, selecione **Publicar** na página.

    O serviço Criador de FAQ é agora publicado com êxito. Pode utilizar o ponto final na sua aplicação ou código de bot.

    ![Screenshot da publicação bem sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, pode criar um bot a partir da página **Publicar:**

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de conhecimento para diferentes regiões ou planos de preços para os bots individuais.
* Se quiser apenas um bot para a base de conhecimento, use o Ver todos os seus bots no link **do portal Azure** para ver uma lista dos seus bots atuais.

Quando se faz alterações na base de conhecimento e se republica, não precisa de tomar mais medidas com o bot. Já está configurado para trabalhar com a base de conhecimento, e trabalha com todas as mudanças futuras na base de conhecimento. Sempre que publica uma base de conhecimento, todos os bots ligados a ela são automaticamente atualizados.

1. No portal QnA Maker, na página **Publicar,** selecione **Create bot**. Este botão só aparece depois de publicar a base de conhecimento.

    ![Screenshot de criar um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Um novo separador de navegador abre para o portal Azure, com a página de criação do Azure Bot Service. Configure o serviço de bots Azure. O bot e o QnA Maker podem partilhar o plano de serviço de aplicações web, mas não podem partilhar a aplicação web. Isto significa que o nome da **aplicação** para o bot deve ser diferente do nome da aplicação para o serviço QnA Maker.

    * **O que deve fazer**
        * Mude a pega bot - se não for única.
        * Selecione linguagem SDK. Uma vez criado o bot, você pode baixar o código para o seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **O que não deve fazer**
        * alterar as seguintes definições no portal Azure ao criar o bot. São pré-povoados para a sua base de conhecimento existente:
           * Chave QnA Auth
           * Plano de serviço de aplicações e localização


1. Após a criação do bot, abra o recurso **de serviço Bot.**
1. Sob **Gestão de** Bot, selecione **Teste no Web Chat**.
1. No pedido de chat do **Tipo da sua mensagem,** insira:

    `Azure services?`

    O chat bot responde com uma resposta da sua base de conhecimento.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Introduza uma consulta do utilizador no chat web de teste.":::

## <a name="what-did-you-accomplish"></a>O que conseguiu?

Criou uma nova base de conhecimento, adicionou um URL público à base de conhecimento, adicionou o seu próprio par QnA, treinado, testado e publicado a base de conhecimento.

Depois de publicar a base de conhecimento, criou um bot e testou o bot.

Tudo isto foi feito em poucos minutos sem ter que escrever nenhum código ou limpar o conteúdo.

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar para o próximo quickstart, elimine os recursos-quadro do QnA Maker e bot no portal Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar perguntas com metadados](add-question-metadata-portal.md)

Para obter mais informações:

* [Formato de marcação em respostas](../reference-markdown-format.md)
* [Fontes de dados](../concepts/knowledge-base.md)do QnA Maker.

