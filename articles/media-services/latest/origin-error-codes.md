---
title: Erros do Azure de empacotamento e a origem de serviços de multimédia | Documentos da Microsoft
description: Este tópico descreve os erros que possa receber do serviço de empacotamento de serviços de multimédia do Azure.
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
ms.date: 04/28/2019
ms.author: juliako
ms.openlocfilehash: e30c51ff3526bb5ed193b65b3f36a64c552024ff
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141139"
---
# <a name="media-services-packaging-errors"></a>Erros de empacotamento de serviços de multimédia 

Este tópico descreve os erros que possa receber dos serviços de multimédia do Azure [serviço de empacotamento](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 pedido inválido

O pedido contém informação inválida e foi rejeitado com esses códigos de erro e devido a um dos seguintes motivos:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Um erro de sintaxe ou formato de URL. Os exemplos incluem pedidos para um tipo inválido, um fragmento inválido ou um controle inválido. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|O pedido não tem nenhuma marca de encriptação no URL. Pedidos CMAF requerem uma marca de encriptação no URL. Outros protocolos que estão configurados com mais de um tipo de encriptação também exigem a marca de encriptação de desambiguação. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|O pedido para o armazenamento para satisfazer o pedido falhou com um erro de pedido incorreto. |

## <a name="403-forbidden"></a>403 Proibido

O pedido não é permitido devido a um dos seguintes motivos:

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|O pedido para o armazenamento para satisfazer o pedido falhou com uma falha de autenticação. Isto pode acontecer se as chaves de armazenamento foram giradas e o serviço não foi possível sincronizar as chaves de armazenamento. <br/><br/>Entre em contato com Azure suportam acedendo a [ajuda + suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erro de operação de armazenamento, acesso falhou devido a permissões insuficientes de conta. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |O pedido para o armazenamento para satisfazer o pedido falhou porque a conta de armazenamento está desativado. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erro de operação de armazenamento, acesso falhou devido a erros genéricos. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |O formato de saída está bloqueado devido à configuração no StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A encriptação é necessária para o conteúdo, a política de entrega é necessária para o formato de saída. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Encriptação não está definida nas definições de política de entrega. |

## <a name="404-not-found"></a>404 Não Encontrado

A operação está a tentar tomar decisões sobre um recurso que já não existe. Por exemplo, o recurso pode ter já foi eliminado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A faixa pedida não foi encontrada. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |O recurso pedido não foi encontrado. |
|MPE_UNAUTHORIZED |0x80890244 |O acesso não está autorizado. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |O carimbo de hora pedido não foi encontrado. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |O filtro do manifesto dinâmico pedido não foi encontrado. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |O índice de pedido de fragmento está além do intervalo válido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Em direto não não possível localizar a entradas para obter o buffer de moov de suporte de dados. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Não é possível localizar o fragmento ao tempo pedido para um controle específico.<br/><br/>É possível que o fragmento não está no armazenamento. Tente uma camada diferente da apresentação que pode ter um fragmento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Não é possível localizar a entrada de suporte de dados para a velocidade de transmissão solicitada no manifesto. <br/><br/>É possível que o jogador-lhe pedido para uma faixa de vídeo de uma determinada velocidade de transmissão que não estava no manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Não é possível encontrar alguns metadados no manifesto ou não é possível encontrar rebase do armazenamento. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erro de operação de armazenamento, o recurso não foi encontrado. |

## <a name="409-conflict"></a>409 conflito

O ID fornecido para um recurso num `PUT` ou `POST` operação está a ser utilizada por um recurso existente. Utilize outro ID para o recurso para resolver este problema.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erro de operação de armazenamento, erro de conflito.  |

## <a name="410"></a>410

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Para transmissão em fluxo em direto, quando o filtro que tem forceEndTimestamp definido como VERDADEIRO, o início ou fim timestamp está fora da janela DVR atual.|

## <a name="412-precondition-failure"></a>Falha de pré-condição 412

A operação especificada uma eTag diferente da versão disponível no servidor, ou seja, um erro de simultaneidade otimista. Repita o pedido depois de ler a versão mais recente do recurso e a atualizar a eTag no pedido.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |O fragmento pedido não está pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erro de operação de armazenamento, uma falha de pré-condição.|

## <a name="415-unsupported-media-type"></a>Tipo de suporte não suportado 415

O formato do payload enviado pelo cliente está num formato não suportado.

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Não deve aplicar a encriptação no conteúdo já encriptado.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A encriptação é inválida para o formato de entrada.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Tipo de política de entrega é inválido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |As definições originais podem ser compartilhadas por vários formatos de saída.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|O formato de suporte de dados ou o tipo não é suportado. Por exemplo, os serviços de multimédia não suporta a contagem de nível de qualidade que é superior a 64. Na etiqueta de vídeo FLV, serviços de multimédia não suporta um quadro de vídeo com várias SPS e vários PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| O formato de entrada do recurso solicitado não é suportado. Serviços de multimédia suportam uniforme (ao vivo), MP4 (VoD) e progressiva transferir formatos.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|O formato de saída pedido não é suportado. Serviços de multimédia suporta uniforme, DASH (CSF, CMAF), progressiva e HLS (v3, v4, CMAF), baixe formatos.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Foi encontrado um tipo de encriptação não suportado.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|O tipo de suporte de dados pedido não é suportado pelo formato de saída. Os tipos suportados são o vídeo, áudio ou subtítulo "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|O suporte de dados do elemento de origem foi codificado com um formato de suporte de dados que não é compatível com o formato de saída.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|O elemento de origem foi codificado com um formato de vídeo que não é compatível com o formato de saída. H.264 AVC, H.265 (HEVC, hev1 ou hvc1) são suportados.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|O elemento de origem foi codificado com um formato de áudio que não é compatível com o formato de saída. Formatos de áudio suportados são AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|O recurso protegido de origem não é possível converter o formato de saída.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|O formato de proteção não é suportado pelo formato de saída.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|O formato de proteção não é suportado pelo formato de entrada.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Inválido vídeo NAL unidade, por exemplo, apenas a primeiro NAL no exemplo pode ser um AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Tamanho da unidade NAL inválido.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valor de comprimento de unidade de inválido NAL.|
|MPE_FILTER_INVALID|0x80890236|Filtros do manifesto dinâmico inválidos.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versões do filtro inválido ou não suportado.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo de filtro inválido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Intervalo inválido é especificado pelo filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Atributo de controlar inválido é especificado pelo filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Duração da janela de apresentação inválido é especificada pelo filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Em direto do término do inválido é especificado pelo filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Apenas um elemento de absTimeInHNS é suportado em filtros de legado.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Não é não fluxos mais depois de aplicar os filtros.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|O término em direto está além da janela DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|O término em direto é maior do que a janela de apresentação.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Foi excedido filtros de máximo permitido de predefinição dez (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Vários operador first de qualidade do vídeo não é permitida em filtros de pedido combinada.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|O número de atributos de velocidade de transmissão de qualidade primeiro tem de ser um (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Duração de segmento HLS tem de ser menor do que um terço da janela DVR e HLS voltar e.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Durações de fragmento tem de ser menor ou igual a cerca de 20 segundos ou os níveis de qualidade de entrada não estão alinhado de tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erro de DTS específicos, não é possível localizar o ReservedBox quando ele deve apresentar a DTSSpecficBox durante a análise de caixa DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Erro de DTS específicos, não existem canais encontrados no DTSSpecficBox durante a análise de caixa DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Erro de específica de DTS, incompatibilidade de tipo de exemplo no DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erro de específica de DTS, asset multi está definido, mas a incompatibilidade de tipo de exemplo DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erro de específica de DTS, o tamanho de fluxo de núcleos é inválido.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erro de específica de DTS, a resolução da amostra é inválida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Erro de específica de DTS, o índice de extensão do fluxo secundárias é inválido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erro de específica de DTS, número de blocos de fluxo secundária é inválido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erro de específica de DTS, a frequência de amostragem é inválida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Erro de DTS específica, o código de relógio de referência na extensão do fluxo secundária é inválido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erro de DTS específica, o número de oradores remapeamento conjunto é inválido.|

Para artigos de encriptação e exemplos, consulte:

- [Conceito: proteção de conteúdo](content-protection-overview.md)
- [Conceito: Diretivas de chave de conteúdo](content-key-policy-concept.md)
- [Conceito: Políticas de transmissão em fluxo](streaming-policy-concept.md)
- [Exemplo: proteger com encriptação AES](protect-with-aes128.md)
- [Exemplo: proteger com o DRM](protect-with-drm.md)

Para obter orientações de filtro, consulte:

- [Conceito: os manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Conceito: filtros](filters-concept.md)
- [Exemplo: criar filtros com as APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Exemplo: criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplo: criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

Para artigos em direto e exemplos, consulte:

- [Conceito: Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
- [Conceito: Eventos em direto e saídas em direto](live-events-outputs-concept.md)
- [Exemplo: tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 não variar Satisfiable

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erro de operação do armazenamento, devolvido um erro de http 416, intervalo inválido.|

## <a name="500-internal-server-error"></a>500 Erro de Servidor Interno

Durante o processamento do pedido, dos serviços de multimédia encontra um erro que impede o processamento de continuar.  

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Recebido e traduzido de Winhttp código de erro de ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Recebido e traduzido de Winhttp código de erro de ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Recebido e traduzido de Winhttp código de erro de ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erro de operação de armazenamento, InternalError geral de um dos erros de HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erro de operação de armazenamento, OperationTimedOut geral de um dos erros de HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erro de operação de armazenamento, outros erros de HTTP 500 que InternalError ou OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Serviço Indisponível

O servidor está atualmente não é possível receber pedidos. Este erro pode ser causado por excesso pedidos para o serviço. Mecanismo de limitação de serviços de multimédia restringe a utilização de recursos para aplicativos que tornam o pedido excessivos para o serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres de código de erro para obter informações mais detalhadas sobre o motivo pelo qual obteve o erro 503. Este erro não significa sempre a limitação.
> 

|Código de erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erro de operação de armazenamento, recebeu o erro de disponibilidade do servidor HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="see-also"></a>Consulte também

- [Encoding error codes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode) (Códigos de erro de codificação)
- [Conceitos de serviços de multimédia do Azure](concepts-overview.md)
- [Quotas e limitações](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passos Seguintes

[Exemplo: acessar o código de erro e mensagem de ApiException com .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
