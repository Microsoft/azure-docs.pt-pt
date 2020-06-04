---
title: Azure Media Services Video Indexer lança notas de lançamento / Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe as últimas atualizações sobre o Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/02/2020
ms.author: juliako
ms.openlocfilehash: 5bd4c9aa3fde9e3fa596ce5a18b892edfab60af5
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325070"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de lançamento do Azure Media Services Video Indexer

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="may-2020"></a>Maio de 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Indexante de Vídeo implantado no Leste dos EUA

Agora pode criar uma conta paga por Video Indexer na região leste dos EUA.
 
### <a name="video-indexer-url"></a>URL indexante de vídeo

Os pontos finais regionais do Indexer de Vídeo foram todos unificados para começar apenas com www. Não é necessário qualquer item de ação.

A partir de agora, você chega www.videoindexer.ai se é para incorporar widgets ou iniciar sessão em aplicações web do Video Indexer.

Também wus.videoindexer.ai seria redirecionado para www. Mais informações estão disponíveis em [widgets Do Indexer de Vídeo Incorporado nas suas aplicações.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-widget-parameters-capabilities"></a>Novas capacidades de parâmetros de widget

O widget **Insights** inclui novos parâmetros: `language` e `control` .

O widget **Player** tem um novo `locale` parâmetro. Ambos `locale` e `language` os parâmetros controlam a linguagem do jogador.

Para obter mais informações, consulte a secção [de tipos de widgets.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Pele de novo jogador

Uma nova pele de jogador lançada com design atualizado.

### <a name="prepare-for-upcoming-changes"></a>Prepare-se para as próximas mudanças

* Hoje, as seguintes APIs devolvem um objeto de conta:

    * [Criar Conta Paga](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Conta Get](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Obter-Contas-Autorização](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    O objeto Conta tem um `Url` campo que aponta para a localização do website do Indexer de [Vídeo.](https://www.videoindexer.ai/)
Para contas pagas, o `Url` campo está atualmente a apontar para um URL interno em vez do site público.
Nas próximas semanas vamos alterá-lo e devolver o URL do [site do Indexer de Vídeo](https://www.videoindexer.ai/) para todas as contas (julgamento e pagamento).

    Não utilize os URLs internos, deve utilizar as [APIs públicas do Indexante de Vídeo](https://api-portal.videoindexer.ai/).
* Se estiver a incorporar URLs indexantes de vídeo nas suas aplicações e os URLs não estiverem a apontar para o [website do Video Indexer](https://www.videoindexer.ai/) ou para o ponto final da API do Indexer de Vídeo ( `https://api.videoindexer.ai` ), mas sim para um ponto final regional (por exemplo, `https://wus2.videoindexer.ai` ), regenerar os URLs.

   Pode fazê-lo por qualquer um dos dois:

    * Substituição do URL por um URL que aponta para as APIs widget do Indexer de Vídeo (por exemplo, o [widget insights](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget))
    * Utilizando o website do Índice de Vídeo para gerar um novo URL incorporado:
         
         Prima **Play** para chegar à página do seu vídeo -> clique no botão ** &lt; / &gt; Embed** -> copiar o URL na sua aplicação:
   
    Os URLs regionais não são apoiados e serão bloqueados nas próximas semanas.

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Suporte linguístico personalizado para idiomas adicionais

O Video Indexer suporta agora modelos de linguagem personalizados para `ar-SY` `en-UK` , e `en-AU` (apenas API).
 
### <a name="delete-account-timeframe-action-update"></a>Eliminar atualização do prazo de tempo da conta

A ação da conta de eliminação agora elimina a conta no prazo de 90 dias em vez de 48 horas.
 
### <a name="new-video-indexer-github-repository"></a>Novo repositório do índice de vídeo GitHub

Um novo Índice de Vídeo GitHub com diferentes projetos, iniciando guias e amostras de código está agora disponível:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Atualização de Swagger

O Indexante de Vídeo uniu **autenticações** e **operações** numa única [Especificação OpenAPI (swagger) ( Índice de Vídeo).](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson) Os desenvolvedores podem encontrar as APIs no [Portal do Desenvolvedor de Indexados de Vídeo.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>Dezembro de 2019

### <a name="update-transcript-with-the-new-api"></a>Atualizar transcrição com a nova API

Atualize uma secção específica na transcrição utilizando a API [do Índice de Actualização-Vídeo.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Corrigir a configuração da conta a partir do portal do Indexante de Vídeo

Pode agora atualizar a configuração de ligação dos Serviços de Mídia para se auto-ajudar com questões como: 

* recurso incorreto da Azure Media Services
* alterações de senha
* Os recursos dos Serviços de Comunicação Social foram movidos entre subscrições  

Para corrigir a configuração da conta, no portal do Indexante de Vídeo navegue para o separador > Contas (como proprietário).

### <a name="configure-the-custom-vision-account"></a>Configurar a conta de visão personalizada

Configurar a conta de visão personalizada em contas pagas utilizando o portal Video Indexer (anteriormente, este só foi suportado pela API). Para isso, inscreva-se no portal do Indexer de Vídeo, escolha modelização > caracteres animados > Configurar. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Cenas, fotos e quadros-chave - agora em um painel de insights

Cenas, fotos e quadros-chave estão agora fundidos numa visão para um consumo mais fácil e navegação. Quando seleciona a cena desejada, pode ver em que fotos e quadros-chave consiste. 

### <a name="notification-about-a-long-video-name"></a>Notificação sobre um longo nome de vídeo

Quando um nome de vídeo tem mais de 80 caracteres, o Índice de Vídeo mostra um erro descritivo no upload.

### <a name="streaming-endpoint-is-disabled-notification"></a>O ponto final de streaming é uma notificação desativada

Quando o ponto final de streaming estiver desativado, o Video Indexer apresentará um erro descritivo na página do jogador.

### <a name="error-handling-improvement"></a>Melhoria do manuseamento de erros

O código de estado 409 será agora devolvido das APIs do [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e [update Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) no caso de um vídeo estar ativamente indexado, para evitar que o atual re-indexado mude por acidente.

## <a name="november-2019"></a>Novembro de 2019
 
* Suporte de modelos de linguagem personalizada coreana

    O indexante de vídeo suporta agora modelos linguísticos personalizados em coreano ( `ko-KR` ) tanto na API como no portal. 
* Novas línguas apoiadas para discurso-a-texto (STT)

    As APIs do Indexante de Vídeo suportam agora STT em Árabe Levantine (ar-SY), dialeto inglês do Reino Unido (en-GB) e dialeto australiano inglês (en-AU).
    
    Para o upload de vídeo, substituímos zh-HANS para zh-CN, ambos são suportados mas zh-CN é recomendado e mais preciso.
    
## <a name="october-2019"></a>Outubro de 2019
 
* Pesquisa de personagens animados na galeria

    Ao indexar caracteres animados, pode agora procurá-los na cozinha de vídeo da conta. Para obter mais informações, consulte [o reconhecimento de caracteres animados.](animated-characters-recognition.md)

## <a name="september-2019"></a>Setembro de 2019
 
Múltiplos avanços anunciados no IBC 2019:
 
* Reconhecimento de caracteres animados (pré-visualização pública)

    Capacidade de detetar anúncios de grupo reconhecem caracteres em conteúdo animado, através da integração com visão personalizada. Para obter mais informações, consulte [a deteção de caracteres animados.](animated-characters-recognition.md)
* Identificação multi-linguístico (pré-visualização pública)

    Detete segmentos em vários idiomas na faixa áudio e crie uma transcrição multilíngue baseada neles. Apoio inicial: Inglês, Espanhol, Alemão e Francês. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo multi-linguístico.](multi-language-identification-transcription.md)
* Extração de entidades nomeadas para pessoas e localização

    Extrai marcas, locais e pessoas de linguagem e texto visual através do processamento de linguagem natural (NLP).
* Classificação do tipo tiro editorial

    Marcação de shots com tipos editoriais como close up, medium shot, two shot, indoor, outdoor etc. Para obter mais informações, consulte [a deteção do tipo de tiro editorial.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Aumento de inferição de tópicos - agora abrangendo o nível 2
    
    O modelo de inferenção do tópico suporta agora uma granularidade mais profunda da taxonomia do IPTC. Leia todos os detalhes na [Azure Media Services nova inovação alimentada por IA](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Agosto de 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Indexante de Vídeo implantado no Reino Unido Sul

Agora pode criar uma conta paga por Video Indexer na região sul do Reino Unido.

### <a name="new-editorial-shot-type-insights-available"></a>Novas informações sobre tipo de tiro editorial disponíveis

Novas etiquetas adicionadas às fotos de vídeo fornecem "tipos de tiro" editoriais para identificá-los com frases editoriais comuns usadas no fluxo de trabalho de criação de conteúdo, tais como: closeup extremo, closeup, largo, médio, dois tiros, exterior, interior, face esquerda e direita (Disponível no JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Novas entidades de pessoas e locais disponíveis

O Video Indexer identifica localizações nomeadas e pessoas através do processamento de linguagem natural (NLP) a partir do OCR do vídeo e transcrição. O Video Indexer usa algoritmo de aprendizagem automática para reconhecer quando localizações específicas (por exemplo, a Torre Eiffel) ou pessoas (por exemplo, John Doe) estão a ser chamadas num vídeo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extração de quadros-chave em resolução nativa

Os quadros-chave extraídos pelo Video Indexer estão disponíveis na resolução original do vídeo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA para treinar modelos de rosto personalizados a partir de imagens

Os rostos de treino a partir de imagens passaram do modo de pré-visualização para GA (disponível via API e no portal).

> [!NOTE]
> Não existe qualquer impacto nos preços relacionados com a transição "Pré-visualização para GA".

### <a name="hide-gallery-toggle-option"></a>Ocultar a opção de alternância da galeria

O utilizador pode optar por ocultar o separador da galeria do portal (semelhante ao separador amostras).
 
### <a name="maximum-url-size-increased"></a>Tamanho máximo do URL aumentado

Suporte para cadeia de consulta de URL de 4096 (em vez de 2048) na indexação de um vídeo.
 
### <a name="support-for-multi-lingual-projects"></a>Apoio a projetos multilingues

Os projetos podem agora ser criados com base em vídeos indexados em diferentes línguas (apenas API).

## <a name="july-2019"></a>Julho de 2019

### <a name="editor-as-a-widget"></a>Editor como widget

O editor de IA do Índice de Vídeo está agora disponível como um widget para ser incorporado nas aplicações do cliente.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Atualizar modelo de linguagem personalizada a partir de ficheiro de legenda fechado a partir do portal

Os clientes podem fornecer formatos de ficheiroS VTT, SRT e TTML como entrada para modelos de idioma na página de personalização do portal.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Indexante de Vídeo implantado no Japão Oriental

Agora pode criar uma conta paga por Video Indexer na região leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar conta API (Pré-visualização)

Adicione uma nova API que lhe permite atualizar o ponto final ou chave de [ligação Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o manuseamento de erros no upload 

Uma mensagem descritiva é devolvida em caso de configuração errada da conta subjacente dos Serviços de Media Azure.

### <a name="player-timeline-keyframes-preview"></a>Pré-visualização de quadros-chave da linha do jogador 

Agora pode ver uma pré-visualização de imagem para cada vez na linha temporal do jogador.

### <a name="editor-semi-select"></a>Editor semi-selecionado

Pode agora ver uma pré-visualização de todas as informações que são selecionadas como resultado da escolha de um calendário de introspeção específico no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar modelo de linguagem personalizada a partir de ficheiro de legenda fechada

[Crie um modelo de idioma personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [atualize os modelos de linguagem personalizada](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) APIs suportam agora os formatos de ficheiros VTT, SRT e TTML como entrada para modelos de idioma.

Ao ligar para a [transcrição do Vídeo de Atualização API,](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)a transcrição é adicionada automaticamente. O modelo de treino associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar os seus modelos linguísticos, consulte [Personalizar um modelo de linguagem com índice de vídeo.](customize-language-model-overview.md)

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de downloads - TXT e CSV

Além do formato de legendagem fechado já suportado (SRT, VTT e TTML), o Video Indexer suporta agora o download da transcrição nos formatos TXT e CSV.

## <a name="next-steps"></a>Próximos passos

[Descrição geral](video-indexer-overview.md)
