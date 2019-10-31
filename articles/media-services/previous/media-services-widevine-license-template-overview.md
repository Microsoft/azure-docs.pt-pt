---
title: Visão geral do modelo de licença do Widevine | Microsoft Docs
description: Este tópico fornece uma visão geral de um modelo de licença do Widevine que é usado para configurar licenças do Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ab9725dd50487cf9df9d6fb967959b276f39979f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162453"
---
# <a name="widevine-license-template-overview"></a>Visão geral do modelo de licença do Widevine 
Você pode usar os serviços de mídia do Azure para configurar e solicitar licenças do Google Widevine. Quando o Player tenta reproduzir seu conteúdo protegido por Widevine, uma solicitação é enviada para o serviço de entrega de licença para obter uma licença. Se o serviço de licença aprovar a solicitação, o serviço emitirá a licença. Ele é enviado para o cliente e é usado para descriptografar e reproduzir o conteúdo especificado.

Uma solicitação de licença Widevine é formatada como uma mensagem JSON.  

>[!NOTE]
> Você pode criar uma mensagem vazia sem valores, apenas "{}". Em seguida, um modelo de licença é criado com padrões. O padrão funciona na maioria dos casos. Cenários de entrega de licença baseados em Microsoft sempre devem usar os padrões. Se você precisar definir os valores "Provider" e "content_id", um provedor deverá corresponder a credenciais do Widevine.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>Mensagem JSON
| Nome | Valor | Descrição |
| --- | --- | --- |
| Carga |Cadeia de caracteres codificada em base64 |A solicitação de licença enviada por um cliente. |
| content_id |Cadeia de caracteres codificada em base64 |Identificador usado para derivar a ID de chave e a chave de conteúdo para cada content_key_specs. track_type. |
| Operador |string |Usado para pesquisar chaves de conteúdo e políticas. Se a entrega de chave da Microsoft for usada para entrega de licença Widevine, esse parâmetro será ignorado. |
| Nome_da_Diretiva |string |Nome de uma política previamente registrada. Opcional. |
| allowed_track_types |enumera |SD_ONLY ou SD_HD. Controla quais chaves de conteúdo estão incluídas em uma licença. |
| content_key_specs |Matriz de estruturas JSON, consulte a seção "especificações de chave de conteúdo".  |Um controle mais refinado sobre quais chaves de conteúdo retornam. Para obter mais informações, consulte a seção "especificações de chave de conteúdo". Somente um dos valores allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |Booliano, verdadeiro ou falso |Use atributos de política especificados por policy_overrides e omita todas as políticas armazenadas anteriormente. |
| policy_overrides |Estrutura JSON, consulte a seção "substituições de política". |Configurações de política para esta licença.  Caso este ativo tenha uma política predefinida, esses valores especificados serão usados. |
| session_init |Estrutura JSON, consulte a seção "inicialização da sessão". |Os dados opcionais são passados para a licença. |
| parse_only |Booliano, verdadeiro ou falso |A solicitação de licença é analisada, mas nenhuma licença é emitida. No entanto, os valores da solicitação de licença são retornados na resposta. |

## <a name="content-key-specs"></a>Especificações de chave de conteúdo
Se houver uma política pré-existente, não será necessário especificar nenhum dos valores na especificação de chave de conteúdo. A política preexistente associada a esse conteúdo é usada para determinar a proteção de saída, como a HDCP (Proteção de Conteúdo Digital de alta largura de banda) e o CGMS (sistema de gerenciamento geral de cópia). Se uma política preexistente não estiver registrada com o servidor de licença Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.   

Cada valor de content_key_specs deve ser especificado para todas as faixas, independentemente da opção use_policy_overrides_exclusively. 

| Nome | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de faixa. Se content_key_specs for especificado na solicitação de licença, certifique-se de especificar explicitamente todos os tipos de controle. A falha em fazer isso resulta em falha na reprodução dos últimos 10 segundos. |
| content_key_specs  <br/> security_level |UInt32 |Define os requisitos de robustez do cliente para reprodução. <br/> -A criptografia de caixa branca baseada em software é necessária. <br/> -A criptografia de software e um decodificador ofuscado são necessários. <br/> -As principais operações de criptografia e de material devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> -A criptografia e a decodificação de conteúdo devem ser executadas em um ambiente de execução confiável com suporte de hardware.  <br/> -A criptografia, decodificação e todos os tratamentos da mídia (compactados e descompactados) devem ser tratados em um ambiente de execução confiável com suporte de hardware. |
| content_key_specs <br/> required_output_protection. hDC |Cadeia de caracteres, um de HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se HDCP é necessário. |
| content_key_specs <br/>key |Base64<br/>Cadeia de caracteres codificada |Chave de conteúdo a ser usada para esta faixa. Se especificado, o track_type ou Key_ID é necessário. O provedor de conteúdo pode usar essa opção para injetar a chave de conteúdo para esse controle, em vez de permitir que o servidor de licença do Widevine gere ou pesquise uma chave. |
| content_key_specs. Key _id |Binário de cadeia de caracteres codificada em base64, 16 bytes |Identificador exclusivo da chave. |

## <a name="policy-overrides"></a>Substituições de política
| Nome | Valor | Descrição |
| --- | --- | --- |
| policy_overrides. can_play |Booliano, verdadeiro ou falso |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides. can_persist |Booliano, verdadeiro ou falso |Indica que a licença pode ser persistida para o armazenamento não volátil para uso offline. A predefinição é falso. |
| policy_overrides. can_renew |Booliano, verdadeiro ou falso |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. A predefinição é falso. |
| policy_overrides. license_duration_seconds |Int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides. rental_duration_seconds |Int64 |Indica a janela de tempo enquanto a reprodução é permitida. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides. playback_duration_seconds |Int64 |A janela de exibição do tempo após a reprodução começar dentro da duração da licença. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides. renewal_server_url |string |Todas as solicitações de pulsação (renovação) para essa licença são direcionadas para a URL especificada. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides. renewal_delay_seconds |Int64 |O número de segundos após o license_start_time antes da primeira tentativa de renovação. Esse campo só será usado se can_renew for verdadeiro. O padrão é 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Especifica o atraso em segundos entre as solicitações de renovação de licença subsequentes, em caso de falha. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |A janela de tempo em que a reprodução pode continuar durante a tentativa de renovação, mas sem êxito devido a problemas de back-end com o servidor de licença. Um valor de 0 indica que não há nenhum limite para a duração. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides. renew_with_usage |Booliano, verdadeiro ou falso |Indica que a licença é enviada para renovação quando o uso é iniciado. Esse campo só será usado se can_renew for verdadeiro. |

## <a name="session-initialization"></a>Inicialização de sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em base64 |Esse token de sessão é passado de volta na licença e existe em renovações subsequentes. O token de sessão não persiste além das sessões. |
| provider_client_token |Cadeia de caracteres codificada em base64 |Token de cliente para enviar de volta na resposta da licença. Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persiste além das sessões de licença. |
| override_provider_client_token |Booliano, verdadeiro ou falso |Se false e a solicitação de licença contiver um token de cliente, use o token da solicitação, mesmo que um token de cliente tenha sido especificado nessa estrutura. Se for true, sempre use o token especificado nessa estrutura. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Configurar suas licenças do Widevine usando tipos .NET
Os serviços de mídia fornecem APIs .NET que você pode usar para configurar suas licenças do Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes conforme definido no SDK do .NET dos serviços de mídia
As seguintes classes são as definições desses tipos:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Exemplo
O exemplo a seguir mostra como usar as APIs do .NET para configurar uma licença simples do Widevine:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ver também
[Use PlayReady and/or Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md) (Utilizar a encriptação comum dinâmica com PlayReady e/ou Widevine)

