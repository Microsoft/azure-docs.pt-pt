---
title: Azure Media Services embalagens e erros de origem / Microsoft Docs
description: Este tópico descreve erros que poderá receber do serviço Azure Media Streaming Endpoint (Orgin).
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8442c52052a2016da002d392c6fe0834b3813229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295330"
---
# <a name="streaming-endpoint-origin-errors"></a>Erros do Ponto Final de Transmissão em Fluxo (Origem)

Este tópico descreve erros que poderá receber do serviço Azure Media [Streaming Endpoint](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Pedido Incorreto

O pedido contém informações inválidas e é rejeitado com estes códigos de erro e devido a uma das seguintes razões:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Uma sintaxe URL ou erro de formato. Exemplos incluem pedidos de um tipo inválido, um fragmento inválido ou uma pista inválida. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|O pedido não tem etiqueta de encriptação na URL. Os pedidos da CMAF requerem uma etiqueta de encriptação no URL. Outros protocolos configurados com mais de um tipo de encriptação também requerem a etiqueta de encriptação para desambiguação. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|O pedido de armazenamento para cumprir o pedido falhou com um erro de Mau Pedido. |

## <a name="403-forbidden"></a>403 Proibido

O pedido não é permitido devido a uma das seguintes razões:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|O pedido de armazenamento para cumprir o pedido falhou com uma falha de Autenticação. Isto pode acontecer se as chaves de armazenamento forem rodadas e o serviço não puder sincronizar as chaves de armazenamento. <br/><br/>Contacte o suporte do Azure indo para [o suporte Help +](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erro de funcionamento de armazenamento, o acesso falhou devido a permissões de conta insuficientes. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |O pedido de armazenamento para cumprir o pedido falhou porque a conta de armazenamento está desativada. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erro de funcionamento de armazenamento, o acesso falhou devido a erros genéricos. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |O formato de saída está bloqueado devido à configuração na StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A encriptação é necessária para o conteúdo, a política de entrega é necessária para o formato de saída. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A encriptação não está definida nas definições da política de entrega. |

## <a name="404-not-found"></a>404 Não Encontrado

A operação está a tentar agir com base num recurso que já não existe. Por exemplo, o recurso pode já ter sido eliminado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A pista solicitada não foi encontrada. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |O recurso solicitado não é encontrado. |
|MPE_UNAUTHORIZED |0x80890244 |O acesso não é autorizado. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |O tempotando solicitado não é encontrado. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |O filtro manifesto dinâmico solicitado não é encontrado. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |O índice de fragmentos solicitado está fora do alcance válido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Não é possível encontrar entradas em direto para obter tampão moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Incapaz de encontrar o fragmento no momento solicitado para uma determinada pista.<br/><br/>Pode ser que o fragmento não esteja guardado. Experimente uma camada diferente da apresentação que possa ter um fragmento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Incapaz de encontrar a entrada mediática para o bitrate solicitado no manifesto. <br/><br/>Pode ser que o jogador tenha pedido uma pista de vídeo de uma certa bitrate que não estava no manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Incapaz de encontrar certos metadados no manifesto ou incapaz de encontrar a rebase do armazenamento. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erro de funcionamento de armazenamento, recurso não encontrado. |

## <a name="409-conflict"></a>409 Conflito

O ID fornecido para um recurso numa `PUT` ou `POST` operação foi tomado por um recurso existente. Utilize outro ID para o recurso para resolver este problema.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erro de funcionamento de armazenamento, erro de conflito.  |

## <a name="410"></a>410

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Para transmissão em direto, quando o filtro que tem o set demp de fim de semana de força, o cartão de tempo de início ou fim está fora da janela atual do DVR.|

## <a name="412-precondition-failure"></a>412 Pré-condição Falha

A operação especificou um eTag que é diferente da versão disponível no servidor, ou seja, um erro de concordância otimista. Repita o pedido depois de ler a versão mais recente do recurso e atualizar a eTag no pedido.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |O fragmento solicitado não está pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erro de funcionamento de armazenamento, uma falha de pré-condição.|

## <a name="415-unsupported-media-type"></a>415 Tipo de Mídia Não Suportado

O formato de carga útil enviado pelo cliente encontra-se num formato não suportado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Não deve aplicar encriptação em conteúdo já encriptado.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A encriptação é inválida para o formato de entrada.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| O tipo de política de entrega é inválido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |As definições originais podem ser partilhadas por vários formatos de saída.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|O formato ou tipo de suporte não é suportado. Por exemplo, os Serviços de Comunicação Não suportam a contagem de nível de qualidade superior a 64. Na tag de vídeo FLV, os Media Services não suportam uma moldura de vídeo com vários SPS e vários PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| O formato de entrada do ativo solicitado não é suportado. Os Serviços de Media suportam formatos de descarregamento Smooth (ao vivo), MP4 (VoD) e Progressive.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|O formato de saída solicitado não é suportado. Os Serviços de Comunicação Social suportam formatos de descarregamento Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) e Progressive download.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Encontrou um tipo de encriptação não suportado.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|O tipo de suporte solicitado não é suportado pelo formato de saída. Os tipos suportados são legendas de vídeo, áudio ou "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Os meios de origem foram codificados com um formato de media que não é compatível com o formato de saída.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|O ativo de origem foi codificado com um formato de vídeo que não é compatível com o formato de saída. H.264, AVC, H.265 (HEVC, hev1 ou hvc1) são suportados.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|O ativo de origem foi codificado com um formato áudio que não é compatível com o formato de saída. Os formatos áudio suportados são AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|O ativo protegido de origem não pode ser convertido para o formato de saída.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|O formato de proteção não é suportado pelo formato de saída.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|O formato de proteção não é suportado pelo formato de entrada.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unidade NAL de vídeo inválida, por exemplo, apenas o primeiro NAL da amostra pode ser um AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Tamanho da unidade NAL inválida.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valor inválido do comprimento da unidade NAL.|
|MPE_FILTER_INVALID|0x80890236|Filtros manifestos dinâmicos inválidos.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versões de filtro inválidas ou não suportadas.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo de filtro inválido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|O alcance inválido é especificado pelo filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|O atributo de via inválido é especificado pelo filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|O comprimento da janela de apresentação inválida é especificado pelo filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|O filtro especifica-se o filtro para o retrocesso inválido.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Apenas um elemento absTimeInHNS é suportado em filtros antigos.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Não há mais correntes depois de aplicar os filtros.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|O regresso ao vivo é para lá da janela do DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|O regresso ao vivo é maior do que a janela de apresentação.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Excedeu dez (10) filtros predefinidos máximos permitidos.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Não é permitido um operador de primeira qualidade de vídeo em filtros de pedido combinados.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|O número de atributos bitrate de primeira qualidade deve ser um (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|A duração do segmento HLS deve ser inferior a um terço da janela do DVR e o HLS recuar.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|As durações dos fragmentos devem ser inferiores ou iguais a aproximadamente 20 segundos, ou os níveis de qualidade de entrada não estão alinhados no tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erro específico do DTS, não pode encontrar a Caixa Reservada quando deve apresentar na Caixa DTSSpecfic durante a análise da caixa DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Erro específico do DTS, nenhum canal encontrado na Caixa DTSS durante a análise da caixa DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Erro específico do DTS, desfasamento do tipo de amostra na Caixa de Controlo DTSS.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erro específico do DTS, multi-activo é definido, mas dTSH tipo de amostra desajustado.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erro específico do DTS, o tamanho do fluxo do núcleo é inválido.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erro específico do DTS, a resolução da amostra é inválida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Erro específico do DTS, o índice de extensão do sub-fluxo é inválido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erro específico do DTS, o número do bloco de sub-fluxo é inválido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erro específico do DTS, a frequência de amostragem é inválida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Erro específico do DTS, o código do relógio de referência na extensão do sub-fluxo é inválido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erro específico do DTS, o número de remaps de altifalantes definido é inválido.|

Para artigos de encriptação e exemplos, consulte:

- [Conceito: proteção de conteúdos](content-protection-overview.md)
- [Conceito: Políticas-chave de conteúdo](content-key-policy-concept.md)
- [Conceito: Políticas de streaming](streaming-policy-concept.md)
- [Amostra: proteger com encriptação AES](protect-with-aes128.md)
- [Amostra: proteger com DRM](protect-with-drm.md)

Para obter orientação do filtro, consulte:

- [Conceito: manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Conceito: filtros](filters-concept.md)
- [Amostra: criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Amostra: criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Amostra: criar filtros com CLI](filters-dynamic-manifest-cli-howto.md)

Para artigos e amostras ao vivo, consulte:

- [Conceito: visão geral do streaming ao vivo](live-streaming-overview.md)
- [Conceito: Eventos ao vivo e saídas ao vivo](live-events-outputs-concept.md)
- [Amostra: tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Intervalo Não Satisfatório

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erro de funcionamento de armazenamento, erro de 416, intervalo inválido.|

## <a name="500-internal-server-error"></a>Erro Interno do Servidor 500

Durante o processamento do pedido, os Serviços de Comunicação encontram algum erro que impede que o processamento continue.  

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erro de funcionamento de armazenamento, InternalError geral de um dos erros HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erro de funcionamento de armazenamento, OperaçãoTimedOut geral de um dos erros HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erro de funcionamento de armazenamento, outros erros HTTP 500 do que o InternalError ou o OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Serviço Indisponível

O servidor não consegue receber pedidos. Este erro pode ser causado por excesso de pedidos ao serviço. O mecanismo de limitação dos Serviços de Multimédia restringe a utilização de recursos para aplicações que fazem pedidos em excesso ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de código do erro para obter informações mais detalhadas sobre o motivo pelo qual obteve o erro 503. Este erro nem sempre significa limitação.
> 

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erro de funcionamento de armazenamento, recebeu o erro de ocupado do servidor HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Consulte também

- [Encoding error codes](/rest/api/media/jobs/get#joberrorcode) (Códigos de erro de codificação)
- [Conceitos de Azure Media Services](concepts-overview.md)
- [Quotas e limites](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passos seguintes

[Exemplo: acesso ErrorCode e Mensagem da ApiException com .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
