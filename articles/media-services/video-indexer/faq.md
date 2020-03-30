---
title: Perguntas frequentes sobre o Indexer de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá respostas a perguntas frequentes sobre o Indexer de Vídeo da Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: eacbeca275192e1a68b6682c3036da2d5c09bd54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619878"
---
# <a name="video-indexer-frequently-asked-questions"></a>Indexer de vídeo frequentemente fez perguntas

Este artigo responde frequentemente a perguntas sobre o Indexer de Vídeo.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer é um serviço de inteligência artificial que faz parte da Microsoft Azure Media Services. O Indexer de vídeo fornece uma orquestração de vários modelos de machine learning que lhe permitem extrair facilmente uma visão profunda de um vídeo. Para fornecer informações avançadas e precisas, o Indexer de Vídeo faz uso de vários canais do vídeo: áudio, fala e visual. Os insights do Indexer de vídeo podem ser usados de muitas maneiras, como melhorar a descoberta de conteúdos e acessibilidade, criar novas oportunidades de rentabilização, ou construir novas experiências que usem os insights. O Indexer de Vídeo fornece uma interface baseada na Web para testar, configurar e personalizar modelos na sua conta. Os desenvolvedores podem usar uma API baseada em REST para integrar o Indexer de Vídeo no sistema de produção. 

### <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Indexer de Vídeo?

Algumas das operações que o Video Indexer pode realizar em ficheiros de mídia incluem:

* Identificar e extrair discursos e identificar oradores.
* Identificar e extrair texto no ecrã num vídeo.
* Detetando objetos num ficheiro de vídeo.
* Identifique as marcas (por exemplo: Microsoft) a partir de faixas de áudio e texto sonoro num vídeo.
* Detetar e reconhecer rostos de uma base de dados de celebridades e uma base de dados de rostos definidos pelo utilizador.
* Extrair tópicos discutidos mas não necessariamente mencionados em conteúdos áudio e vídeo.
* Criação de legendas fechadas ou legendas da faixa de áudio.

Para mais informações e mais funcionalidades do Indexer de Vídeo, consulte [a visão geral](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Como posso começar com o Indexer de Vídeo?

O Indexer de Vídeo inclui uma oferta de teste gratuito que lhe proporciona 600 minutos na interface baseada na web e 2.400 minutos através da API. Pode [iniciar sessão na interface web do Indexer de vídeo](https://www.videoindexer.ai/) e experimentá-la por si mesmo usando qualquer identidade web e sem ter de configurar uma Subscrição Azure. 

Para indexar vídeos e moscas de áudio em escala, pode ligar o Indexer de Vídeo a uma subscrição paga do Microsoft Azure. Pode encontrar mais informações sobre os preços na página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Você pode encontrar mais informações sobre começar em [Get started](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Preciso de habilidades de codificação para usar o Indexer de Vídeo?

Pode utilizar a interface baseada na web do Indexer de Vídeo para avaliar, configurar e gerir a sua conta **sem necessidade**de codificação .  Quando estiver pronto para desenvolver aplicações mais complexas, pode utilizar a API do [Indexer](https://api-portal.videoindexer.ai/) de Vídeo para integrar o Indexer de Vídeo nas suas próprias aplicações, web sites ou fluxos de [trabalho personalizados utilizando tecnologias sem servidor, como aplicações lógicas do Azure](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) ou funções azure.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Preciso de habilidades de aprendizagem automática para usar o Indexer de Vídeo?

Não, o Video Indexer fornece a integração de múltiplos modelos de machine learning num só pipeline. Indexar um ficheiro de vídeo ou áudio através do Video Indexer recupera um conjunto completo de insights extraídos numa linha temporal partilhada sem quaisquer habilidades de aprendizagem automática ou conhecimento sobre algoritmos necessários por parte do cliente.

### <a name="what-media-formats-does-video-indexer-support"></a>Que formatos de mídia suportam o Video Indexer?

O Indexer de vídeo suporta os formatos mais comuns dos media. Consulte a lista de [formatos padrão Do Encoder Azure Media](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) para mais detalhes.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Como fazer o upload de um meio de comunicação para o Indexer de Vídeo?

No portal baseado na web do Indexer de vídeo, pode carregar um ficheiro de mídia utilizando o diálogo de upload de ficheiros ou apontando para um URL que acolhe diretamente o ficheiro fonte (ver [exemplo).](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4) Qualquer URL que acolhe o conteúdo dos meios de comunicação utilizando um código iFrame ou incorporado não funcionará (ver [exemplo).](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) A API do Indexante de Vídeo requer que especifique o ficheiro de entrada através de um URL ou de um matriz byte. Os uploads através de um URL utilizando a API estão limitados a 10 GB, mas não têm um limite de duração temporal. Para mais informações, por favor leia as informações sobre [o seu melhor](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Quanto tempo demora o Indexer de Vídeo a extrair informações dos meios de comunicação?

O tempo que demora a indexar um ficheiro de vídeo ou áudio, tanto utilizando a API do Indexer de Vídeo como a interface web do Indexer de Vídeo, depende de múltiplos parâmetros, como o comprimento e a qualidade do ficheiro, o número de insights encontrados no ficheiro, o número de [unidades reservadas](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) disponíveis e se o [ponto final](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) de streaming está ou não ativado. Recomendamos que execute alguns ficheiros de teste com o seu próprio conteúdo e tome uma média para ter uma ideia melhor.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Posso criar fluxos de trabalho personalizados para automatizar processos com O Indexante de Vídeo?

Sim, pode integrar o Indexer de Vídeo em tecnologias sem servidores como Aplicações Lógicas, Fluxo e [Funções Azure.](https://azure.microsoft.com/services/functions/) Pode encontrar mais detalhes sobre a [Aplicação Lógica](https://azure.microsoft.com/services/logic-apps/) e conectores [Flow](https://flow.microsoft.com/en-us/) para Indexer de Vídeo [aqui](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Em que as regiões azure estão disponíveis indexadores de vídeo?

Pode ver quais as regiões azure Video Indexer estão disponíveis na página das [regiões.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Posso personalizar os modelos do Indexer de Vídeo para o meu caso de uso específico? 

Sim. No Indexer de Vídeo pode personalizar alguns dos modelos disponíveis para melhor se adaptar às suas necessidades. 

Por exemplo, o nosso modelo Pessoa suporta 1.000.000 rostos fora da caixa de reconhecimento de celebridades, mas também pode treiná-lo para reconhecer outras faces que não estão nessa base de dados. 

Para mais detalhes, consulte artigos sobre personalizar modelos [pessoa,](customize-person-model-overview.md) [marcas](customize-brands-model-overview.md)e [idiomas.](customize-language-model-overview.md) 

### <a name="what-is-the-sla-for-video-indexer"></a>O que é o SLA para Indexer de Vídeo?

O SLA do Azure Media Service cobre o Indexer de Vídeo e pode ser encontrado na página [SLA.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) O SLA aplica-se apenas às contas pagas do Indexer de Vídeo e não se aplica ao teste gratuito.

## <a name="privacy-questions"></a>Questões de Privacidade

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Os ficheiros de vídeo e áudio estão indexados pelo Indexer de Vídeo armazenados?

Sim, a menos que apague o ficheiro do Indexer de Vídeo, quer utilizando o website do Indexer de Vídeo ou a API, os seus ficheiros de vídeo e áudio são armazenados. Para o teste gratuito, os ficheiros de vídeo e áudio que indexa estão armazenados na região de Azure East US. Caso contrário, os seus ficheiros de vídeo e áudio são armazenados na conta de armazenamento da sua subscrição Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Posso apagar os meus ficheiros que estão armazenados no Portal do Indexante de Vídeo?

Sim, pode sempre eliminar os seus ficheiros de vídeo e áudio, bem como quaisquer metadados e insights extraídos deles pelo Indexer de Vídeo. Uma vez que apague um ficheiro do Indexer de Vídeo, o ficheiro e os seus metadados e insights são permanentemente removidos do Indexer de Vídeo. No entanto, se implementou a sua própria solução de backup no armazenamento Azure, o ficheiro permanece no seu armazenamento Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Posso controlar o acesso dos utilizadores à minha conta de Indexer de Vídeo?

Sim, só os administradores de contas podem convidar e desconvidar as pessoas para as suas contas, bem como atribuir quem tem privilégios de edição e quem tem acesso apenas a leitura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Quem tem acesso aos meus ficheiros de vídeo e áudio que foram indexados e/ou armazenados pelo Video Indexer e os metadados e insights que foram extraídos?

O seu conteúdo de vídeo ou áudio que tenha público como definição de privacidade pode ser acedido por qualquer pessoa que tenha o link para o seu conteúdo de vídeo ou áudio e suas insights. O seu conteúdo de vídeo ou áudio que tenha uma configuração de privacidade privada só pode ser acedido por utilizadores que tenham sido convidados para a conta do conteúdo de vídeo ou áudio. A definição de privacidade do seu conteúdo também se aplica aos metadados e insights que o Indexer de Vídeo extrai. Atribua a definição de privacidade quando faz o upload do seu ficheiro de vídeo ou áudio. Também pode alterar a definição de privacidade após a indexação.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Que acesso tem a Microsoft aos meus ficheiros de vídeo ou áudio indexados e/ou armazenados pelo Video Indexer e os metadados e insights que foram extraídos?

De acordo com os Termos de [Serviços Online Do Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), é totalmente dono do seu conteúdo, e a Microsoft apenas acederá aos seus conteúdos e aos metadados e insights que o Indexer de Vídeo extrai do seu conteúdo de acordo com o OST e a Declaração de Privacidade da Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Os modelos personalizados que construo na minha conta de Indexer de Vídeo estão disponíveis para outras contas?

 Não, os modelos personalizados que cria na sua conta não estão disponíveis para qualquer outra conta. Atualmente, o Indexer de Vídeo permite-lhe construir [marcas](customize-brands-model-overview.md)personalizadas, [linguagem](customize-language-model-overview.md)e modelos [pessoais](customize-person-model-overview.md) na sua conta. Estes modelos só estão disponíveis na conta em que criou os modelos.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>O conteúdo indexado pelo Indexer de Vídeo é mantido na região de Azure onde estou a usar o Indexer de Vídeo?

Sim, o conteúdo e os seus conhecimentos são mantidos na região do Azure, a menos que tenha uma configuração manual na sua subscrição Azure que utiliza várias regiões azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Qual é a política de privacidade para o Indexer de Vídeo?

O Indexer de Vídeo está coberto pela Declaração de Privacidade da [Microsoft.](https://privacy.microsoft.com/privacystatement) A declaração de privacidade explica os dados pessoais que a Microsoft processa, como a Microsoft os processa e para que finalidades a Microsoft os processa. Para saber mais sobre privacidade, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Que certificações tem o Video Indexer?

O Indexer de Vídeo tem atualmente a certificação SOC. Para rever a certificação do Indexer de Vídeo, consulte o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Qual é a diferença entre vídeos privados e públicos? 

Quando os vídeos são enviados para o Indexer de Vídeo, pode escolher entre duas definições de privacidade: privada seleção pública e privada. Os vídeos públicos são acessíveis a qualquer pessoa, incluindo utilizadores anónimos e não identificados. Os privados são limitados exclusivamente aos membros da conta. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Tentei enviar um vídeo como público e foi sinalizado por conteúdo impróprio ou ofensivo, o que significa isso? 

Ao enviar um vídeo para o Video Indexer, uma análise automática de conteúdo é feita por algoritmos e modelos de forma a garantir que nenhum conteúdo impróprio será apresentado publicamente. Se um vídeo for considerado suspeito como contendo conteúdo explícito, não será possível defini-lo como público. No entanto, os membros da conta ainda podem acessá-lo como um vídeo privado (veja-o, descarregue os insights e extraídos artefactos, e execute outras operações disponíveis para os membros da conta).   

Para definir o vídeo para acesso público, pode: 

* Construa a sua própria camada de interface (como app ou website) e use-a para interagir com o serviço De indexante de vídeo. Desta forma o vídeo permanece privado no nosso portal e os seus utilizadores podem interagir com ele através da sua interface. Por exemplo, ainda pode obter as informações ou permitir visualizar o vídeo na sua própria interface. 
* Solicite uma revisão humana do conteúdo, o que resultaria na remoção da restrição, assumindo que o conteúdo não é explícito. 

    Esta opção pode ser explorada se o website do Indexer de Vídeo for utilizado diretamente pelos seus utilizadores como camada de interface, e para visualização pública (não autenticada). 

## <a name="api-questions"></a>Perguntas da API

### <a name="what-apis-does-video-indexer-offer"></a>O que as APIs oferecem o Indexer de Vídeo?

As APIs do Indexer de Vídeo permitem indexar, extrair metadados, gestão de ativos, tradução, incorporação, personalização de modelos e muito mais. Para obter informações mais detalhadas sobre a utilização da API do Indexer de Vídeo, consulte o Portal de Desenvolvimento do [Indexante](https://api-portal.videoindexer.ai/)de Vídeo .

### <a name="what-client-sdks-does-video-indexer-offer"></a>Que cliente sDKs oferece o Indexer de Vídeo?

Atualmente, não existem sDKs de clientes oferecidos. A equipa do Indexer de Vídeo está a trabalhar nos SDKs e planeia entregá-los em breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Como posso começar com a API do Indexer de Vídeo?

Siga [tutorial: começar com o Indexer de Vídeo API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Qual é a diferença entre a API do Indexer de Vídeo e a API do Azure Media Service?

Atualmente existem algumas sobreposições nas funcionalidades oferecidas pela API do Indexer de Vídeo e pelo Azure Media Service v3 API. Pode encontrar mais informações sobre como comparar ambos os serviços [aqui](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>O que é um sinal de acesso da API e por que preciso?

A API do Indexer de Vídeo contém uma API de Autorização e uma API de Operações. A API de Autorizações contém chamadas que lhe dão acesso simbólico. Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

São necessários tokens de acesso para utilizar as APIs do Indexer de Vídeo para fins de segurança. Isto garante que quaisquer chamadas são provenientes de si ou daqueles que têm permissões de acesso à sua conta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Qual é a diferença entre o token de acesso à conta, o token de acesso ao utilizador e o token de acesso ao vídeo?

* Nível de conta – os tokens de acesso de nível de conta permitem realizar operações ao nível de conta ou de vídeo. Por exemplo, faça upload de um vídeo, liste todos os vídeos, obtenha informações de vídeo.
* Nível de utilizador - fichas de acesso ao nível do utilizador permitem-lhe realizar operações ao nível do utilizador. Permitem, por exemplo, obter contas associadas.
* Nível de vídeo – os tokens de acesso de nível de vídeo permitem realizar operações num vídeo específico. Permitem, por exemplo, obter informações de vídeo, transferir legendas, obter widgets, etc.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Quantas vezes preciso de um novo sinal de acesso? Quando é que os tokens de acesso expiram?

Os tokens de acesso expiram a cada hora, por isso tens de gerar um novo sinal de acesso a cada hora. 

## <a name="billing-questions"></a>Perguntas sobre faturação

### <a name="how-much-does-video-indexer-cost"></a>Quanto custa o Indexer de Vídeo?

O Indexer de Vídeo usa um modelo de preços simples com base na duração da entrada de conteúdo que indexa. Podem ser aplicados encargos adicionais para codificação, streaming, armazenamento, utilização da rede e unidades reservadas aos meios de comunicação. Para mais informações, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando é que sou cobrado por usar o Indexer de Vídeo?

Ao enviar um vídeo a ser indexado, o utilizador definirá o indexante para ser análise de vídeo, análise de áudio ou ambos. Isto determinará quais as SKUs que serão cobradas. Se houver um erro de nível crítico durante o processamento, um código de erro será devolvido como resposta. Neste caso, não ocorre qualquer faturação.  Um erro crítico pode ser causado por um bug no nosso código ou por uma falha crítica numa dependência interna que o serviço tem. Erros como identificação errada ou extração de insights não são considerados críticos e uma resposta é devolvida. Em qualquer caso em que uma resposta válida (código não-erro) é devolvida, a faturação ocorre.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>O Indexer de Vídeo oferece um teste gratuito?

Sim, o Video Indexer oferece um teste gratuito que oferece serviço completo e funcionalidade API. Existe uma quota de 600 minutos de vídeos para utilizadores de interface baseados na Web e 2.400 minutos para utilizadores de API. 

## <a name="next-steps"></a>Passos seguintes

[Descrição Geral](video-indexer-overview.md)
