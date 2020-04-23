---
title: Notas de lançamento do Azure Media Services Video Indexer Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe as mais recentes atualizações sobre o Indexante de Vídeo dos Serviços de Mídia Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: f7ce5f5086bcf2d577fb998df307ee684d536c15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870091"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de lançamento do Indexer de Vídeo dos Serviços De Mídia Azure

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="april-2020"></a>Abril de 2020

### <a name="new-widget-parameters-capabilities"></a>Novas capacidades de parâmetros de widget

O widget **Insights** inclui `language` novos `control`parâmetros: e .

O widget **Player** `locale` tem um novo parâmetro. Ambos `locale` `language` e parâmetros controlam a linguagem do jogador.

Para mais informações, consulte a secção de tipos de [widget.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Pele de jogador novo

Uma nova pele de jogador lançada com design atualizado.

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Suporte de linguagem personalizada para idiomas adicionais

O Indexer de vídeo suporta `ar-SY` `en-UK`agora `en-AU` modelos de linguagem personalizada para, e (apenas API).
 
### <a name="delete-account-timeframe-action-update"></a>Eliminar a atualização de ação do prazo da conta

A eliminação da ação da conta agora elimina a conta no prazo de 90 dias em vez de 48 horas.
 
### <a name="new-video-indexer-github-repository"></a>Novo repositório do Indexer de Vídeo GitHub

Um novo Índice de Vídeo GitHub com diferentes projetos, iniciando guias e amostras de código está agora disponível:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Atualização swagger

Autenticações e **operações** **unificadas** do Indexer de Vídeo numa única [especificação openaPI do Indexer de vídeo (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Os desenvolvedores podem encontrar as APIs no Portal de Desenvolvimento do [Indexante](https://api-portal.videoindexer.ai/)de Vídeo .

## <a name="december-2019"></a>Dezembro de 2019

### <a name="update-transcript-with-the-new-api"></a>Atualizar transcrição com a nova API

Atualize uma secção específica na transcrição utilizando a API do [Índice de Vídeo-Actualização.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Corrigir a configuração da conta a partir do portal Do Indexante de Vídeo

Agora pode atualizar a configuração de ligação dos Serviços de Media para auto-ajudar com questões como: 

* recurso incorreto dos Serviços de Mídia Azure
* alterações na palavra-passe
* Os recursos dos Serviços de Media foram movidos entre assinaturas  

Para corrigir a configuração da conta, no portal do Indexante de Vídeo navegar para Definições > separador conta (como proprietário).

### <a name="configure-the-custom-vision-account"></a>Configure a conta de visão personalizada

Configure a conta de visão personalizada em contas pagas utilizando o portal Indexer de Vídeo (anteriormente, este só foi suportado pela API). Para isso, inscreva-se no portal do Indexer de Vídeo, escolha model personalizização > personagens animados > Configurar. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Cenas, fotos e quadros-chave - agora em um painel de insights

Cenas, tiros e quadros-chave são agora fundidos numa única visão para um consumo e navegação mais fáceis. Quando seleciona a cena desejada, pode ver em que imagens e quadros de chaves é composto. 

### <a name="notification-about-a-long-video-name"></a>Notificação sobre um longo nome de vídeo

Quando um nome de vídeo é superior a 80 caracteres, o Indexer de vídeo mostra um erro descritivo no upload.

### <a name="streaming-endpoint-is-disabled-notification"></a>O ponto final de streaming é uma notificação desativada

Quando o ponto final de streaming é desativado, o Indexer de vídeo mostrará um erro descritivo na página do leitor.

### <a name="error-handling-improvement"></a>Melhoria do tratamento de erros

O código de estado 409 será agora devolvido das APIs do [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e [do Update Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) no caso de um vídeo estar ativamente indexado, para evitar que se sobrepôs às atuais alterações do índice de reindexem por acidente.

## <a name="november-2019"></a>Novembro de 2019
 
* Suporte de modelos de idioma personalizado coreano

    O indexador de vídeo agora`ko-KR`suporta modelos de linguagem personalizada em coreano ( ) tanto na API como no portal. 
* Novas línguas apoiadas para o discurso-a-texto (STT)

    ApIs indexador de vídeo agora suportaM STT em Levantine árabe (ar-SY), dialeto inglês do Reino Unido (en-GB) e dialeto australiano inglês (en-UA).
    
    Para o upload de vídeo, substituímos zh-HANS para zh-CN, ambos são suportados, mas zh-CN é recomendado e mais preciso.
    
## <a name="october-2019"></a>Outubro de 2019
 
* Pesquisa de personagens animados na galeria

    Ao indexar personagens animados, pode agora procurá-los na cozinha de vídeo da conta. Para mais informações, consulte o reconhecimento de [personagens animados.](animated-characters-recognition.md)

## <a name="september-2019"></a>Setembro de 2019
 
Múltiplos avanços anunciados no IBC 2019:
 
* Reconhecimento de personagens animados (pré-visualização pública)

    Capacidade de detetar ad si reconhece personagens em conteúdo animado, através da integração com visão personalizada. Para mais informações, consulte a [deteção de caracteres animados.](animated-characters-recognition.md)
* Identificação multi-linguística (pré-visualização pública)

    Detete segmentos em várias línguas na faixa de áudio e crie uma transcrição multilingual com base neles. Apoio inicial: Inglês, Espanhol, Alemão e Francês. Para mais informações, consulte [Automaticamente identificar e transcrever conteúdo multilingues.](multi-language-identification-transcription.md)
* Extração de entidade nomeada para Pessoas e Localização

    Extrai marcas, locais e pessoas da fala e texto visual através do processamento de linguagem natural (NLP).
* Classificação do tipo de tiro editorial

    Marcação de fotos com tipos editoriais como close-up, tiro médio, dois tiros, interior, exterior etc. Para mais informações, consulte a deteção do [tipo editorial de tiro.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Melhoria da inferência do tópico - agora cobrindo o nível 2
    
    O modelo de inferência do tema suporta agora uma granularidade mais profunda da taxonomia do IPTC. Leia todos os detalhes na [Azure Media Services nova inovação alimentada por IA.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Agosto de 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Indexer de vídeo implantado no Reino Unido Sul

Pode agora criar uma conta paga por Video Indexer na região sul do Reino Unido.

### <a name="new-editorial-shot-type-insights-available"></a>Novos insights do tipo de tiro editorial disponíveis

Novas tags adicionadas a imagens de vídeo fornecem "tipos de tiro" editoriais para identificá-los com frases editoriais comuns usadas no fluxo de trabalho de criação de conteúdo, tais como: closeup extremo, closeup, wide, medium, two shot, outdoor, indoor, left face e right face (Disponível no JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extração de novas entidades pessoas e locais disponíveis

O Indexer de vídeo identifica locais e pessoas nomeados através do processamento de linguagem natural (NLP) a partir do OCR e transcrição do vídeo. O Indexer de vídeo usa algoritmo de aprendizagem automática para reconhecer quando locais específicos (por exemplo, a Torre Eiffel) ou pessoas (por exemplo, John Doe) estão a ser chamados num vídeo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extração de quadros-chave em resolução nativa

Os quadros-chave extraídos pelo Indexer de Vídeo estão disponíveis na resolução original do vídeo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA para treinar modelos de rosto personalizados a partir de imagens

Rostos de treino de imagens transferidas do modo Preview para GA (disponíveis via API e no portal).

> [!NOTE]
> Não existe qualquer impacto de preços relacionados com a transição "Preview to GA".

### <a name="hide-gallery-toggle-option"></a>Ocultar a opção de alternância da galeria

O utilizador pode optar por ocultar o separador da galeria do portal (semelhante ao ocultar o separador de amostras).
 
### <a name="maximum-url-size-increased"></a>Tamanho máximo do URL aumentado

Suporte para cadeia de consulta de URL de 4096 (em vez de 2048) na indexação de um vídeo.
 
### <a name="support-for-multi-lingual-projects"></a>Apoio a projetos multilingues

Os projetos podem agora ser criados com base em vídeos indexados em diferentes línguas (apenas API).

## <a name="july-2019"></a>Julho de 2019

### <a name="editor-as-a-widget"></a>Editor como widget

O editor de IA do Indexer de Vídeo está agora disponível como um widget para ser incorporado nas aplicações do cliente.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Atualizar modelo de idioma personalizado a partir de ficheiro de legenda fechada a partir do portal

Os clientes podem fornecer formatos de ficheiroS VTT, SRT e TTML como entrada para modelos de idiomas na página de personalização do portal.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Indexer de vídeo implantado no Japão Leste

Agora pode criar uma conta paga por Video Indexer na região do Leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar a conta API (Pré-visualização)

Adicione um novo API que lhe permite atualizar o ponto final ou chave de ligação do [Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o manuseamento de erros no upload 

Uma mensagem descritiva é devolvida em caso de configuração errada da conta de Serviços De Mídia Azure subjacente.

### <a name="player-timeline-keyframes-preview"></a>Pré-visualização de keyframes de linha de tempo do jogador 

Pode agora ver uma pré-visualização de imagem para cada vez na linha temporal do jogador.

### <a name="editor-semi-select"></a>Editor semi-selecionado

Pode agora ver uma pré-visualização de todas as informações que são selecionadas como resultado da escolha de um prazo específico de insight no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar modelo de idioma personalizado a partir de ficheiro de legenda fechado

[Crie modelos de idiomas personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [atualize modelos de idiomas personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) APIs agora suportam formatos de ficheiros VTT, SRT e TTML como entrada para modelos de idiomas.

Ao ligar para a API da transcrição do [Vídeo de Atualização,](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)a transcrição é adicionada automaticamente. O modelo de treino associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar os seus modelos linguísticos, consulte [Personalizar um modelo de Idioma com O Indexante](customize-language-model-overview.md)de Vídeo .

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download - TXT e CSV

Além do formato de legendagem fechado já suportado (SRT, VTT e TTML), o Video Indexer suporta agora o download da transcrição em formatos TXT e CSV.

## <a name="next-steps"></a>Passos seguintes

[Descrição Geral](video-indexer-overview.md)
