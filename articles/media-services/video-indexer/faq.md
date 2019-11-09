---
title: Perguntas frequentes sobre o Video Indexer – Azure
titleSuffix: Azure Media Services
description: Obtenha respostas para perguntas frequentes sobre Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 95f2af1b192564290551f9ed31f0fe158b4536f6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838236"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo responde a perguntas frequentes sobre Video Indexer.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-video-indexer"></a>O que é o Video Indexer?

Video Indexer é um serviço de inteligência artificial que faz parte do Serviços de Mídia do Microsoft Azure. O Video Indexer fornece uma orquestração de vários modelos de aprendizado de máquina que permitem extrair facilmente informações profundas de um vídeo. Para fornecer informações avançadas e precisas, Video Indexer utiliza vários canais do vídeo: áudio, fala e Visual. As informações de Video Indexer podem ser usadas de várias maneiras, como melhorar a descoberta e acessibilidade de conteúdo, criar novas oportunidades de monetização ou criar novas experiências que usam as informações. O Video Indexer fornece uma interface baseada na Web para teste, configuração e personalização de modelos em sua conta. Os desenvolvedores podem usar uma API baseada em REST para integrar Video Indexer ao sistema de produção. 

### <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com Video Indexer?

Algumas das operações que Video Indexer podem executar em arquivos de mídia incluem:

* Identificação e extração de fala e identificação de alto-falantes.
* Identificação e extração de texto na tela em um vídeo.
* Detectando objetos em um arquivo de vídeo.
* Identificar marcas (por exemplo: Microsoft) de faixas de áudio e texto na tela em um vídeo.
* Detectando e reconhecendo rostos de um banco de dados de celebridades e um banco de dados de rostos definido pelo usuário.
* Extraindo tópicos discutidos, mas não necessariamente mencionados em conteúdo de áudio e vídeo.
* Criar legendas ou legendas ocultas da faixa de áudio.

Para obter mais informações e mais recursos de Video Indexer, consulte [visão geral](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Como fazer introdução ao Video Indexer?

O Video Indexer inclui uma oferta de avaliação gratuita que fornece 600 minutos na interface baseada na Web e 2.400 minutos por meio da API. Você pode [fazer logon no video indexer interface baseada na Web](https://www.videoindexer.ai/) e experimentá-lo usando qualquer identidade da Web e sem precisar configurar uma assinatura do Azure. 

Para indexar vídeos e intervalos de áudio em escala, você pode se conectar Video Indexer a uma assinatura paga do Microsoft Azure. Você pode encontrar mais informações sobre preços na página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

Você pode encontrar mais informações sobre como começar a [usar](video-indexer-get-started.md)o guia de introdução.

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Preciso de habilidades de codificação para usar Video Indexer?

Você pode usar o Video Indexer interface baseada na Web para avaliar, configurar e gerenciar sua conta sem a **necessidade de codificação**.  Quando estiver pronto para desenvolver aplicativos mais complexos, você poderá usar a [API de video indexer](https://api-portal.videoindexer.ai/) para integrar o video indexer em seus próprios aplicativos, sites ou [fluxos de trabalho personalizados usando tecnologias sem servidor, como aplicativos lógicos do Azure](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) ou Azure functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Preciso de habilidades de aprendizado de máquina para usar Video Indexer?

Não, Video Indexer fornece a integração de vários modelos de aprendizado de máquina em um único pipeline. A indexação de um arquivo de vídeo ou áudio via Video Indexer recupera um conjunto completo de informações extraídas em uma linha do tempo compartilhada sem nenhuma habilidade de aprendizado de máquina ou conhecimento sobre algoritmos necessários na parte do cliente.

### <a name="what-media-formats-does-video-indexer-support"></a>Para quais formatos de mídia Video Indexer suporte?

O Video Indexer suporta a maioria dos formatos de multimédia comuns. Consulte a lista de [formatos padrão do codificador de mídia do Azure](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) para obter mais detalhes.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Como fazer para carregar uma mídia em Video Indexer?

No Video Indexer Portal baseado na Web, você pode carregar um arquivo de mídia usando a caixa de diálogo de carregamento de arquivo ou apontando para uma URL que hospeda diretamente o arquivo de origem (consulte o [exemplo](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Qualquer URL que hospede o conteúdo de mídia usando um iFrame ou código de inserção não funcionará (consulte o [exemplo](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). A API Video Indexer requer que você especifique o arquivo de entrada por meio de uma URL ou uma matriz de bytes. Os carregamentos por meio de uma URL usando a API são limitados a 10 GB, mas não têm um limite de duração de tempo. Para obter mais informações, consulte este [Guia de instruções](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Quanto tempo leva Video Indexer para extrair informações da mídia?

O tempo necessário para indexar um arquivo de vídeo ou áudio, usando a API de Video Indexer e a interface baseada na Web do Video Indexer, depende de vários parâmetros, como o tamanho e a qualidade do arquivo, o número de informações encontradas no arquivo, o número de [unidades reservadas](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) disponíveis e se o [ponto de extremidade de streaming](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) está habilitado ou não. Recomendamos que você execute alguns arquivos de teste com seu próprio conteúdo e obtenha uma média para ter uma ideia melhor.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Posso criar fluxos de trabalho personalizados para automatizar processos com Video Indexer?

Sim, você pode integrar Video Indexer em tecnologias sem servidor, como aplicativos lógicos, fluxo e [Azure Functions](https://azure.microsoft.com/services/functions/). Você pode encontrar mais detalhes sobre o [aplicativo lógico](https://azure.microsoft.com/services/logic-apps/) e conectores de [fluxo](https://flow.microsoft.com/en-us/) para video indexer [aqui](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>No qual as regiões do Azure estão disponíveis para o indexador de vídeo?

Você pode ver quais regiões do Azure Video Indexer estão disponíveis na página [regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) .

### <a name="what-is-the-sla-for-video-indexer"></a>O que é o SLA para Video Indexer?

O SLA do serviço de mídia do Azure abrange Video Indexer e pode ser encontrado na página [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) . O SLA só se aplica a Video Indexer contas pagas e não se aplica à avaliação gratuita.

## <a name="privacy-questions"></a>Perguntas de privacidade

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Os arquivos de vídeo e áudio são indexados por Video Indexer armazenados?

Sim, a menos que você exclua o arquivo de Video Indexer, usando o site ou a API do Video Indexer, os arquivos de vídeo e áudio são armazenados. Para a avaliação gratuita, os arquivos de vídeo e áudio que você indexa são armazenados na região do Azure leste dos EUA. Caso contrário, os arquivos de áudio e vídeo são armazenados na conta de armazenamento da sua assinatura do Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Posso excluir meus arquivos armazenados no portal Video Indexer?

Sim, você sempre pode excluir seus arquivos de áudio e vídeo, bem como quaisquer metadados e informações extraídos deles por Video Indexer. Depois de excluir um arquivo do Video Indexer, o arquivo e seus metadados e informações são removidos permanentemente do Video Indexer. No entanto, se você tiver implementado sua própria solução de backup no armazenamento do Azure, o arquivo permanecerá no armazenamento do Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Posso controlar o acesso do usuário à minha conta do Video Indexer?

Sim, somente administradores de contas podem convidar e desconvidar pessoas para suas contas, bem como atribuir quem tem privilégios de edição e quem tem acesso somente leitura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Quem tem acesso aos meus arquivos de vídeo e áudio que foram indexados e/ou armazenados por Video Indexer e os metadados e informações que foram extraídos?

Seu conteúdo de vídeo ou áudio que tenha público como sua configuração de privacidade pode ser acessado por qualquer pessoa que tenha o link para seu conteúdo de vídeo ou áudio e suas ideias. Seu conteúdo de áudio ou vídeo que tenha privado como sua configuração de privacidade só pode ser acessado por usuários que foram convidados para a conta do conteúdo de vídeo ou áudio. A configuração de privacidade do seu conteúdo também se aplica aos metadados e informações que Video Indexer extrai. Você atribui a configuração de privacidade ao carregar o arquivo de áudio ou vídeo. Você também pode alterar a configuração de privacidade após a indexação.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Que acesso a Microsoft tem aos meus arquivos de vídeo ou áudio que foram indexados e/ou armazenados por Video Indexer e os metadados e informações que foram extraídos?

De acordo com os [termos dos serviços online do Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), você possui completamente seu conteúdo e a Microsoft acessará somente seu conteúdo e os metadados e as informações que Video indexer extrair do seu conteúdo de acordo com o Ost e a política de privacidade da Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Os modelos personalizados que crio em minha conta de Video Indexer estão disponíveis para outras contas?

 Não, os modelos personalizados que você cria em sua conta não estão disponíveis para nenhuma outra conta. No momento, Video Indexer permite que você crie modelos personalizados de [marcas](customize-brands-model-overview.md), [idiomas](customize-language-model-overview.md)e [pessoas](customize-person-model-overview.md) em sua conta. Esses modelos só estão disponíveis na conta em que você criou os modelos.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>O conteúdo é indexado por Video Indexer mantido na região do Azure onde estou usando Video Indexer?

Sim, o conteúdo e suas informações são mantidos na região do Azure, a menos que você tenha uma configuração manual em sua assinatura do Azure que usa várias regiões do Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Qual é a política de privacidade para Video Indexer?

O Video Indexer é coberto pela [política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement). A declaração de privacidade explica os dados pessoais dos processos da Microsoft, como a Microsoft o processa e para quais finalidades a Microsoft o processa. Para saber mais sobre privacidade, visite a [central de confiabilidade da Microsoft](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Quais certificações Video Indexer tem?

Video Indexer atualmente tem a certificação SOC. Para examinar a certificação de Video Indexer, consulte a [central de confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Perguntas sobre a API

### <a name="what-apis-does-video-indexer-offer"></a>Quais APIs Video Indexer oferecem?

As APIs do Video Indexer permitem indexação, extração de metadados, gerenciamento de ativos, tradução, inserção, personalização de modelos e muito mais. Para obter informações mais detalhadas sobre como usar a API Video Indexer, consulte o [portal do video indexer Developer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>O que os SDKs do cliente Video Indexer oferecem?

No momento, não há SDKs de cliente oferecidos. A equipe de Video Indexer está trabalhando em SDKs e planos para entregá-los em breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Como fazer introdução à API do Video Indexer?

Siga [o tutorial: introdução à API de video indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Qual é a diferença entre a API de Video Indexer e a API do serviço de mídia do Azure v3?

Atualmente, há algumas sobreposições nos recursos oferecidos pela API de Video Indexer e a API do serviço de mídia do Azure v3. Você pode encontrar mais informações sobre como comparar os dois serviços [aqui](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>O que é um token de acesso de API e por que preciso dele?

A API de Video Indexer contém uma API de autorização e uma API de operações. A API de autorizações contém chamadas que fornecem o token de acesso. Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

Os tokens de acesso são necessários para usar as APIs de Video Indexer para fins de segurança. Isso garante que todas as chamadas sejam provenientes de você ou aquelas que tenham permissões de acesso à sua conta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Qual é a diferença entre o token de acesso da conta, o token de acesso do usuário e o token de acesso de vídeo?

* Nível da conta – os tokens de acesso no nível da conta permitem executar operações no nível da conta ou do vídeo. Por exemplo, carregue um vídeo, liste todos os vídeos, obtenha informações de vídeo.
* Nível de usuário os tokens de acesso no nível do usuário permitem executar operações no nível do usuário. Permitem, por exemplo, obter contas associadas.
* Nível de vídeo – os tokens de acesso no nível de vídeo permitem executar operações em um vídeo específico. Permitem, por exemplo, obter informações de vídeo, transferir legendas, obter widgets, etc.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Com que frequência preciso obter um novo token de acesso? Quando os tokens de acesso expiram?

Tokens de acesso expiram a cada hora, portanto, você precisa gerar um novo token de acesso a cada hora. 

## <a name="billing-questions"></a>Perguntas sobre faturação

### <a name="how-much-does-video-indexer-cost"></a>Quanto custa Video Indexer?

Video Indexer usa um modelo de preços pré-pago simples com base na duração da entrada de conteúdo que você indexa. Encargos adicionais podem ser aplicados para codificação, streaming, armazenamento, uso de rede e unidades reservadas de mídia. Para obter mais informações, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando sou cobrado por usar Video Indexer?

Quando enviar um vídeo para indexação, o utilizador definirá a indexação para análise de vídeo, análise de áudio ou ambas. Isto determinará que SKUs serão cobrados. Em caso de erro de nível crítico durante o processamento, será devolvido um código de erro como resposta. Nesse caso, não ocorre faturação.  Um erro crítico pode ser causado por um erro no nosso código ou uma falha crítica numa dependência interna do serviço. Erros como identificação ou extração de informações incorretas não são considerados críticos e é devolvida uma resposta. Sempre que seja devolvida uma resposta válida (sem ser código de erro), ocorre faturação.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer oferece uma avaliação gratuita?

Sim, a Video Indexer oferece uma avaliação gratuita que oferece funcionalidade completa de serviço e API. Há uma cota de 600 minutos de vídeos para usuários de interface baseada na Web e 2.400 minutos para usuários de API. 

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](video-indexer-overview.md)
