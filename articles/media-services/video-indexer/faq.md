---
title: Perguntas frequentes sobre Índice de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá respostas a perguntas frequentes sobre o Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 721af069a76497576b92c4203a93656d19c283aa
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217733"
---
# <a name="video-indexer-frequently-asked-questions"></a>Indexer de vídeo frequentemente fez perguntas

Este artigo responde frequentemente a perguntas sobre o Indexer de Vídeo.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer é um serviço de inteligência artificial que faz parte da Microsoft Azure Media Services. O Video Indexer fornece uma orquestração de vários modelos de machine learning que lhe permitem extrair facilmente uma visão profunda de um vídeo. Para fornecer informações avançadas e precisas, o Video Indexer utiliza vários canais do vídeo: áudio, fala e visual. Os insights do Video Indexer podem ser usados de muitas formas, como melhorar a descoberta de conteúdos e acessibilidade, criar novas oportunidades de rentabilização ou construir novas experiências que usem os insights. O Video Indexer fornece uma interface baseada na Web para testes, configuração e personalização de modelos na sua conta. Os desenvolvedores podem usar uma API baseada em REST para integrar o Video Indexer no sistema de produção. 

### <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Indexer de Vídeo?

Algumas das operações que o Video Indexer pode executar em ficheiros de mídia incluem:

* Identificar e extrair discursos e identificar os altifalantes.
* Identificar e extrair texto no ecrã num vídeo.
* Detetar objetos num ficheiro de vídeo.
* Identifique as marcas (por exemplo: Microsoft) a partir de faixas de áudio e texto no ecrã num vídeo.
* Detetar e reconhecer rostos de uma base de dados de celebridades e uma base de dados de rostos definida pelo utilizador.
* Extrair tópicos discutidos mas não necessariamente mencionados em conteúdos áudio e vídeo.
* Criando legendas fechadas ou legendas a partir da faixa de áudio.

Para obter mais informações e mais funcionalidades do Indexante de Vídeo, consulte [a Visão Geral](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Como é que começo com o Video Indexer?

O Video Indexer inclui uma oferta de teste gratuita que lhe proporciona 600 minutos na interface baseada na web e 2.400 minutos através da API. Pode [iniciar sessão na interface web do Video Indexer](https://www.videoindexer.ai/) e experimentá-la por si mesmo usando qualquer identidade web e sem ter de configurar uma Subscrição Azure. Siga [este laboratório de introdução fácil](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) para obter uma melhor ideia de como usar o Video Indexer.

Para indexar vídeos e moscas áudio em escala, pode ligar o Video Indexer a uma subscrição paga do Microsoft Azure. Pode encontrar mais informações sobre preços na página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Pode encontrar mais informações sobre como começar no [Get start](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Preciso de codificação para usar o Video Indexer?

Pode utilizar a interface web do Video Indexer para avaliar, configurar e gerir a sua conta **sem necessidade de codificação.**  Quando estiver pronto para desenvolver aplicações mais complexas, pode utilizar a [API do Indexer de Vídeo](https://api-portal.videoindexer.ai/) para integrar o Video Indexer nas suas próprias aplicações, web sites ou [fluxos de trabalho personalizados utilizando tecnologias sem servidor, como apps Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) ou Funções Azure.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Preciso de habilidades de aprendizagem automática para usar o Video Indexer?

Não, o Video Indexer proporciona a integração de vários modelos de aprendizagem automática num único oleoduto. A indexação de um ficheiro de vídeo ou áudio através do Video Indexer recupera um conjunto completo de insights extraídos numa linha de tempo partilhada sem qualquer capacidade de aprendizagem automática ou conhecimento sobre algoritmos necessários por parte do cliente.

### <a name="what-media-formats-does-video-indexer-support"></a>Que formatos de mídia suportam o Video Indexer?

O Indexer de Vídeo suporta os formatos de mídia mais comuns. Consulte a lista [de formatos padrão Azure Media Encoder](../latest/media-encoder-standard-formats.md) para obter mais detalhes.

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Como faço o upload de um ficheiro de media para o Video Indexer e quais são as limitações?

No portal baseado na web do Índice de Vídeo Indexer, pode fazer o upload de um ficheiro de mídia utilizando o diálogo de upload de ficheiros ou apontando para um URL que acolhe diretamente o ficheiro de origem (ver [exemplo).](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4) Qualquer URL que acolhia o conteúdo do meio-dia utilizando um código iFrame ou incorporado não funcionará (ver [exemplo).](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) 

Para mais informações, por favor leia as [boas-na-voz.](./upload-index-videos.md)

#### <a name="limitations"></a>Limitações

* O nome do vídeo não pode exceder 80 caracteres.
* Se carregar um vídeo utilizando a matriz byte, o tamanho do vídeo está limitado a 2GB (e 30GB ao utilizar URL). 

Para a lista completa, consulte [as considerações e limitações de upload](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Quanto tempo demora o Video Indexer a extrair informações dos meios de comunicação?

O tempo necessário para indexar um ficheiro de vídeo ou áudio, tanto utilizando a API do Indexer de Vídeo como a interface web do Indexer de Vídeo, depende de vários parâmetros, como o comprimento e qualidade do ficheiro, o número de insights encontrados no ficheiro, o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md) disponíveis e se o [ponto final de streaming](../previous/media-services-streaming-endpoints-overview.md) está ativado ou não. Recomendamos que execute alguns ficheiros de teste com o seu próprio conteúdo e tome uma média para ter uma ideia melhor.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Posso criar fluxos de trabalho personalizados para automatizar processos com Índice de Vídeo?

Sim, pode integrar o Indexer de Vídeo em tecnologias sem servidor, como Apps Lógicas, Flow e [Azure Functions](https://azure.microsoft.com/services/functions/). Pode encontrar mais detalhes sobre os conectores [Logic App](https://azure.microsoft.com/services/logic-apps/) e [Flow](https://flow.microsoft.com/en-us/) para Índice de Vídeo [aqui.](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) Pode ver alguns projetos de automação realizados por parceiros no [repo video indexer Samples.](https://github.com/Azure-Samples/media-services-video-indexer)

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Em que regiões de Azure está disponível indexador de vídeo?

Pode ver quais regiões Azure Video Indexer está disponível na página das [regiões.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Posso personalizar os modelos de Indexer de Vídeo para o meu caso de utilização específica? 

Sim. No Video Indexer pode personalizar alguns dos modelos disponíveis para melhor se adaptar às suas necessidades. 

Por exemplo, o nosso modelo Pessoa suporta 1.000.000 rostos fora da caixa de reconhecimento de celebridades, mas também pode treiná-lo para reconhecer outras caras que não estão nessa base de dados. 

Para mais detalhes, consulte artigos sobre personalizar modelos [pessoais,](customize-person-model-overview.md) [marcas](customize-brands-model-overview.md)e [idiomas.](customize-language-model-overview.md) 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Posso editar os vídeos na minha biblioteca?

Sim. Prima o botão de **vídeo de edição** a partir do visor da biblioteca ou o botão **de editor aberto** a partir do visor do leitor para chegar ao separador **Projetos.** Pode criar um novo projeto e adicionar mais vídeos da sua biblioteca para editá-los juntos, uma vez que você está feito você pode renderizar o seu vídeo e baixar. 

Se quiser obter informações sobre o seu novo vídeo, indexe-o ao Video Indexer e aparecerá na sua biblioteca com as suas ideias.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Posso indexar vários streams de áudio ou canais?

Se houver vários streams de áudio, o Video Indexer toma o primeiro que encontra e processará apenas este fluxo. Em qualquer fluxo de áudio Os processos do Indexer de Vídeo, toma os diferentes canais (se presentes) e processa-os em conjunto como mono. Para a manipulação de streams/canais, pode utilizar comandos ffmpeg no ficheiro antes de o indexar.

### <a name="what-is-the-sla-for-video-indexer"></a>O que é o SLA para Índice de Vídeo?

O SLA do Azure Media Service cobre o Video Indexer e pode ser encontrado na página [SLA.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) O SLA aplica-se apenas às contas pagas do Video Indexer e não se aplica ao teste gratuito.

## <a name="privacy-questions"></a>Questões de Privacidade

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Os ficheiros de vídeo e áudio são indexados pelo Video Indexer?

Sim, a menos que elimine o ficheiro do Video Indexer, utilizando o website do Índice de Vídeo ou a API, os seus ficheiros de vídeo e áudio são armazenados. Para o teste gratuito, os ficheiros de vídeo e áudio que indexa são armazenados na região de Azure Leste dos EUA. Caso contrário, os seus ficheiros de vídeo e áudio são armazenados na conta de armazenamento da sua subscrição Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Posso eliminar os meus ficheiros que estão armazenados no Portal do Indexante de Vídeo?

Sim, pode sempre eliminar os seus ficheiros de vídeo e áudio, bem como quaisquer metadados e insights extraídos dos mesmos pelo Video Indexer. Uma vez eliminado um ficheiro do Indexer de Vídeo, o ficheiro e os seus metadados e insights são permanentemente removidos do Indexador de Vídeo. No entanto, se implementou a sua própria solução de backup no armazenamento Azure, o ficheiro permanece no seu armazenamento Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Posso controlar o acesso dos utilizadores à minha conta de Indexer de Vídeo?

Sim, apenas os administradores de conta podem convidar e desconvidar as pessoas para as suas contas, bem como atribuir quem tem privilégios de edição e quem tem acesso apenas de leitura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Quem tem acesso aos meus ficheiros de vídeo e áudio que foram indexados e/ou armazenados pelo Video Indexer e pelos metadados e insights que foram extraídos?

O seu conteúdo de vídeo ou áudio que tenha público como definição de privacidade pode ser acedido por qualquer pessoa que tenha a ligação com o seu conteúdo de vídeo ou áudio e as suas informações. O conteúdo de vídeo ou áudio que tenha como configuração de privacidade apenas pode ser acedido por utilizadores que tenham sido convidados para a conta do conteúdo de vídeo ou áudio. A definição de privacidade do seu conteúdo também se aplica aos metadados e insights que o Indexer de Vídeo extrai. Atribua a definição de privacidade quando faz o upload do seu ficheiro de vídeo ou áudio. Também pode alterar a definição de privacidade após a indexação.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Que acesso tem a Microsoft aos meus ficheiros de vídeo ou áudio que tenham sido indexados e/ou armazenados pelo Video Indexer e pelos metadados e insights extraídos?

De acordo com os Termos de Serviços Online do [Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), possui-se completamente do seu conteúdo, e a Microsoft apenas acederá aos seus conteúdos e aos metadados e insights que o Video Indexer extrai do seu conteúdo de acordo com o OST e a Declaração de Privacidade da Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Os modelos personalizados que construo na minha conta de Video Indexer estão disponíveis para outras contas?

 Não, os modelos personalizados que cria na sua conta não estão disponíveis para qualquer outra conta. O Video Indexer atualmente permite-lhe construir [marcas](customize-brands-model-overview.md)personalizadas, [idiomas](customize-language-model-overview.md)e modelos [pessoais](customize-person-model-overview.md) na sua conta. Estes modelos só estão disponíveis na conta em que criou os modelos.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>O conteúdo indexado pelo Video Indexer é mantido dentro da região de Azure onde estou a usar o Video Indexer?

Sim, o conteúdo e os seus insights são mantidos dentro da região Azure, a menos que tenha uma configuração manual na sua subscrição Azure que utiliza várias regiões do Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Qual é a política de privacidade para o Video Indexer?

O Indexer de Vídeo é abrangido pela Declaração de Privacidade da [Microsoft.](https://privacy.microsoft.com/privacystatement) A declaração de privacidade explica os dados pessoais que a Microsoft processa, como a Microsoft o processa e para que finalidades a Microsoft o processa. Para saber mais sobre privacidade, visite o [Microsoft Trust Center.](https://www.microsoft.com/trustcenter)

### <a name="what-certifications-does-video-indexer-have"></a>Que certificações tem o Video Indexer?

O Video Indexer tem atualmente a certificação SOC. Para rever a certificação do Video Indexer, consulte o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Qual é a diferença entre vídeos privados e públicos? 

Quando os vídeos são enviados para o Video Indexer, pode escolher entre duas definições de privacidade: privada e pública. Os vídeos públicos são acessíveis a qualquer pessoa, incluindo utilizadores anónimos e não identificados. Os privados são restritos exclusivamente aos membros da conta. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Tentei fazer o upload de um vídeo como público e foi sinalizado por conteúdo inapropriado ou ofensivo, o que significa isso? 

Ao fazer o upload de um vídeo para o Video Indexer, uma análise automática de conteúdos é feita por algoritmos e modelos para garantir que nenhum conteúdo impróprio será apresentado publicamente. Se um vídeo for considerado suspeito como contendo conteúdo explícito, não será possível defini-lo como público. No entanto, os membros da conta ainda podem aceder-lhe como um vídeo privado (ver, descarregar os insights e artefactos extraídos, e realizar outras operações disponíveis para os membros da conta).   

Para definir o vídeo para acesso público, você pode: 

* Construa a sua própria camada de interface (como app ou website) e use-a para interagir com o serviço de Indexer de Vídeo. Desta forma, o vídeo permanece privado no nosso portal e os seus utilizadores podem interagir com ele através da sua interface. Por exemplo, ainda pode obter as ideias ou permitir visualizar o vídeo na sua própria interface. 
* Solicite uma revisão humana do conteúdo, o que resultaria na eliminação da restrição, assumindo que o conteúdo não é explícito. 

    Esta opção pode ser explorada se o website do Índice de Vídeo for utilizado diretamente pelos seus utilizadores como camada de interface e para visualização pública (não autenticada). 

## <a name="api-questions"></a>Perguntas da API

### <a name="what-apis-does-video-indexer-offer"></a>Que APIs oferece o Indexer de Vídeo?

As APIs do Indexante de Vídeo permitem a indexação, extração de metadados, gestão de ativos, tradução, incorporação, personalização de modelos e muito mais. Para obter informações mais detalhadas sobre a utilização da API do Indexador de Vídeo, consulte o [Portal de Desenvolvedores de Indexados de Vídeo](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Que cliente sDKs oferece Índicer de Vídeo?

Atualmente, não existem SDKs de clientes oferecidos. A equipa do Video Indexer está a trabalhar nos SDKs e planeia entregá-los em breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Como é que começo com a API do Indexante de Vídeo?

Siga [tutorial: começa com a API do Indexante de Vídeo](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Qual é a diferença entre a API indexante de vídeo e o Azure Media Service v3 API?

Atualmente existem algumas sobreposições nas funcionalidades oferecidas pela API indexante de vídeo e pelo Azure Media Service v3 API. Pode encontrar mais informações sobre como comparar ambos os serviços [aqui.](compare-video-indexer-with-media-services-presets.md)

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>O que é um símbolo de acesso da API e por que preciso?

A API do Indexante de Vídeo contém uma API de Autorização e uma API de Operações. A API de Autorizações contém chamadas que lhe dão acesso a ficha. Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

São necessários tokens de acesso para utilizar as APIs do Indexador de Vídeo para fins de segurança. Isto garante que quaisquer chamadas estão a chegar de si ou de quem tem permissões de acesso à sua conta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Qual é a diferença entre o token de acesso à conta, o token de acesso ao utilizador e o token de acesso de vídeo?

* Nível de conta – os tokens de acesso de nível de conta permitem realizar operações ao nível de conta ou de vídeo. Por exemplo, faça upload de um vídeo, liste todos os vídeos, obtenha informações de vídeo.
* Nível de utilizador - fichas de acesso ao nível do utilizador permitem-lhe executar operações ao nível do utilizador. Permitem, por exemplo, obter contas associadas.
* Nível de vídeo – os tokens de acesso de nível de vídeo permitem realizar operações num vídeo específico. Permitem, por exemplo, obter informações de vídeo, transferir legendas, obter widgets, etc.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Quantas vezes preciso de um novo sinal de acesso? Quando é que os tokens de acesso expiram?

Os tokens de acesso expiram a cada hora, por isso tens de gerar um novo token de acesso a cada hora. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Quais são as opções de login do portal de desenvolvimento do Indexer de Vídeo?

Pode iniciar sessão usando Azure AD, conta Microsoft, conta Google ou conta do Facebook. 

Uma vez que registe a sua conta de e-mail utilizando um fornecedor de identidade, não poderá utilizar esta conta de e-mail com outro fornecedor de identidade.

## <a name="billing-questions"></a>Perguntas sobre faturação

### <a name="how-much-does-video-indexer-cost"></a>Quanto custa o Indexante de Vídeo?

O Video Indexer utiliza um modelo simples de preços pay-as-you-go com base na duração da entrada de conteúdo que indexa. Podem ser aplicados encargos adicionais para codificação, streaming, armazenamento, utilização da rede e unidades reservadas aos meios de comunicação. Para mais informações, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando é que estou a ser cobrado por usar o Video Indexer?

Ao enviar um vídeo a ser indexado, o utilizador definirá o indexante para ser análise de vídeo, análise áudio ou ambos. Isto determinará quais os SKUs que serão cobrados. Se houver um erro de nível crítico durante o processamento, um código de erro será devolvido como resposta. Em tal caso, não ocorre nenhuma faturação.  Um erro crítico pode ser causado por um erro no nosso código ou por uma falha crítica numa dependência interna que o serviço tem. Erros como identificação errada ou extração de insights não são considerados críticos e uma resposta é devolvida. Em qualquer caso, quando uma resposta válida (código de não erro) é devolvida, a faturação ocorre.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>O Video Indexer oferece um teste gratuito?

Sim, o Video Indexer oferece um teste gratuito que dá serviço completo e funcionalidade de API. Existe uma quota de 600 minutos de vídeos para utilizadores de interface baseados na Web e 2.400 minutos para utilizadores de API. 

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
