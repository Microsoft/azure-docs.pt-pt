---
title: Azure Media Services packaging and origin errors [ Azure Media Services) Microsoft Docs
description: Este tópico descreve erros que pode receber do serviço De streaming de serviços de mídia Azure (Orgin).
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582687"
---
# <a name="streaming-endpoint-origin-errors"></a>Erros de streaming endpoint (Origem) 

Este tópico descreve erros que pode receber do serviço De [streaming](streaming-endpoint-concept.md)de serviços de mídia Azure.

## <a name="400-bad-request"></a>400 Mau Pedido

O pedido contém informações inválidas e é rejeitado com estes códigos de erro e devido a uma das seguintes razões:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Uma sintaxe url ou erro de formato. Exemplos incluem pedidos de um tipo inválido, um fragmento inválido ou uma pista inválida. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|O pedido não tem nenhuma etiqueta de encriptação no URL. Os pedidos da CMAF requerem uma etiqueta de encriptação no URL. Outros protocolos que são configurados com mais de um tipo de encriptação também requerem a etiqueta de encriptação para desambiguação. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|O pedido de armazenamento para cumprir o pedido falhou com um erro de Pedido Mau. |

## <a name="403-forbidden"></a>403 Proibido

O pedido não é permitido devido a uma das seguintes razões:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|O pedido de armazenamento para cumprir o pedido falhou com uma falha de Autenticação. Isto pode acontecer se as chaves de armazenamento forem rodadas e o serviço não conseguir sincronizar as chaves de armazenamento. <br/><br/>Contacte o suporte azure indo para [ajudar + suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erro de operação de armazenamento, o acesso falhou devido a Permissões de conta insuficientes. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |O pedido de armazenamento para cumprir o pedido falhou porque a conta de armazenamento é desativada. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erro de operação de armazenamento, o acesso falhou devido a erros genéricos. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |O formato de saída está bloqueado devido à configuração no StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A encriptação é necessária para o conteúdo, a política de entrega é necessária para o formato de saída. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A encriptação não está definida nas definições da política de entrega. |

## <a name="404-not-found"></a>404 Não Encontrado

A operação está a tentar agir com base num recurso que já não existe. Por exemplo, o recurso pode já ter sido apagado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A pista solicitada não foi encontrada. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |O recurso solicitado não é encontrado. |
|MPE_UNAUTHORIZED |0x80890244 |O acesso não é autorizado. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |O carimbo de tempo solicitado não é encontrado. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |O filtro manifesto dinâmico solicitado não é encontrado. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |O índice de fragmentos solicitado está fora do alcance válido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Não se pode encontrar entradas de mídia ao vivo para obter tampão moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Incapaz de encontrar o fragmento na hora solicitada para uma pista particular.<br/><br/>Pode ser que o fragmento não esteja guardado. Experimente uma camada diferente da apresentação que pode ter um fragmento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Incapaz de encontrar a entrada mediática para o bitrate solicitado no manifesto. <br/><br/>Pode ser que o jogador tenha pedido uma videofaixa de um determinado bitrate que não estava no manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Incapaz de encontrar certos metadados no manifesto ou incapaz de encontrar a rebase do armazenamento. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erro de operação de armazenamento, recurso não encontrado. |

## <a name="409-conflict"></a>409 Conflito

O ID fornecido para `PUT` um `POST` recurso numa ou operação foi tomado por um recurso existente. Utilize outro ID para o recurso para resolver este problema.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erro de operação de armazenamento, erro de conflito.  |

## <a name="410"></a>410

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Para transmissão em direto, quando o filtro que tem forçaEndTimestamp definido para verdade, o carimbo de tempo de início ou fim está fora da janela DVR atual.|

## <a name="412-precondition-failure"></a>412 Falha pré-condição

A operação especificou um eTag diferente da versão disponível no servidor, ou seja, um erro de conmoeda otimista. Retry o pedido depois de ler a versão mais recente do recurso e atualizar o eTag no pedido.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |O fragmento solicitado não está pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erro de funcionamento de armazenamento, falha de pré-condição.|

## <a name="415-unsupported-media-type"></a>415 Tipo de mídia não suportado

O formato de carga útil enviado pelo cliente encontra-se num formato não suportado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Não deve aplicar encriptação em conteúdo já encriptado.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A encriptação é inválida para o formato de entrada.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| O tipo de política de entrega é inválido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |As definições originais podem ser partilhadas por vários formatos de saída.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|O formato ou tipo de mídia não é suportado. Por exemplo, a Media Services não suporta a contagem de qualidade que tem mais de 64 anos. Na tag de vídeo FLV, a Media Services não suporta uma moldura de vídeo com múltiplos SPS e múltiplas PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| O formato de entrada do ativo solicitado não é suportado. Os Media Services suportam formatos de download smooth (ao vivo), MP4 (VoD) e Progressive.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|O formato de saída solicitado não é suportado. Os Media Services suportam formatos de descarregamento liso, DASH (CSF, CMAF), HLS (v3, v4, CMAF) e Progressive.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Encontrado tipo de encriptação não suportada.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|O tipo de mídia solicitado não é suportado pelo formato de saída. Os tipos suportados são a legenda de vídeo, áudio ou "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Os meios de ativo de origem foram codificados com um formato de mídia que não é compatível com o formato de saída.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|O ativo de origem foi codificado com um formato de vídeo que não é compatível com o formato de saída. H.264, AVC, H.265 (HEVC, hev1 ou hvc1) são suportados.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|O ativo de origem foi codificado com um formato áudio que não é compatível com o formato de saída. Os formatos de áudio suportados são AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|O ativo protegido de origem não pode ser convertido para o formato de saída.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|O formato de proteção não é suportado pelo formato de saída.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|O formato de proteção não é suportado pelo formato de entrada.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unidade NAL de vídeo inválido, por exemplo, apenas o primeiro NAL na amostra pode ser um AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Tamanho inválido da unidade NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valor de comprimento da unidade NAL inválido.|
|MPE_FILTER_INVALID|0x80890236|Filtros manifestos dinâmicos inválidos.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versões de filtro inválidas ou não suportadas.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo de filtro inválido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|O intervalo inválido é especificado pelo filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|O atributo da faixa inválida é especificado pelo filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|O comprimento da janela de apresentação inválido é especificado pelo filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|O recuo inválido é especificado pelo filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Apenas um elemento absTimeInHNS é suportado em filtros legados.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Não há mais correntes depois de aplicar os filtros.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|O recuo ao vivo está para além da janela do DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|O recuo ao vivo é maior do que a janela de apresentação.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Superior a dez (10) filtros por defeito máximo permitidos.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|O operador de primeira qualidade de vídeo não é permitido em filtros de pedido combinados.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|O número de atributos bitrate de primeira qualidade deve ser um (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|A duração do segmento HLS deve ser inferior a um terço da janela DVR e o HLS recua.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|As durações dos fragmentos devem ser inferiores ou iguais a aproximadamente 20 segundos, ou os níveis de qualidade da entrada não estão alinhados no tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erro específico do DTS, não pode encontrar a Caixa Reservada quando deve apresentar-se na DTSSpecficBox durante a análise da caixa DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Erro específico do DTS, nenhum canal encontrado no DTSSpecficBox durante a análise da caixa DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Erro específico do DTS, desfasamento do tipo de amostra no DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erro específico do DTS, multi-activos é definido, mas desfasamento do tipo de amostra DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erro específico do DTS, o tamanho do fluxo do núcleo é inválido.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erro específico do DTS, resolução de amostras é inválida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Erro específico do DTS, índice de extensão de subfluxo é inválido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erro específico do DTS, número de bloco de subfluxo é inválido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erro específico do DTS, a frequência de amostragem é inválida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Erro específico do DTS, o código do relógio de referência na extensão de sub-fluxo é inválido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erro específico do DTS, o número de colunas remap definido é inválido.|

Para artigos e exemplos de encriptação, consulte:

- [Conceito: proteção de conteúdos](content-protection-overview.md)
- [Conceito: Políticas-chave de conteúdo](content-key-policy-concept.md)
- [Conceito: Políticas de streaming](streaming-policy-concept.md)
- [Amostra: proteger com encriptação AES](protect-with-aes128.md)
- [Amostra: proteger com ADR](protect-with-drm.md)

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

## <a name="416-range-not-satisfiable"></a>416 Gama Não Satisfiável

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erro de operação de armazenamento, erro devolvido http 416, intervalo inválido.|

## <a name="500-internal-server-error"></a>500 Erro de Servidor Interno

Durante o processamento do pedido, a Media Services encontra algum erro que impede que o processamento continue.  

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Recebido e traduzido do código de erro winhttp de ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erro de operação de armazenamento, erro interno geral de um dos 500 erros http 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erro de operação de armazenamento, Operação TimedOut geral de um dos 500 erros http 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erro de funcionamento do armazenamento, outros erros HTTP 500 que não o InternalError ou o OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Serviço Indisponível

O servidor não está atualmente em modo de receber pedidos. Este erro pode ser causado por pedidos excessivos ao serviço. O mecanismo de estrangulamento dos Serviços de Media restringe o uso de recursos para aplicações que fazem um pedido excessivo ao serviço.

> [!NOTE]
> Verifique a linha de erro e código de erro para obter informações mais detalhadas sobre a razão pela qual obteve o erro 503. Este erro nem sempre significa estrangulamento.
> 

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erro de operação de armazenamento, recebeu erro ocupado do servidor HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="see-also"></a>Consulte também

- [Encoding error codes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode) (Códigos de erro de codificação)
- [Conceitos de Serviços De Mídia Azure](concepts-overview.md)
- [Quotas e limites](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passos seguintes

[Exemplo: aceder ErrorCode e Mensagem de ApiException com .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
