---
title: API de áudio longo (pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de Áudio Longo é projetada para uma síntese assíncrona de texto de longa forma para a fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 0e18fd0c52fd4090477599f53cd0ef0bc05855f2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587345"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (pré-visualização)

A API de Áudio Longo foi concebida para uma síntese assíncrona de texto de longa forma para a fala (por exemplo: livros de áudio, artigos noticiosos e documentos). Esta API não devolve áudio sintetizado em tempo real, em vez disso a expectativa é que você faça uma sondagem para a(s) resposta(s) e consuma a(s) saída(s) à medida que são disponibilizados a partir do serviço. Ao contrário do texto para a fala API que é usado pelo Speech SDK, a API de Áudio Longo pode criar áudio sintetizado mais de 10 minutos, tornando-o ideal para editores e plataformas de conteúdo sonoro.

Benefícios adicionais da API de Áudio Longo:

* Discurso sintetizado devolvido pelo serviço usa as melhores vozes neurais.
* Não há necessidade de implantar um ponto final de voz, pois sintetiza vozes em nenhum modo de lote em tempo real.

> [!NOTE]
> A API de áudio longo agora suporta [vozes neuronais públicas](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) e [vozes neurais personalizadas.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao utilizar a API de Áudio Longo, submeterá um ficheiro de texto ou ficheiros para ser sintetizado, pesquisa para o estado, então se o estado for bem sucedido, pode descarregar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho de API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para a síntese

Ao preparar o seu ficheiro de texto, certifique-se de que:

* É texto simples (.txt) ou texto SSML (.txt)
* É codificado como [UTF-8 com Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um único ficheiro, não um fecho.
* Contém mais de 400 caracteres para texto simples ou 400 [caracteres de billable](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) para texto SSML, e menos de 10.000 parágrafos
  * Para texto simples, cada parágrafo é separado ao bater **Enter/Return** - Ver exemplo de entrada de [texto simples](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para texto SSML, cada peça SSML é considerada um parágrafo. As peças SSML devem ser separadas por diferentes parágrafos - Ver exemplo de entrada de [texto SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para os chineses (continente), chinês (Hong Kong), chinês (Taiwan), japonês e coreano, uma palavra será contada como dois caracteres. 

## <a name="submit-synthesis-requests"></a>Enviar pedidos de síntese

Depois de preparar o conteúdo de entrada, siga [rapidamente](https://aka.ms/long-audio-python) a síntese áudio de forma longa para submeter o pedido. Se tiver mais de um ficheiro de entrada, terá de submeter vários pedidos. 

Os **códigos** de estado HTTP indicam erros comuns.

| API | Código de estado de HTTP | Descrição | Proposta |
|-----|------------------|-------------|----------|
| Criar | 400 | A síntese de voz não está ativada nesta região. | Mude a chave de subscrição do discurso com uma região apoiada. |
|        | 400 | Apenas a subscrição de discurso **Standard** para esta região é válida. | Mude a chave de subscrição do discurso para o nível de preços "Standard". |
|        | 400 | Exceda o limite de 20.000 pedidos para a conta Azure. Por favor, remova alguns pedidos antes de enviar novos. | O servidor manterá até 20.000 pedidos para cada conta Azure. Apague alguns pedidos antes de submeter novos. |
|        | 400 | Este modelo não pode ser utilizado na síntese de voz : {modelID}. | Certifique-se de que o estado do {modelID}está correto. |
|        | 400 | A região para o pedido não corresponde à região para o modelo : {modelID}. | Certifique-se de que a região do {modelID}corresponde à região do pedido. |
|        | 400 | A síntese de voz apenas suporta o ficheiro de texto no UTF-8 codificando com o marcador de encomenda byte. | Certifique-se de que os ficheiros de entrada estão na codificação UTF-8 com o marcador de encomenda byte. |
|        | 400 | Apenas são permitidas inputs SSML válidas no pedido de síntese de voz. | Certifique-se de que as expressões SSML de entrada estão corretas. |
|        | 400 | O nome de voz {voiceName} não encontra-se no ficheiro de entrada. | O nome de voz SSML de entrada não está alinhado com o ID do modelo. |
|        | 400 | O número de parágrafos no ficheiro de entrada deve ser inferior a 10.000. | Certifique-se de que o parágrafo no ficheiro é inferior a 10.000. |
|        | 400 | O ficheiro de entrada deve ser superior a 400 caracteres. | Certifique-se de que o seu ficheiro de entrada excede 400 caracteres. |
|        | 404 | O modelo declarado na definição de síntese de voz não pode ser encontrado: {modelID}. | Certifique-se de que o {modelID} está correto. |
|        | 429 | Exceda o limite de síntese de voz ativa. Por favor, espere até que alguns pedidos terminem. | O servidor está autorizado a executar e fazer fila até 120 pedidos para cada conta Azure. Por favor, aguarde e evite submeter novos pedidos até que alguns pedidos estejam concluídos. |
| Todos       | 429 | Há muitos pedidos. | O cliente está autorizado a submeter até 5 pedidos ao servidor por segundo para cada conta Azure. Por favor, reduza o valor do pedido por segundo. |
| Eliminar    | 400 | A tarefa de síntese de voz ainda está em uso. | Só pode eliminar pedidos que sejam **concluídos** ou **falhados**. |
| GetByID   | 404 | A entidade especificada não pode ser encontrada. | Certifique-se de que o ID de síntese está correto. |

## <a name="regions-and-endpoints"></a>Regiões e pontos finais

A API áudio Longa está disponível em várias regiões com pontos finais únicos.

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Canadá Central | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| E.U.A. Leste | `https://eastus.customvoice.api.speech.microsoft.com` |
| Índia Central | `https://centralindia.customvoice.api.speech.microsoft.com` |
| E.U.A. Centro-Sul | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Ásia Sudeste | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |
| E.U.A.Oeste 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Apoiamos formatos flexíveis de saída de áudio. Pode gerar saídas de áudio por parágrafo ou concatenar as saídas de áudio numa única saída, definindo o parâmetro 'concatenateResult'. Os seguintes formatos de saída de áudio são suportados pela API de Áudio Longo:

> [!NOTE]
> O formato de áudio padrão é riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* áudio-16khz-32kbitrate-mono-mp3
* áudio-16khz-64kbitrate-mono-mp3
* áudio-16khz-128kbitrate-mono-mp3
* áudio-24khz-48kbitrate-mono-mp3
* áudio-24khz-96kbitrate-mono-mp3
* áudio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Inícios rápidos

Oferecemos quickstarts projetados para ajudá-lo a executar a API de Áudio Longo com sucesso. Esta tabela inclui uma lista de quickstarts DaAPi de Áudio Longo organizados por linguagem.

* [Quickstart: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de amostra para API de Áudio Longo está disponível no GitHub.

* [Código da amostra: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código da amostra: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código da amostra: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
