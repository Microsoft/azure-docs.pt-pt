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
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 95b4978b66accce0182e58493cb9d600dbe5e35f
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277490"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas para QnA Maker

A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.


## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de anfitrião do serviço do QnAMaker

O nome de anfitrião do QnAMaker serviço é útil para fins de depuração quando contactar o suporte de QnAMaker ou UserVoice. O nome do host é uma URL neste formato: https:// *{hostname}* . azurewebsites.net.
    
1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o serviço de aplicativo associado ao recurso de QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o serviço de aplicações do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL de nome de anfitrião está disponível na seção de visão geral

    ![Nome de anfitrião do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Usar o bot de ajuda no portal de QnA Maker

QnA Maker fornece um bot de **ajuda** dentro do portal de QnA Maker para ajudá-lo. O bot da ajuda está disponível em todas as páginas da Web. O bot usa QnA Maker para fornecer respostas e fornece o [ C# projeto de código do bot Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) ao bot para que você possa entrar em funcionamento rapidamente com seu próprio bot de resposta. 

![! [QnA Maker fornece um bot * * Help * * no portal QnA Maker para ajudá-lo.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gerir a base de dados de conhecimento

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Eliminei acidentalmente uma parte do meu QnA Maker, o que devo fazer? 

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

Ver [localizações de origem de dados](./Concepts/data-sources-supported.md#data-source-locations) para obter mais informações.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>As atualizações que fiz minha base de dados de conhecimento não são refletidas publicar. Porquê?

Cada operação de edição, seja numa atualização da tabela, teste ou definição, tem de ser guardadas antes de poderem ser publicadas. Certifique-se de que clique a **guardar e treinar** botão após cada operação de edição.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Faz a base de dados de conhecimento suporta dados avançados ou a multimídia?

A base de dados de conhecimento suporta Markdown. No entanto, a extração automática a partir de URLs limitou a capacidade de conversão de HTML para Markdown. Se pretender utilizar o Markdown completo, pode modificar o seu conteúdo diretamente na tabela, ou carregar uma base de dados de conhecimento com o conteúdo.

Multimídia, como imagens e vídeos, não é suportada neste momento.

### <a name="does-qna-maker-support-non-english-languages"></a>O QnA Maker suporta idiomas inglês?

Ver mais detalhes [idiomas suportados](./Overview/languages-supported.md).

Se tiver conteúdo a partir de vários idiomas, certifique-se de que criar um serviço separado para cada idioma.

## <a name="manage-service"></a>Gerir serviço

### <a name="when-should-i-restart-my-app-service"></a>Quando eu reiniciar o meu serviço de aplicações? 

Atualizar o serviço de aplicações quando o ícone de cuidado estiver junto ao valor da versão para a base de dados de conhecimento no **chaves de ponto final** da tabela no **definições do utilizador** [página](https://www.qnamaker.ai/UserSettings).

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Excluí meu serviço de pesquisa existente. Como posso corrigir isso?

Se você excluir um índice de Azure Search, a operação será final e o índice não poderá ser recuperado. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Excluí meu `testkb` índice no meu serviço de pesquisa. Como posso corrigir isso? 

Seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie sua base de dados de conhecimento novamente.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando razão devo atualizar as minhas chaves de ponto final?

Atualize as suas chaves de ponto final, se suspeitar que tenham sido comprometidos.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Pode utilizar o mesmo recurso do Azure Search para bases de dados de conhecimento com vários idiomas?

Para utilizar múltiplos idiomas e várias bases de dados de conhecimento, o utilizador tem de criar um recurso do QnA Maker para cada idioma. Isso criará um serviço do Azure Search separado por idioma. Misturar bases de dados de conhecimento do idioma diferente num serviço de pesquisa do Azure única irá resultar em degradação relevância dos resultados.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Como posso alterar o nome do recurso do Azure Search utilizado pela ferramenta QnA Maker?

O nome do recurso do Azure Search é o nome de recurso do QnA Maker com algumas letras aleatórios anexadas ao final. Isso dificulta distinguir entre os vários recursos de pesquisa para o QnA Maker. Criar um serviço separado do Azure Search (nomeando-a forma como gostaria de) e ligá-la ao seu serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-search-service).

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


## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços incluindo Bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Preciso de utilizar o Bot Framework para poder utilizar o QnA Maker?

Não, você não precisa usar o [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com QnA Maker. No entanto, QnA Maker é oferecida como um dos vários modelos no [serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service permite um desenvolvimento rápido de bots inteligentes através do Microsoft Bot Framework, e é executado num ambiente sem servidor.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Como posso criar um novo bot com QnA Maker?

Siga as instruções em [isso](./Tutorials/create-qna-bot.md) documentação para criar o seu Bot com o Azure Bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Como fazer usar uma base de dados de conhecimento diferente com um serviço de bot do Azure existente?

Você precisa ter as seguintes informações sobre sua base de dados de conhecimento:

* ID da base de dados de conhecimento.
* Nome do host do ponto de extremidade publicado da base de dados de conhecimento-encontrado na página de **configurações** após a publicação.
* Chave de ponto de extremidade publicada da base de dados de conhecimento-encontrada na página de **configurações** após a publicação. 

Com essas informações, vá para o serviço de aplicativo do bot no portal do Azure. Em **configurações-> configuração – > configurações do aplicativo**, altere esses valores.  

A chave do ponto de extremidade da base de `QnAAuthkey` dados de conhecimento é rotulada no serviço ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Dois ou mais aplicativos cliente podem compartilhar uma base de dados de conhecimento? 

Sim, a base de dados de conhecimento pode ser consultada de qualquer número de clientes. Se a resposta da base de dados de conhecimento parecer ser lenta ou atingir o tempo limite, considere atualizar a camada de serviço para o serviço de aplicativo associado à base de dados de conhecimento.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Como incorporo o serviço QnA Maker no meu site?

Siga estes passos para incorporar o serviço QnA Maker como um controle de web chat no seu Web site:

1. Criar o seu bot de FAQ, seguindo as instruções [aqui](./Tutorials/create-qna-bot.md).
2. Ativar o chat web ao seguir os passos [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Armazenamento de dados

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Que dados são armazenados e onde ele é armazenado? 

Ao criar o serviço QnA Maker, selecionou uma região do Azure. As suas bases de dados de conhecimento e ficheiros de registo são armazenados nesta região. 
