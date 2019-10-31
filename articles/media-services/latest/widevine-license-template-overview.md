---
title: Visão geral dos serviços de mídia do Azure com o modelo de licença do Widevine | Microsoft Docs
description: Este tópico fornece uma visão geral de um modelo de licença do Widevine que é usado para configurar licenças do Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 30e5daba56bb371aafa6d2636a0a9f641977e6fa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162561"
---
# <a name="widevine-license-template-overview"></a>Visão geral do modelo de licença do Widevine 

Os serviços de mídia do Azure permitem que você criptografe seu conteúdo com o **Google Widevine**. Os serviços de mídia também fornecem um serviço para fornecer licenças Widevine. Você pode usar as APIs dos serviços de mídia do Azure para configurar licenças do Widevine. Quando um Player tenta reproduzir seu conteúdo protegido por Widevine, uma solicitação é enviada para o serviço de entrega de licença para obter a licença. Se o serviço de licença aprovar a solicitação, o serviço emitirá a licença. Ele é enviado para o cliente e é usado para descriptografar e reproduzir o conteúdo especificado.

Uma solicitação de licença Widevine é formatada como uma mensagem JSON.  

>[!NOTE]
> Você pode criar uma mensagem vazia sem valores, apenas "{}". Em seguida, um modelo de licença é criado com padrões. O padrão funciona na maioria dos casos. Cenários de entrega de licença baseados em Microsoft sempre devem usar os padrões. Se você precisar definir os valores "Provider" e "content_id", um provedor deverá corresponder a credenciais do Widevine.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage":<renew with usage>
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
Se houver uma política pré-existente, não será necessário especificar nenhum dos valores na especificação de chave de conteúdo. A política pré-existente associada a esse conteúdo é usada para determinar a proteção de saída, como o Proteção de Conteúdo Digital de alta largura de banda (HDCP) e o sistema de gerenciamento geral de cópia (CGMS). Se uma política pré-existente não estiver registrada no servidor de licença Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.   

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
| policy_overrides&#46;can_play |Booliano, verdadeiro ou falso |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides&#46;can_persist |Booliano, verdadeiro ou falso |Indica que a licença pode ser persistida para o armazenamento não volátil para uso offline. A predefinição é falso. |
| policy_overrides&#46;can_renew |Booliano, verdadeiro ou falso |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. A predefinição é falso. |
| policy_overrides&#46;license_duration_seconds |Int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Indica a janela de tempo enquanto a reprodução é permitida. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |A janela de exibição do tempo após a reprodução começar dentro da duração da licença. Um valor de 0 indica que não há nenhum limite para a duração. O padrão é 0. |
| policy_overrides&#46;renewal_server_url |string |Todas as solicitações de pulsação (renovação) para essa licença são direcionadas para a URL especificada. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |O número de segundos após o license_start_time antes da primeira tentativa de renovação. Esse campo só será usado se can_renew for verdadeiro. O padrão é 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Especifica o atraso em segundos entre as solicitações de renovação de licença subsequentes, em caso de falha. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |A janela de tempo em que a reprodução pode continuar durante a tentativa de renovação, mas sem êxito devido a problemas de back-end com o servidor de licença. Um valor de 0 indica que não há nenhum limite para a duração. Esse campo só será usado se can_renew for verdadeiro. |
| policy_overrides&#46;renew_with_usage |Booliano, verdadeiro ou falso |Indica que a licença é enviada para renovação quando o uso é iniciado. Esse campo só será usado se can_renew for verdadeiro. |

## <a name="session-initialization"></a>Inicialização de sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em base64 |Esse token de sessão é passado de volta na licença e existe em renovações subsequentes. O token de sessão não persiste além das sessões. |
| provider_client_token |Cadeia de caracteres codificada em base64 |Token de cliente para enviar de volta na resposta da licença. Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persiste além das sessões de licença. |
| override_provider_client_token |Booliano, verdadeiro ou falso |Se false e a solicitação de licença contiver um token de cliente, use o token da solicitação, mesmo que um token de cliente tenha sido especificado nessa estrutura. Se for true, sempre use o token especificado nessa estrutura. |

## <a name="configure-your-widevine-license-with-net"></a>Configurar sua licença do Widevine com o .NET 

Os serviços de mídia fornecem uma classe que permite configurar uma licença do Widevine. Para construir a licença, passe JSON para [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Para configurar o modelo, você pode:

### <a name="directly-construct-a-json-string"></a>Construir uma cadeia de caracteres JSON diretamente

Esse método pode ser propenso a erros. É recomendável usar outro método, descrito em [definir as classes necessárias e serializar para JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a>Definir as classes necessárias e serializar para JSON

#### <a name="define-classes"></a>Definir classes

O exemplo a seguir mostra um exemplo de definições de classes que são mapeadas para o esquema JSON Widevine. Você pode instanciar as classes antes de serializá-las para cadeia de caracteres JSON.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Configurar a licença

Use classes definidas na seção anterior para criar JSON que é usado para configurar o [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>Passos seguintes

Confira como [proteger com DRM](protect-with-drm.md)
