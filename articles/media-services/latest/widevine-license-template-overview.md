---
title: Visão geral do modelo de licença de mídia v3 widevine
description: Saiba mais sobre o Azure Media Services com o modelo de licença Widevine e como é usado para configurar licenças widevine.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 39cccd270a4947820640940ae43fa0feb3e52028
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954484"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Serviços de Mídia v3 com visão geral do modelo de licença widevine

O Azure Media Services permite-lhe encriptar o seu conteúdo com **o Google Widevine**. Os Serviços de Comunicação Social também fornecem um serviço para a entrega de licenças Widevine. Pode utilizar apis da Azure Media Services para configurar licenças widevine. Quando um jogador tenta reproduzir o seu conteúdo protegido por Widevine, é enviado um pedido ao serviço de entrega de licenças para obter a licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. É enviado para o cliente e é usado para desencriptar e reproduzir o conteúdo especificado.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Um pedido de licença Widevine é formatado como uma mensagem JSON.  



```json
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
```

>[!NOTE]
> Pode criar uma mensagem vazia sem valores, apenas {} " " Em seguida, um modelo de licença é criado com predefinições. O padrão funciona para a maioria dos casos. Os cenários de entrega de licenças baseados na Microsoft devem sempre utilizar os padrão. Se precisar de definir os valores "fornecedor" e "content_id", um fornecedor deve corresponder às credenciais de Widevine.

## <a name="json-message"></a>Mensagem JSON

| Name | Valor | Descrição |
| --- | --- | --- |
| payload |Cadeia codificada base64 |O pedido de licença enviado por um cliente. |
| content_id |Cadeia codificada base64 |O identificador utilizado para obter o ID da chave e a chave de conteúdo para cada content_key_specs.track_type. |
| provedor |string |Costumava procurar chaves e políticas de conteúdo. Se a entrega da chave da Microsoft for utilizada para a entrega da licença Widevine, este parâmetro é ignorado. |
| policy_name |string |Nome de uma apólice previamente registada. Opcional. |
| allowed_track_types |enum |SD_ONLY ou SD_HD. Controlos que chaves de conteúdo estão incluídas numa licença. |
| content_key_specs |Matriz de estruturas JSON, consulte a secção "Especificações chave de conteúdo".  |Um controlo mais fino sobre quais as chaves de conteúdo para devolver. Para mais informações, consulte a secção "Especificações de chave de conteúdo". Apenas um dos valores allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |Booleano, verdadeiro ou falso |Utilize atributos de política especificados por policy_overrides e omita toda a política previamente armazenada. |
| policy_overrides |Estrutura JSON, veja a secção "A política sobrepõe-se". |Definições de política para esta licença.  No caso de este ativo ter uma política predefinida, estes valores especificados são utilizados. |
| session_init |Estrutura JSON, consulte a secção "Iniciação de Sessão". |Os dados opcionais são transmitidos à licença. |
| parse_only |Booleano, verdadeiro ou falso |O pedido de licença é analisado, mas nenhuma licença é emitida. No entanto, os valores do pedido de licença são devolvidos na resposta. |

## <a name="content-key-specs"></a>Especificações-chave de conteúdo
Se existir uma política pré-existente, não há necessidade de especificar nenhum dos valores na especificação de chave de conteúdo. A política pré-existente associada a este conteúdo é utilizada para determinar a proteção de saída, como a Proteção de Conteúdo Digital de Alta largura (HDCP) e o Sistema de Gestão Geral de Cópias (CGMS). Se uma política pré-existente não estiver registada no servidor de licença widevine, o fornecedor de conteúdos pode injetar os valores no pedido de licença.   

Cada content_key_specs valor deve ser especificado para todas as faixas, independentemente da opção use_policy_overrides_exclusively. 

| Name | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome do tipo de faixa. Se content_key_specs for especificado no pedido de licença, certifique-se de especificar explicitamente todos os tipos de faixas. Se não o fizer, resulta em não voltar a jogar depois de 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos de robustez do cliente para a reprodução. <br/> - É necessária uma criptografia de caixa branca baseada em software. <br/> - São necessárias criptografia de software e um descodificador obfuscado. <br/> - As operações de material-chave e criptografia devem ser efetuadas num ambiente de execução fidedigno apoiado por hardware. <br/> - A criptografia e a descodão dos conteúdos devem ser efetuadas num ambiente de execução fidedigno apoiado por hardware.  <br/> - A criptografia, a descodão e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados num ambiente de execução fidedigno apoiado por hardware. |
| content_key_specs <br/> required_output_protection.hdc |corda, um dos HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se o HDCP é necessário. |
| content_key_specs <br/>key |Base64-<br/>corda codificada |Chave de conteúdo para usar para esta faixa. Se especificado, é necessário track_type ou key_id. O fornecedor de conteúdo pode usar esta opção para injetar a chave de conteúdo para esta faixa em vez de permitir que o servidor de licença Widevine gere ou procure uma chave. |
| content_key_specs.key_id |Binário de cordas codificado base64, 16 bytes |Identificador único para a chave. |

## <a name="policy-overrides"></a>A política sobrepõe-se
| Name | Valor | Descrição |
| --- | --- | --- |
| policy_overrides&#46;can_play |Booleano, verdadeiro ou falso |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides&#46;can_persist |Booleano, verdadeiro ou falso |Indica que a licença pode ser persistido para armazenamento nãovolásimo para uso offline. A predefinição é falso. |
| policy_overrides&#46;can_renew |Booleano, verdadeiro ou falso |Indica que a renovação desta licença é permitida. Se for verdade, a duração da licença pode ser prolongada por batimentos cardíacos. A predefinição é falso. |
| policy_overrides&#46;license_duration_seconds |int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não há limite para a duração. O padrão é 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Indica a janela de tempo enquanto a reprodução é permitida. Um valor de 0 indica que não há limite para a duração. O padrão é 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |A janela de visualização do tempo após a reprodução começa dentro da duração da licença. Um valor de 0 indica que não há limite para a duração. O padrão é 0. |
| policy_overrides&#46;renewal_server_url |string |Todos os pedidos de batimentocardíaco (renovação) para esta licença são direcionados para o URL especificado. Este campo só é usado se can_renew for verdade. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Quantos segundos depois license_start_time antes da renovação ser tentada pela primeira vez. Este campo só é usado se can_renew for verdade. O padrão é 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre os pedidos subsequentes de renovação da licença, em caso de falha. Este campo só é usado se can_renew for verdade. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |A janela de tempo em que a reprodução pode continuar enquanto a renovação é tentada, mas sem sucesso devido a problemas de back-end com o servidor de licença. Um valor de 0 indica que não há limite para a duração. Este campo só é usado se can_renew for verdade. |
| policy_overrides&#46;renew_with_usage |Booleano, verdadeiro ou falso |Indica que a licença é enviada para renovação quando a utilização começa. Este campo só é usado se can_renew for verdade. |

## <a name="session-initialization"></a>Inicialização da sessão
| Name | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia codificada base64 |Esta sessão é repercutido na licença e existe em renovações subsequentes. O token da sessão não persiste além das sessões. |
| provider_client_token |Cadeia codificada base64 |Ficha do cliente para enviar de volta a resposta da licença. Se o pedido de licença contiver um símbolo de cliente, este valor é ignorado. O símbolo do cliente persiste para além das sessões de licença. |
| override_provider_client_token |Booleano, verdadeiro ou falso |Se for falso e o pedido de licença contiver um símbolo de cliente, use o token do pedido mesmo que um token cliente tenha sido especificado nesta estrutura. Se for verdade, utilize sempre o símbolo especificado nesta estrutura. |

## <a name="configure-your-widevine-license-with-net"></a>Configure a sua licença Widevine com .NET 

A Media Services fornece uma classe que permite configurar uma licença Widevine. Para construir a licença, passe jSON para [WidevineTemplate.](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)

Para configurar o modelo, pode:

### <a name="directly-construct-a-json-string"></a>Construa diretamente uma corda JSON

Este método pode ser propenso a erros. Recomenda-se a utilização de outro método, descrito em [Define as classes necessárias e serialize para JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Definir as aulas necessárias e serializar para JSON

#### <a name="define-classes"></a>Definir aulas

O exemplo a seguir mostra um exemplo de definições de classes que mapeiam para o esquema de Widevine JSON. Pode instantanear as aulas antes de as serializar para a corda JSON.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Configure a licença

Utilize classes definidas na secção anterior para criar json que é usado para configurar [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

Confira como [proteger com DRM](protect-with-drm.md)
