---
title: Solução de problemas-QnA Maker
titleSuffix: Azure Cognitive Services
description: A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795433"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas para QnA Maker

A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Como obter o ponto de extremidade de serviço QnAMaker

O ponto de extremidade do serviço QnAMaker é útil para fins de depuração quando você entra em contato com o suporte do QnAMaker ou UserVoice. O ponto de extremidade é uma URL neste formato: https://your-resource-name.azurewebsites.net.
    
1. Vá para o serviço QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![QnAMaker o grupo de recursos do Azure no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o serviço de aplicativo associado ao recurso de QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o serviço de aplicativo QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A URL do ponto de extremidade está disponível na seção visão geral

    ![Ponto de extremidade QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Usar o bot de ajuda no portal de QnA Maker

QnA Maker fornece um bot de **ajuda** dentro do portal de QnA Maker para ajudá-lo. O bot da ajuda está disponível em todas as páginas da Web. O bot usa QnA Maker para fornecer respostas e fornece o [ C# projeto de código do bot Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) ao bot para que você possa entrar em funcionamento rapidamente com seu próprio bot de resposta. 

![! [QnA Maker fornece um bot * * Help * * no portal QnA Maker para ajudá-lo.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gerenciar a base de dados de conhecimento

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Excluí acidentalmente uma parte do meu QnA Maker, o que devo fazer? 

Não exclua nenhum dos serviços do Azure criados junto com o QnA Maker recurso, como pesquisa ou aplicativo Web. Eles são necessários para que QnA Maker funcionem, se você excluir um, QnA Maker deixará de funcionar corretamente.

Todas as exclusões são permanentes, incluindo pares de perguntas e respostas, arquivos, URLs, perguntas e respostas personalizadas, bases de dados de conhecimento ou recursos do Azure. Certifique-se de exportar sua base de dados de conhecimento na página **configurações** antes de excluir qualquer parte da sua base de dados de conhecimento. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Por que minhas URLs (s)/File (s) não extraindo pares de resposta de pergunta?

É possível que QnA Maker não possa extrair automaticamente um conteúdo de QnA (pergunta e resposta) de URLs de perguntas frequentes válidas. Nesses casos, você pode colar o conteúdo do QnA em um arquivo. txt e ver se a ferramenta pode ingerir isso. Como alternativa, você pode adicionar conteúdo de forma editorial à sua base de dados de conhecimento por meio do [portal de QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Que tamanho pode ter a base de dados de conhecimento que eu criar?

O tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolher ao criar o serviço de QnA Maker. Leia [aqui](./Tutorials/choosing-capacity-qnamaker-deployment.md) para obter mais detalhes.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Por que não consigo ver nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?

Você ainda não criou nenhum serviço de QnA Maker no Azure. Leia [aqui](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Como fazer compartilhar uma base de dados de conhecimento com outras pessoas?

O compartilhamento funciona no nível de um serviço de QnA Maker, ou seja, todas as bases de conhecimento no serviço serão compartilhadas. Leia [aqui](./How-To/collaborate-knowledge-base.md) como colaborar em uma base de dados de conhecimento.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Você pode compartilhar uma base de dados de conhecimento com um colaborador que não está no mesmo locatário do AAD, para modificar uma base de dados de conhecimento? 

O compartilhamento é baseado no RBAC (controle de acesso baseado em função) do Azure. Se você puder compartilhar _qualquer_ recurso no Azure com outro usuário, também poderá compartilhar QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Se você tiver um plano do serviço de aplicativo com 5 bases de dados de conhecimento QnAMaker. Você pode atribuir direitos de leitura/gravação a 5 usuários diferentes para que cada um deles possa acessar apenas uma base de dados de conhecimento QnAMaker?

Você pode compartilhar um serviço QnAMaker inteiro, não as bases de conhecimento individuais.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Como posso alterar a mensagem padrão quando nenhuma correspondência adequada for encontrada?

A mensagem padrão faz parte das configurações em seu serviço de aplicativo.
- Vá para o recurso do serviço de aplicativo no portal do Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **configurações**

![configurações do qnamaker appservice](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da configuração **Defaultanswer**
- Reiniciar o serviço de aplicativo

![qnamaker appservice Restart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Por que meu link do SharePoint não está sendo extraído?

Consulte [locais de fonte de dados](./Concepts/data-sources-supported.md#data-source-locations) para obter mais informações.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>As atualizações que fiz na base de dados de conhecimento não são refletidas na publicação. Porquê?

Cada operação de edição, seja em uma atualização de tabela, teste ou configuração, precisa ser salva antes que possa ser publicada. Certifique-se de clicar no botão **salvar e treinar** após cada operação de edição.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A base de conhecimento oferece suporte a dados avançados ou multimídia?

A base de dados de conhecimento suporta Markdown. No entanto, a extração automática de URLs tem capacidade limitada de conversão de HTML para redução. Se você quiser usar a redução completa, poderá modificar o conteúdo diretamente na tabela ou carregar uma base de dados de conhecimento com o conteúdo avançado.

Não há suporte para multimídia, como imagens e vídeos no momento.

### <a name="does-qna-maker-support-non-english-languages"></a>O Criador de FAQ suporta idiomas para além de inglês?

Veja mais detalhes sobre os [idiomas com suporte](./Overview/languages-supported.md).

Se tiver conteúdo em vários idiomas, certifique-se de que cria um serviço separado para cada idioma.

## <a name="manage-service"></a>Gerir serviço

### <a name="when-should-i-restart-my-app-service"></a>Quando devo reiniciar meu serviço de aplicativo? 

Atualize seu serviço de aplicativo quando o ícone de cuidado estiver próximo ao valor da versão da base de dados de conhecimento na tabela **chaves de ponto de extremidade** na [página](https://www.qnamaker.ai/UserSettings) **configurações do usuário** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Excluí meu serviço de pesquisa existente. Como posso corrigir isso?

Se você excluir um índice de Pesquisa Cognitiva do Azure, a operação será final e o índice não poderá ser recuperado. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Excluí meu índice `testkb` no meu serviço de pesquisa. Como posso corrigir isso? 

Seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie sua base de dados de conhecimento novamente.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando devo atualizar minhas chaves de ponto de extremidade?

Atualize suas chaves de ponto de extremidade se suspeitar que elas foram comprometidas.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Posso usar o mesmo recurso de Pesquisa Cognitiva do Azure para bases de dados de conhecimento usando vários idiomas?

Para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso de QnA Maker para cada idioma. Isso criará um serviço do Azure Search separado por idioma. Misturar bases de conhecimento de idioma diferentes em um único serviço Azure Search resultará em degradação dos resultados.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Como posso alterar o nome do recurso de Pesquisa Cognitiva do Azure usado pelo QnA Maker?

O nome do recurso de Pesquisa Cognitiva do Azure é o nome do recurso de QnA Maker com algumas letras aleatórias anexadas no final. Isso torna difícil distinguir entre vários recursos de pesquisa para QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como você gostaria) e conecte-o ao serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Quando QnA Maker retorna `Runtime core is not initialized,` como corrigi-lo?

O espaço em disco para o serviço de aplicativo pode estar cheio. Etapas para corrigir o espaço em disco:

1. Na [portal do Azure](https://portal.azure.com), selecione o serviço de aplicativo do QnA Maker e interrompa o serviço.
1. Enquanto ainda estiver no serviço de aplicativo, selecione **ferramentas de desenvolvimento**, depois **ferramentas avançadas**e, em seguida, **vá**. Isso abre uma nova janela do navegador.
1. Selecione **console de depuração**e, em seguida, **cmd** para abrir uma ferramenta de linha de comando. 
1. Navegue até o diretório _site/wwwroot/data/QnAMaker/_ .
1. Remova todas as pastas cujo nome começa com `rd`. 

    **Não exclua** o seguinte:

    * Arquivo KbIdToRankerMappings. txt
    * Arquivo EndpointSettings. JSON
    * Pasta EndpointKeys 

1. Inicie o serviço de aplicativo.
1. Acesse sua base de dados de conhecimento para verificar se ela funciona agora. 


## <a name="integrate-with-other-services-including-bots"></a>Integre com outros serviços, incluindo bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Preciso de utilizar o Bot Framework para poder utilizar o QnA Maker?

Não, você não precisa usar o [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com QnA Maker. No entanto, QnA Maker é oferecida como um dos vários modelos no [serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). O serviço de bot permite um rápido desenvolvimento inteligente de bot por meio do Microsoft bot Framework e é executado em um ambiente sem servidor.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Como posso criar um novo bot com QnA Maker?

Siga as instruções nesta [documentação para](./Tutorials/create-qna-bot.md) criar o bot com o serviço de bot do Azure.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Como fazer usar uma base de dados de conhecimento diferente com um serviço de bot do Azure existente?

Você precisa ter as seguintes informações sobre sua base de dados de conhecimento:

* ID da base de dados de conhecimento.
* Nome de subdomínio personalizado do ponto de extremidade publicado da base de dados de conhecimento, conhecido como `host`, encontrado na página de **configurações** após a publicação.
* Chave de ponto de extremidade publicada da base de dados de conhecimento-encontrada na página de **configurações** após a publicação. 

Com essas informações, vá para o serviço de aplicativo do bot no portal do Azure. Em **configurações-> configuração – > configurações do aplicativo**, altere esses valores.  

A chave do ponto de extremidade da base de dados de conhecimento é rotulada `QnAAuthkey` no serviço ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Dois ou mais aplicativos cliente podem compartilhar uma base de dados de conhecimento? 

Sim, a base de dados de conhecimento pode ser consultada de qualquer número de clientes. Se a resposta da base de dados de conhecimento parecer ser lenta ou atingir o tempo limite, considere atualizar a camada de serviço para o serviço de aplicativo associado à base de dados de conhecimento.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Como incorporo o serviço QnA Maker no meu site?

Siga estas etapas para inserir o serviço de QnA Maker como um controle de chat da Web em seu site:

1. Crie o bot de perguntas frequentes seguindo as instruções [aqui](./Tutorials/create-qna-bot.md).
2. Habilite o chat Web seguindo as etapas [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Armazenamento de dados

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Quais dados são armazenados e onde são armazenados? 

Ao criar seu serviço de QnA Maker, você selecionou uma região do Azure. As bases de dados de conhecimento e os arquivos de log são armazenados nessa região. 
