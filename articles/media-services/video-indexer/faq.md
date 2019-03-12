---
title: Perguntas mais frequentes sobre o Video Indexer - Azure
titlesuffix: Azure Media Services
description: Obtenha respostas às perguntas mais frequentes sobre o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 8d70fca5820dbd2152ca378916bfbcdbad4a027f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776761"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo responde às perguntas mais frequentes sobre o Video Indexer.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-video-indexer"></a>O que é o Video Indexer?

Indexador de vídeos é um serviço de inteligência artificial que faz parte dos serviços de multimédia do Microsoft Azure. Indexador de vídeo fornece uma orquestração de vários modelos de machine learning que lhe permite extrair facilmente informações mais detalhadas de um vídeo. Para fornecer informações preciso e avançado, o Video Indexer torna o uso de vários canais do vídeo: áudio, voz e visual. Informações do Video Indexer podem ser utilizadas de várias maneiras, como melhorar a detectabilidade do conteúdo e a acessibilidade, criando novas oportunidades de monetização, ou criar novas experiências que utilizam as informações. Indexador de vídeo fornece uma interface baseada na web para testes, configuração e personalização dos modelos em sua conta. Os programadores podem utilizar uma API baseada em REST para integrar o indexador de vídeo no sistema de produção. 

### <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o indexador de vídeo?

Algumas das operações que o indexador de vídeo pode executar nos arquivos de suporte de dados incluem:

* Identificar e a extração de voz e identificar oradores.
* Identificar e a extração no ecrã texto num vídeo.
* A detetar objetos num arquivo de vídeo.
* Identificar marcas (por exemplo: Texto da Microsoft) a partir de faixas de áudio e na tela num vídeo.
* Detetar e reconhecer rostos de uma base de dados de celebridades e uma base de dados definidas pelo utilizador de faces.
* Extrair os tópicos abordados, mas não necessariamente mencionado no conteúdo de áudio e vídeo.
* A criar legendas ou textos da faixa de áudio.

Para obter mais informações e mais funcionalidades do indexador de vídeo, consulte [descrição geral](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Como posso começar a utilizar com o indexador de vídeo?

Indexador de vídeos inclui uma oferta que fornece-lhe 600 minutos na interface baseada na web e de 2.400 minutos através da API de avaliação gratuita. Pode [início de sessão para a interface baseada na web do Video Indexer](https://www.videoindexer.ai/) e experimente mesmo usando qualquer identidade da web e sem ter de configurar uma subscrição do Azure. 

Vídeos de índice e flies áudio em escala, pode ligar o indexador de vídeo para uma subscrição paga do Microsoft Azure. Pode encontrar mais informações sobre os preços sobre o [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) página.

Pode encontrar mais informações sobre como começar [começar](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>É necessário habilidades para utilizar o Video Indexer?

Pode utilizar a interface do indexador de vídeos baseada na web para avaliar, configurar e gerir a sua conta com **não é necessária programação**.  Quando estiver pronto para desenvolver aplicações mais complexas, pode utilizar o [API do Video Indexer](https://api-portal.videoindexer.ai/) integrar o indexador de vídeo em seus próprios aplicativos, sites da web, ou [fluxos de trabalho personalizados usando tecnologias sem servidor, como Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) ou as funções do Azure.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Preciso de habilidades de aprendizado de máquina para utilizar o Video Indexer?

Não, o Video Indexer fornece a integração de vários modelos de machine learning num pipeline. Indexação de um ficheiro de vídeo ou áudio através do Video Indexer obtém um conjunto completo de informações extraídas na linha de tempo partilhada um sem qualquer competências ou dados de conhecimento em necessário na parte do cliente de algoritmos de aprendizagem automática.

### <a name="what-media-formats-does-video-indexer-support"></a>Formatos de suporte de dados de que é o suporte de indexador de vídeo?

O Video Indexer suporta formatos de mídia mais comuns. Consulte a [formatos padrão do codificador de multimédia do Azure](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) lista para obter mais detalhes.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Como fazer carrego um suporte de dados para o Video Indexer?

No portal baseado na web indexador de vídeo, pode carregar um ficheiro de suporte de dados usando a caixa de diálogo de carregamento de ficheiro ou ao apontar para um URL ou diretamente aloja o ficheiro de origem (consulte [exemplo](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Qualquer URL que o suporte de dados com uma iFrame de conteúdo ou código de incorporação de anfitriões não irão funcionar (consulte [exemplo](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). A API do Video Indexer requer que especifique o ficheiro de entrada através de um URL ou uma matriz de bytes. Carregamentos através de um URL com a API estão limitados a 10 GB, mas não tem um limite de tempo de duração. Para obter mais informações, consulte este [guia de procedimentos](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>O tempo que demora o indexador de vídeo para extrair informações de suporte de dados?

A quantidade de tempo que demora a indexar um ficheiro de vídeo ou áudio, as duas usando a API do indexador de vídeo e a interface baseada na web do Video Indexer, depende de vários parâmetros, como a extensão de ficheiro e a qualidade, o número de informações encontradas no arquivo, o número de [unidades reservadas](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) disponíveis e se o [ponto final de transmissão em fluxo](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) está ativada ou não. Recomendamos que execute alguns arquivos de teste com seu próprio conteúdo e tirar uma média para ter uma idéia melhor.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Pode criar fluxos de trabalho personalizados para automatizar processos com o indexador de vídeo?

Sim, pode integrar Video Indexer tecnologias sem servidor, como o Logic Apps, Flow, e [as funções do Azure](https://azure.microsoft.com/services/functions/). Pode encontrar mais detalhes sobre o [aplicação lógica](https://azure.microsoft.com/services/logic-apps/) e [fluxo](https://flow.microsoft.com/en-us/) conectores para o Video Indexer [aqui](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Em que regiões do Azure é o indexador de vídeo disponível?

Pode ver que regiões do Azure o indexador de vídeo está disponível na [regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) página.

### <a name="what-is-the-sla-for-video-indexer"></a>O que é o SLA para o Video Indexer?

SLA dos serviços multimédia do Azure abrange o indexador de vídeo e podem ser encontrada no [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) página. O SLA só aplica-se ao Video Indexer pago contas e não é aplicável a versão de avaliação gratuita.

## <a name="privacy-questions"></a>Questões de privacidade

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Ficheiros de vídeos e áudio são indexados pelo indexador de vídeos armazenados?

Sim, a menos que elimine o ficheiro do Video Indexer, usando o Web site do indexador de vídeos ou a API, são armazenados os ficheiros de áudio e vídeos. A avaliação gratuita, o vídeo e áudio, arquivos que índice são armazenados na região do Azure E.U.A. Leste. Caso contrário, os ficheiros de áudio e vídeos são armazenados na conta de armazenamento da sua subscrição do Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Posso excluir minha ficheiros que estão armazenados no Portal do indexador de vídeo?

Sim, pode sempre eliminar o vídeo e arquivos de áudio, bem como quaisquer metadados e informações extraídas dos mesmos pelo indexador de vídeos. Uma vez elimina um ficheiro de indexador de vídeo, o ficheiro e seus metadados e as informações são permanentemente removidas do Video Indexer. No entanto, se tiver implementado a sua própria solução de cópia de segurança no armazenamento do Azure, o ficheiro permanece no armazenamento do Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Pode controlar o acesso de utilizador para a minha conta do Video Indexer?

Sim, apenas os administradores de conta podem convidar e desconvidar pessoas às respetivas contas, bem como atribuir quem tem privilégios de edição e quem tem acesso só de leitura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Quem tem acesso para meus arquivos de áudio e vídeos que tem sido indexados e/ou armazenados pelo indexador de vídeos e os metadados e informações que foram extraídas?

O conteúdo de vídeo ou áudio que tenham públicos como sua definição de privacidade pode ser acedido por qualquer pessoa que a ligação para o conteúdo de vídeo ou áudio e respetivas insights. O conteúdo de vídeo ou áudio que tenham privados como sua definição de privacidade só pode ser acedido por utilizadores que foram convidados para a conta do conteúdo de vídeo ou áudio. A definição de privacidade dos seus conteúdos também se aplica aos metadados e informações que extrai do Video Indexer. Atribuir a definição de privacidade ao carregar o ficheiro de vídeo ou áudio. Também pode alterar a definição de privacidade após indexar.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Que acesso Microsoft tem para meus arquivos de vídeos ou áudio que tem sido indexados e/ou armazenados pelo indexador de vídeos e os metadados e informações que foram extraídas?

Pelo [termos de serviço Online do Azure](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), que é proprietário completamente o seu conteúdo e Microsoft apenas irá aceder ao seu conteúdo e os metadados e informações que o indexador de vídeo extrai a partir do seu conteúdo, de acordo com OST e da Microsoft Declaração de privacidade.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Estão disponíveis para outras contas os modelos personalizados que crio na minha conta do Video Indexer?

 Não, os modelos personalizados que criou na sua conta não estão disponíveis para qualquer outra conta. Indexador de vídeos atualmente permite-lhe criar personalizado [marcas](customize-brands-model-overview.md), [linguagem](customize-language-model-overview.md), e [pessoa](customize-person-model-overview.md) modelos na sua conta. Esses modelos só estão disponíveis na conta em que criou os modelos.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>O conteúdo é indexado pelo indexador de vídeo mantidas ao nível da região do Azure, onde estou usando o indexador de vídeo?

Sim, o conteúdo e as suas informações são mantidas na região do Azure, a menos que tem uma configuração manual na sua subscrição do Azure que utiliza várias regiões do Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>O que é a política de privacidade para o indexador de vídeo?

Indexador de vídeos é abrangido pela [declaração de privacidade do Microsoft](https://privacy.microsoft.com/privacystatement). A declaração de privacidade explica os Microsoft processa os dados pessoais, como a Microsoft processa- e para fins de que a Microsoft processa-lo. Para saber mais sobre a privacidade, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>As certificações o indexador de vídeo tem?

Indexador de vídeos atualmente possui a certificação de SOC. Para rever a certificação do Video Indexer, veja a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Perguntas de API

### <a name="what-apis-does-video-indexer-offer"></a>As APIs oferecem o Video Indexer?

APIs do Video Indexer permite a indexação, a extrair metadados, gerenciamento de ativos, tradução, incorporar, personalização de modelos e muito mais. Para obter mais informações sobre como utilizar a API do Video Indexer, consulte a [Portal do Programador de indexador de vídeo](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Os SDKs de cliente oferecem o Video Indexer?

Atualmente, não há nenhum cliente SDKs oferecidos. A equipe do indexador de vídeo está trabalhando nos SDKs e planos para fornecê-los em breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Como posso começar a utilizar com a API do Video Indexer?

Siga [Tutorial: introdução à API do Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>O que é a diferença entre a API do Video Indexer e a API do serviço de multimédia do Azure v3?

Atualmente, existem alguns sobreposições em recursos oferecidos pela API do Video Indexer e a API do serviço de multimédia do Azure v3. Pode encontrar mais informações sobre como comparar ambos os serviços [aqui](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>O que é um token de acesso de API e por que preciso?

A API do Video Indexer contém uma API de autorização e uma API de operações. A API de autorizações contém chamadas que lhe token de acesso. Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

Tokens de acesso são necessárias para utilizar as APIs do indexador de vídeo por motivos de segurança. Isto garante que todas as chamadas são provenientes de ou os utilizadores que têm permissões de acesso à sua conta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>O que é a diferença entre o token de acesso de conta, o token de acesso de utilizador e o token de acesso de vídeo?

* Nível de conta – a tokens de acesso de nível de conta permitem-lhe efetuar operações no nível da conta ou ao nível do vídeo. Por exemplo, carregar um vídeo, listar todos os vídeos, obter informações de vídeo.
* Nível de utilizador - a tokens de acesso de nível de utilizador permitem-lhe efetuar operações no nível do usuário. Permitem, por exemplo, obter contas associadas.
* Nível de vídeo – a tokens de acesso de nível de vídeo permitem-lhe efetuar operações num vídeo específico. Permitem, por exemplo, obter informações de vídeo, transferir legendas, obter widgets, etc.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>A frequência com que é necessário obter um novo token de acesso? Quando os tokens de acesso expira?

Tokens de acesso expirarem a cada hora, por isso terá de gerar um novo token de acesso a cada hora. 

## <a name="billing-questions"></a>Perguntas sobre faturação

### <a name="how-much-does-video-indexer-cost"></a>Quanto custa o Video Indexer?

O Video Indexer utiliza um modelo de preços pay as you go simple com base na duração da entrada conteúda que indexar. Encargos adicionais poderão aplicar-se para a codificação, transmissão em fluxo, armazenamento, a utilização de rede e suporte de dados de unidades reservadas. Para obter mais informações, consulte a [preços](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) página.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando me são faturadas para utilizar o Video Indexer?

Ao enviar um vídeo ser indexados, o utilizador irá definir a indexação para ser a análise de vídeo, análise de áudio ou ambos. Isso determinará qual são cobradas SKUs. Se existir um erro de nível crítico durante o processamento, será devolvido um código de erro como resposta. Nesse caso, uma faturação sem ocorre.  Um erro crítico pode ser causado por um bug no nosso código ou tem uma falha crítica numa dependência interna do serviço. Erros, tais como a extração errado de identificação ou informações não são considerados críticas e uma resposta é devolvida. Em qualquer caso em que é devolvida uma resposta válida (código de erro não), ocorre a faturação.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>O indexador de vídeo oferece uma avaliação gratuita?

Sim, o indexador de vídeo oferece uma avaliação gratuita, que fornece o serviço completo e a funcionalidade de API. Existe uma quota de 600 minutos que vale a pena de vídeos para utilizadores de interface baseada na web e de 2.400 minutos para que os utilizadores de API. 

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral](video-indexer-overview.md)
