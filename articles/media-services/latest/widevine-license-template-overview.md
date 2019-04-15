---
title: Descrição geral do modelo de licenciamento de serviços de multimédia do Azure com o Widevine | Documentos da Microsoft
description: Este tópico apresenta uma visão geral de um modelo de licença do Widevine que é utilizado para configurar as licenças do Widevine.
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
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548722"
---
# <a name="widevine-license-template-overview"></a>Descrição geral do modelo de licença do Widevine 

Serviços de multimédia do Azure permite-lhe encriptar o seu conteúdo com **Google Widevine**. Serviços de multimédia também fornecem um serviço para entregar licenças do Widevine. Pode usar APIs de serviços de multimédia do Azure para configurar as licenças do Widevine. Quando um jogador tenta reproduzir os conteúdos protegidos pelo Widevine, é enviado um pedido para o serviço de entrega de licença para obter a licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. Ele é enviado para o cliente e é utilizado para desencriptar e reproduzir o conteúdo especificado.

Um pedido de licença do Widevine é formatado como uma mensagem JSON.  

>[!NOTE]
> Pode criar apenas uma mensagem vazio com nenhum valor, "{}." Em seguida, um modelo de licença é criado com as predefinições. A predefinição trabalha para a maioria dos casos. Cenários de entrega de licença baseado em Microsoft devem sempre utilizar as predefinições. Se tiver de definir os valores de "content_id" e "fornecedor", um fornecedor tem de corresponder ao Widevine credenciais.

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
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Mensagem JSON

| Name | Valor | Descrição |
| --- | --- | --- |
| payload |Cadeia de caracteres codificada em Base64 |O pedido de licença enviado por um cliente. |
| content_id |Cadeia de caracteres codificada em Base64 |Identificador utilizado para derivar o ID de chave e o conteúdo chave para cada content_key_specs.track_type. |
| fornecedor |string |Utilizado para pesquisar chaves de conteúdo e políticas. Se a entrega de chave da Microsoft é utilizada para entrega de licenças do Widevine, este parâmetro é ignorado. |
| policy_name |string |Nome de uma política registrado anteriormente. Opcional. |
| allowed_track_types |Enum |SD_ONLY ou SD_HD. Controles que as chaves de conteúdo são incluídos numa licença. |
| content_key_specs |Matriz de JSON estruturas, consulte a secção "Especificações de chave Conteúdas."  |Um controlo mais refinado sobre as chaves de conteúdo para retornar. Para obter mais informações, consulte a secção "Especificações de chave Conteúdas." Apenas um dos valores allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |Booleano, VERDADEIRO ou FALSO |Utilizar atributos de política especificados por policy_overrides e omitir a diretiva de todos os anteriormente armazenada. |
| policy_overrides |JSON estruturar, consulte a secção "Substituições de diretiva". |Definições de política para esta licença.  No caso deste recurso tem uma política predefinida, estes valores especificados são utilizados. |
| session_init |JSON estruturar, consulte a secção "Inicialização de sessão". |Dados opcionais são passados para a licença. |
| parse_only |Booleano, VERDADEIRO ou FALSO |O pedido de licença é analisado, mas nenhuma licença é emitida. No entanto, os valores no pedido de licença são devolvidos na resposta. |

## <a name="content-key-specs"></a>Especificações de chave de conteúdo
Se existir uma política já existente, não é necessário especificar qualquer um dos valores na especificação de chave do conteúdo. A política já existente, associada este conteúdo é utilizada para determinar a proteção de saída, como a proteção de conteúdo Digital da largura de banda alta (HDCP) e o sistema de cópia gestão geral (CGMS). Se uma política já existente não estiver registrada com o servidor de licenças do Widevine, o fornecedor de conteúdos pode injetar os valores para o pedido de licença.   

Cada valor de content_key_specs tem de ser especificado para todas as faixas, independentemente da opção de use_policy_overrides_exclusively. 

| Name | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de controle. Se content_key_specs é especificado no pedido de licença, certifique-se de especificar que todos os tipos de controlar explicitamente. Falha ao fazer isso resulta em falha para reproduzir após 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos da robustez do cliente para a reprodução. <br/> -É necessária a criptografia de caixa branca baseada em software. <br/> -Criptografia de software e um Decodificador oculto são necessários. <br/> -As operações de criptografia e material de chave devem ser executadas dentro de um ambiente de execução de confiança de hardware de segurança. <br/> -A criptografia e a decodificação de conteúdo tem de ser efetuadas dentro de um ambiente de execução de confiança de hardware de segurança.  <br/> -A criptografia, decodificar e manipulação de todos os de suporte de dados (comprimidos e descomprimidos) deve ser tratados dentro de um ambiente de execução de confiança de hardware de segurança. |
| content_key_specs <br/> required_output_protection.hdc |cadeia de caracteres, um dos HDCP_V2 HDCP_NONE, HDCP_V1, |Indica se a HDCP é necessária. |
| content_key_specs <br/>key |Base64-<br/>cadeia de caracteres codificada |Chave de conteúdo a utilizar para este Roteiro. Se for especificado, é necessário o track_type ou key_id. O fornecedor de conteúdos pode utilizar esta opção para inserir a chave de conteúdo para este Roteiro em vez de permitir que o servidor de licenças do Widevine gerar ou pesquisar uma chave. |
| content_key_specs.key_id |Binário de cadeia de caracteres codificada em Base64, 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de diretiva
| Name | Valor | Descrição |
| --- | --- | --- |
| policy_overrides&#46;can_play |Booleano, VERDADEIRO ou FALSO |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides&#46;can_persist |Booleano, VERDADEIRO ou FALSO |Indica que a licença pode ser persistentes para o armazenamento não volátil para utilização offline. A predefinição é falso. |
| policy_overrides&#46;can_renew |Booleano, VERDADEIRO ou FALSO |Indica que a renovação desta licença é permitida. Se for VERDADEIRO, a duração da licença pode ser estendida por heartbeat. A predefinição é falso. |
| policy_overrides&#46;license_duration_seconds |int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Indica a janela de tempo enquanto reprodução é permitida. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |A janela de visualização de tempo depois de reprodução começar dentro da duração de licença. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides&#46;renewal_server_url |string |Todos os pedidos de heartbeat (renovação) para esta licença é direcionado para o URL especificado. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Quantos segundos após license_start_time antes de renovação em primeiro lugar é tentada. Este campo é utilizado apenas se can_renew for verdadeira. A predefinição é 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre pedidos de renovação de licença subsequentes, em caso de falha. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |A janela de tempo no qual a reprodução pode continuar enquanto renovação é tentada, mas sem êxito devido a problemas de back-end com o servidor de licenças. Um valor de 0 indica que não existe nenhum limite para a duração. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides&#46;renew_with_usage |Booleano, VERDADEIRO ou FALSO |Indica que a licença é enviada para a renovação quando inicia de utilização. Este campo é utilizado apenas se can_renew for verdadeira. |

## <a name="session-initialization"></a>Inicialização de sessão
| Name | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em Base64 |Este token de sessão é passado de volta na licença e se existe na renovações subseqüentes. O token de sessão não persiste além sessões. |
| provider_client_token |Cadeia de caracteres codificada em Base64 |Token de cliente para enviar novamente na resposta da licença. Se o pedido de licença contém um token de cliente, este valor é ignorado. O token de cliente persiste para além de sessões de licença. |
| override_provider_client_token |Booleano, VERDADEIRO ou FALSO |Se false e o pedido de licença contém um token de cliente, utilize o token do pedido, mesmo se um token de cliente foi especificado nesta estrutura. Se for VERDADEIRO, utilize sempre o token especificado nesta estrutura. |

## <a name="configure-your-widevine-license-with-net"></a>Configurar a sua licença do Widevine com .NET 

Os Media Services fornecem uma classe que lhe permite configurar uma licença do Widevine. Para construir a licença, de JSON para passar [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Para configurar o modelo, pode:

### <a name="directly-construct-a-json-string"></a>Diretamente construir uma cadeia de caracteres do JSON

Esse método pode ser suscetível a erros. É recomendado que utilize outro método, descrito em [definir necessário classes e serializar para JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> Definir as classes necessárias e serializar para JSON

#### <a name="define-classes"></a>Definir as classes

O exemplo seguinte mostra um exemplo de definições de classes que mapeiam para o esquema JSON do Widevine. Pode instanciar as classes antes à sua serialização para a cadeia de caracteres do JSON.  

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

Utilizar classes definidas na secção anterior para criar o JSON que é utilizado para configurar [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

## <a name="next-steps"></a>Passos Seguintes

Verificar como [proteger com o DRM](protect-with-drm.md)
