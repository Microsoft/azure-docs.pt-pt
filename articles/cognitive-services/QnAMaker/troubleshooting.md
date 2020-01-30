---
title: Resolução de problemas - QnA Maker
titleSuffix: Azure Cognitive Services
description: A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/23/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 159b00f9417f4a572655f1f93208dbc755012922
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844858"
---
# <a name="troubleshooting-for-qna-maker"></a>Resolução de problemas para O Criador de QnA

A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Como obter o ponto final do serviço QnAMaker

O ponto final do serviço QnAMaker é útil para fins de depuração quando contacta o QnAMaker Support ou userVoice. O ponto final é um URL desta forma: https://your-resource-name.azurewebsites.net.

1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o Serviço de Aplicações associado ao recurso QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o serviço de aplicações do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL do ponto final está disponível na secção Visão Geral

    ![Ponto final do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)


## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Use o bot de ajuda no portal QnA Maker

A QnA Maker fornece um bot **de ajuda** dentro do portal QnA Maker para ajudá-lo. O bot de ajuda está disponível em todas as páginas web. O bot usa o QnA Maker para fornecer respostas e fornece o [ C# projeto bot-quadro](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) para o bot para que possa levantar-se e funcionar rapidamente com o seu próprio bot de resposta.

![! [QnA Maker fornece um bot **Help** dentro do portal QnA Maker para ajudá-lo.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gerir a base de dados de conhecimento

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Eliminei acidentalmente uma parte do meu QnA Maker, o que devo fazer?

Não elimine nenhum dos serviços Azure criados juntamente com o recurso QnA Maker, como Search ou Web App. Estes são necessários para que o Fabricante qnA funcione, se eliminar um, o Fabricante QnA deixará de funcionar corretamente.

Todas as exclusões são permanentes, incluindo os pares de perguntas e respostas, ficheiros, URLs, personalizadas perguntas e respostas, bases de dados de conhecimento ou recursos do Azure. Certifique-se de exportar a sua base de dados de conhecimento a partir da **definições** página antes de eliminar qualquer parte da sua base de dados de conhecimento.

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Por que é o meu URLs / ficheiro (s) não extrair pares de pergunta-resposta?

É possível que a ferramenta QnA Maker não é automática extrair algum conteúdo de (FAQ) de perguntas e respostas de URLs de FAQ válidos. Nesses casos, pode colar o conteúdo de QnA num ficheiro. txt e ver se a ferramenta pode ingeri-lo. Em alternativa, forma editorial pode adicionar conteúdo à sua base de dados de conhecimento através de [portal do QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Que tamanho pode ter a base de dados de conhecimento que eu criar?

O tamanho da base de dados de conhecimento depende da pesquisa de SKU do Azure que escolher ao criar o serviço QnA Maker. Leia [aqui](./Tutorials/choosing-capacity-qnamaker-deployment.md) para obter mais detalhes.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Por que motivo não vejo qualquer coisa no menu pendente ao tentar criar uma nova base de dados de conhecimento?

Ainda não criou quaisquer serviços do QnA Maker no Azure. Leia [aqui](./How-To/set-up-qnamaker-service-azure.md) para saber como fazê-lo.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Como posso partilhar uma base de dados de conhecimento com outras pessoas?

Partilha funciona no nível de um serviço QnA Maker, ou seja, todas as bases de dados de conhecimento no serviço serão partilhadas. Leia [aqui](./How-To/collaborate-knowledge-base.md) como colaborar numa base de dados de conhecimento.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Pode partilhar uma base de dados de conhecimento com contribuintes que não se encontra no mesmo inquilino do AAD, para modificar uma base de dados de conhecimento?

Partilha baseia-se no controlo de acesso baseado em função do Azure (RBAC). Se pode compartilhar _qualquer_ recursos no Azure com outro utilizador, também pode partilhar o QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Se tiver um plano de serviço de aplicações com 5 bases de dados de conhecimento do QnAMaker. Pode atribuir direitos de leitura/escrita para 5 utilizadores diferentes para que cada um deles possa aceder apenas 1 base de dados de conhecimento do QnAMaker?

Pode partilhar um serviço QnAMaker completo, bases de dados de conhecimento não individuais.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Como posso alterar a mensagem predefinida quando for encontrada nenhuma correspondência bom?

A mensagem predefinida é parte integrante das definições no seu serviço de aplicações.
- Aceda ao seu recurso de serviço de aplicações no portal do Azure

![serviço de aplicações do qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique nas **definições** opção

![definições de serviço de aplicações do qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor do **DefaultAnswer** definição
- Reinicie o serviço de aplicações

![reiniciar o serviço de aplicações qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Por que minha ligação do SharePoint não é obter extraída?

Ver [localizações de origem de dados](./Concepts/knowledge-base.md#data-source-locations) para obter mais informações.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>As atualizações que fiz minha base de dados de conhecimento não são refletidas publicar. Porquê?

Cada operação de edição, seja numa atualização da tabela, teste ou definição, tem de ser guardadas antes de poderem ser publicadas. Certifique-se de clicar no botão **Guardar e treinar** após cada operação de edição.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Faz a base de dados de conhecimento suporta dados avançados ou a multimídia?

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática multimédia para ficheiros e URLs

* URLS - capacidade limitada de conversão HTML-to-Markdown.
* Ficheiros - não suportados

#### <a name="answer-text-in-markdown"></a>Responder texto em marcação
Uma vez que os conjuntos qnA estão na base de conhecimento, você pode editar o texto de marcação de uma resposta para incluir links para meios disponíveis a partir de URLs públicos.

### <a name="does-qna-maker-support-non-english-languages"></a>O Criador de FAQ suporta idiomas para além de inglês?

Ver mais detalhes [idiomas suportados](./Overview/languages-supported.md).

Se tiver conteúdo em vários idiomas, certifique-se de que cria um serviço separado para cada idioma.

## <a name="manage-service"></a>Gerir serviço

### <a name="when-should-i-restart-my-app-service"></a>Quando eu reiniciar o meu serviço de aplicações?

Atualizar o serviço de aplicações quando o ícone de cuidado estiver junto ao valor da versão para a base de dados de conhecimento no **chaves de ponto final** da tabela no **definições do utilizador** [página](https://www.qnamaker.ai/UserSettings).

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Apaguei o meu serviço de pesquisa existente. Como posso consertar isto?

Se eliminar um índice de Pesquisa Cognitiva Azure, a operação é definitiva e o índice não pode ser recuperado.

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Apaguei o meu índice de `testkb` no meu serviço de pesquisa. Como posso consertar isto?

Os seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie novamente a sua base de conhecimento.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando razão devo atualizar as minhas chaves de ponto final?

Atualize as suas chaves de ponto final, se suspeitar que tenham sido comprometidos.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Posso usar o mesmo recurso azure cognitive search para bases de conhecimento usando várias línguas?

Para utilizar múltiplos idiomas e várias bases de dados de conhecimento, o utilizador tem de criar um recurso do QnA Maker para cada idioma. Isto criará um serviço de pesquisa Azure separado por idioma. Misturar bases de dados de conhecimento do idioma diferente num serviço de pesquisa do Azure única irá resultar em degradação relevância dos resultados.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Como posso alterar o nome do recurso azure cognitive search usado pela QnA Maker?

O nome do recurso Azure Cognitive Search é o nome de recurso QnA Maker com algumas letras aleatórias anexadas no final. Isso dificulta distinguir entre os vários recursos de pesquisa para o QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como gostaria) e conecte-o ao seu Serviço QnA. Os passos são semelhantes aos passos que precisa de fazer para [atualizar uma pesquisa azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Quando a QnA Maker regressa `Runtime core is not initialized,` como posso consertá-lo?

O espaço do disco para o seu serviço de aplicações pode estar cheio. Passos para fixar o seu espaço de disco:

1. No [portal Azure,](https://portal.azure.com)selecione o serviço de Aplicações do Fabricante QnA e, em seguida, pare o serviço.
1. Enquanto ainda está no serviço app, selecione **Ferramentas**de Desenvolvimento , em **seguida, Ferramentas Avançadas**, em **seguida, Ir**. Isto abre uma nova janela do navegador.
1. Selecione **a consola Debug**e, em seguida, **CMD** para abrir uma ferramenta de linha de comando.
1. Navegue para o _site/wwwroot/Data/QnAMaker/_ diretório.
1. Remova todas as pastas cujo nome começa com `rd`.

    **Não apague** o seguinte:

    * Ficheiro KbIdToRankerMappings.txt
    * Ficheiro EndpointSettings.json
    * Pasta EndpointKeys

1. Inicie o serviço app.
1. Aceda à sua base de conhecimentos para verificar se funciona agora.


## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços incluindo Bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Preciso de utilizar o Bot Framework para poder utilizar o QnA Maker?

Não, não precisa de usar o [Quadro Bot](https://github.com/Microsoft/botbuilder-dotnet) com o Fabricante QnA. No entanto, o Fabricante QnA é oferecido como um dos vários modelos no [Serviço Bot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service permite um desenvolvimento rápido de bots inteligentes através do Microsoft Bot Framework, e é executado num ambiente sem servidor.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Como posso criar um novo bot com o QnA Maker?

Siga as instruções em [isso](./Tutorials/create-qna-bot.md) documentação para criar o seu Bot com o Azure Bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Como uso uma base de conhecimento diferente com um serviço de bots Azure existente?

Precisa de ter as seguintes informações sobre a sua base de conhecimentos:

* Identificação da base de conhecimento.
* O nome de subdomínio personalizado de endpoint publicado da base de conhecimento, conhecido como `host`, encontra-se na página **Definições** após a sua publicação.
* Chave final publicada da base de conhecimento - encontrada na página **Definições** após a publicação.

Com esta informação, vá ao serviço de aplicações do seu bot no portal Azure. Em **Definições -> Configuração -> Definições de aplicação,** altere esses valores.

A chave final da base de conhecimento está rotulada `QnAAuthkey` no serviço ABS.

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Duas ou mais aplicações de clientes podem partilhar uma base de conhecimento?

Sim, a base de conhecimento pode ser consultada por qualquer número de clientes. Se a resposta da base de conhecimento parecer lenta ou desativada, considere melhorar o nível de serviço para o serviço de aplicações associado à base de conhecimentos.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Como incorporo o serviço QnA Maker no meu site?

Siga estes passos para incorporar o serviço QnA Maker como um controle de web chat no seu Web site:

1. Criar o seu bot de FAQ, seguindo as instruções [aqui](./Tutorials/create-qna-bot.md).
2. Ativar o chat web ao seguir os passos [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Armazenamento de dados

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Que dados são armazenados e onde ele é armazenado?

Ao criar o serviço QnA Maker, selecionou uma região do Azure. As suas bases de dados de conhecimento e ficheiros de registo são armazenados nesta região.
