---
title: Azure Media Services v3 com visão geral do modelo de licença Widevine
description: Este tópico dá uma visão geral de um modelo de licença Widevine que é usado para configurar licenças Widevine.
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
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76705637"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Media Services v3 com visão geral do modelo de licença Widevine

O Azure Media Services permite-lhe encriptar os seus conteúdos com o **Google Widevine**. A Media Services também presta um serviço para a entrega de licenças Widevine. Pode utilizar APIs da Azure Media Services para configurar as licenças widevine. Quando um jogador tenta reproduzir o seu conteúdo protegido pela Widevine, um pedido é enviado para o serviço de entrega de licença para obter a licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. É enviado para o cliente e é usado para desencriptar e reproduzir o conteúdo especificado.

Um pedido de licença widevine é formatado como uma mensagem JSON.  

>[!NOTE]
> Pode criar uma mensagem vazia sem{}valores, apenas." Em seguida, um modelo de licença é criado com incumprimentos. O padrão funciona para a maioria dos casos. Os cenários de entrega de licençabaseados na Microsoft devem sempre utilizar os predefinidos. Se precisar de definir os valores de "fornecedor" e "content_id", um fornecedor deve corresponder às credenciais da Widevine.

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
| payload |Fio codificado Base64 |O pedido de licença enviado por um cliente. |
| content_id |Fio codificado Base64 |Identificador usado para obter a chave ID e chave de conteúdo para cada content_key_specs.track_type. |
| fornecedor |string |Costumava procurar chaves de conteúdo e políticas. Se a entrega da chave da Microsoft for utilizada para a entrega da licença Widevine, este parâmetro é ignorado. |
| policy_name |string |Nome de uma política previamente registada. Opcional. |
| allowed_track_types |enum |SD_ONLY ou SD_HD. Controla quais as chaves de conteúdo incluídas numa licença. |
| content_key_specs |Conjunto de estruturas JSON, consulte a secção "Especificações de chave de conteúdo".  |Um controlo mais fino sobre as chaves de conteúdo para devolver. Para mais informações, consulte a secção "Especificações de chave de conteúdo". Apenas um dos valores allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |Boolean, verdadeiro ou falso |Utilize atributos de política especificados por policy_overrides e omita todas as políticas previamente armazenadas. |
| policy_overrides |Estrutura JSON, veja a secção "A política substitui". |Definições políticas para esta licença.  No caso de este ativo ter uma política predefinida, estes valores especificados são utilizados. |
| session_init |Estrutura JSON, consulte a secção "Inicialização da sessão". |Os dados opcionais são passados para a licença. |
| parse_only |Boolean, verdadeiro ou falso |O pedido de licença é analisado, mas nenhuma licença é emitida. No entanto, os valores do pedido de licença são devolvidos na resposta. |

## <a name="content-key-specs"></a>Especificações de chave de conteúdo
Se existir uma política pré-existente, não é necessário especificar nenhum dos valores na especificação da chave de conteúdo. A política pré-existente associada a este conteúdo é utilizada para determinar a proteção de saída, como a Proteção de Conteúdos Digitais de Alta Largura (HDCP) e o Sistema de Gestão Geral de Cópias (CGMS). Se uma apólice pré-existente não estiver registada no servidor de licença widevine, o fornecedor de conteúdos pode injetar os valores no pedido de licença.   

Cada content_key_specs valor deve ser especificado para todas as faixas, independentemente da opção use_policy_overrides_exclusively. 

| Nome | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome tipo de pista. Se content_key_specs especificado no pedido de licença, certifique-se de especificar explicitamente todos os tipos de faixas. Não o fazer resulta em não ter reprodução nos últimos 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos de robustez do cliente para a reprodução. <br/> - É necessária criptografia de caixa branca baseada em software. <br/> - São necessárias criptografia de software e um descodificador obfuscado. <br/> - As operações de material-chave e criptografia devem ser realizadas num ambiente de execução fidedigno apoiado em hardware. <br/> - A criptografia e a descodificação de conteúdos devem ser realizadas num ambiente de execução fidedigno apoiado em hardware.  <br/> - A criptografia, a descodificação e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados num ambiente de execução fidedigno apoiado em hardware. |
| content_key_specs <br/> required_output_protection.hdc |cadeia, um dos HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se o HDCP é necessário. |
| content_key_specs <br/>key |Base64-<br/>cadeia codificada |Chave de conteúdo a utilizar para esta faixa. Se especificado, é necessário track_type ou key_id. O fornecedor de conteúdos pode usar esta opção para injetar a chave de conteúdo para esta faixa em vez de deixar o servidor de licença Widevine gerar ou procurar uma chave. |
| content_key_specs.key_id |Binário de cordas codificado base64, 16 bytes |Identificador único para a chave. |

## <a name="policy-overrides"></a>Sobreposições políticas
| Nome | Valor | Descrição |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, verdadeiro ou falso |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides&#46;can_persist |Boolean, verdadeiro ou falso |Indica que a licença pode ser persistiu no armazenamento não volátil para uso offline. A predefinição é falso. |
| policy_overrides&#46;can_renew |Boolean, verdadeiro ou falso |Indica que a renovação desta licença é permitida. Se for verdade, a duração da licença pode ser prolongada por batimentos cardíacos. A predefinição é falso. |
| policy_overrides&#46;license_duration_seconds |int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não há limite para a duração. Padrão é 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Indica a janela de tempo enquanto é permitida a reprodução. Um valor de 0 indica que não há limite para a duração. Padrão é 0. |
| policy_overrides playback_duration_seconds&#46; |int64 |A janela de visualização do tempo após a reprodução começa dentro da duração da licença. Um valor de 0 indica que não há limite para a duração. Padrão é 0. |
| policy_overrides&#46;renewal_server_url |string |Todos os pedidos de batimentos cardíacos (renovação) para esta licença são direcionados para o URL especificado. Este campo só é usado se can_renew for verdade. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Quantos segundos depois de license_start_time antes da primeira tentativa de renovação. Este campo só é usado se can_renew for verdade. Padrão é 0. |
| policy_overrides renewal_retry_interval_seconds&#46; |int64 |Especifica o atraso em segundos entre os pedidos de renovação da licença subsequentes, em caso de falha. Este campo só é usado se can_renew for verdade. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |A janela de tempo em que a reprodução pode continuar enquanto a renovação é tentada, mas sem sucesso devido a problemas no back-end com o servidor de licença. Um valor de 0 indica que não há limite para a duração. Este campo só é usado se can_renew for verdade. |
| policy_overrides renew_with_usage&#46; |Boolean, verdadeiro ou falso |Indica que a licença é enviada para renovação quando o uso começa. Este campo só é usado se can_renew for verdade. |

## <a name="session-initialization"></a>Inicialização da sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Fio codificado Base64 |Esta sessão é retransmitida na licença e existe em renovações subsequentes. O símbolo da sessão não persiste para além das sessões. |
| provider_client_token |Fio codificado Base64 |Ficha do cliente para enviar de volta na resposta da licença. Se o pedido de licença contiver um símbolo do cliente, este valor é ignorado. O símbolo do cliente persiste para além das sessões de licença. |
| override_provider_client_token |Boolean, verdadeiro ou falso |Se o falso e o pedido de licença contiver um token do cliente, utilize o símbolo do pedido mesmo que um token de cliente tenha sido especificado nesta estrutura. Se for verdade, utilize sempre o símbolo especificado nesta estrutura. |

## <a name="configure-your-widevine-license-with-net"></a>Configure a sua licença Widevine com .NET 

A Media Services fornece uma classe que lhe permite configurar uma licença Widevine. Para construir a licença, passe jSON para [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Para configurar o modelo, pode:

### <a name="directly-construct-a-json-string"></a>Construa diretamente uma corda JSON

Este método pode ser propenso a erros. Recomenda-se a utilização de outro método, descrito nas classes necessárias de [Defini e serializar-se para jSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Defina as classes necessárias e serialize para a JSON

#### <a name="define-classes"></a>Definir aulas

O exemplo que se segue mostra um exemplo de definições de classes que mapeiam para o esquema Widevine JSON. Pode instantaneamente as aulas antes de as serializar para a corda JSON.  

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

#### <a name="configure-the-license"></a>Configure a licença

Utilize as classes definidas na secção anterior para criar o JSON que é usado para configurar [o Modelo widevine:](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)

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

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

Confira como [proteger com a DRM](protect-with-drm.md)
