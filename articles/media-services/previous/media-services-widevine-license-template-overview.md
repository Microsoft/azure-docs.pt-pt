---
title: Descrição geral do modelo de licença Widevine | Documentos da Microsoft
description: Este tópico apresenta uma visão geral de um modelo de licença do Widevine que é utilizado para configurar as licenças do Widevine.
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
ms.openlocfilehash: d0bb72361e1bff3615f6785ac4c91a10ea773498
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312976"
---
# <a name="widevine-license-template-overview"></a>Descrição geral do modelo de licença do Widevine 
Pode utilizar os serviços de multimédia do Azure para configurar e solicitam licenças do Widevine da Google. Quando o jogador tenta reproduzir os conteúdos protegidos pelo Widevine, é enviado um pedido para o serviço de entrega de licença para obter uma licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. Ele é enviado para o cliente e é utilizado para desencriptar e reproduzir o conteúdo especificado.

Um pedido de licença do Widevine é formatado como uma mensagem JSON.  

>[!NOTE]
> Pode criar apenas uma mensagem vazio com nenhum valor, "{}." Em seguida, um modelo de licença é criado com as predefinições. A predefinição trabalha para a maioria dos casos. Cenários de entrega de licença baseado em Microsoft devem sempre utilizar as predefinições. Se tiver de definir os valores de "content_id" e "fornecedor", um fornecedor tem de corresponder ao Widevine credenciais.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Mensagem JSON
| Name | Value | Descrição |
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
Se existir uma política preexistente, é necessário especificar qualquer um dos valores na especificação de chave do conteúdo. A política preexistente associada a este conteúdo é utilizada para determinar a proteção de saída, como a proteção de conteúdo Digital da largura de banda alta (HDCP) e o sistema de cópia gestão geral (CGMS). Se uma política preexistente não estiver registrada com o servidor de licenças do Widevine, o fornecedor de conteúdos pode injetar os valores para o pedido de licença.   

Cada valor de content_key_specs tem de ser especificado para todas as faixas, independentemente da opção de use_policy_overrides_exclusively. 

| Name | Value | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de controle. Se content_key_specs é especificado no pedido de licença, certifique-se de especificar que todos os tipos de controlar explicitamente. Falha ao fazer isso resulta em falha para reproduzir após 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos da robustez do cliente para a reprodução. <br/> -É necessária a criptografia de caixa branca baseada em software. <br/> -Criptografia de software e um Decodificador oculto são necessários. <br/> -As operações de criptografia e material de chave devem ser executadas dentro de um ambiente de execução de confiança de hardware de segurança. <br/> -A criptografia e a decodificação de conteúdo tem de ser efetuadas dentro de um ambiente de execução de confiança de hardware de segurança.  <br/> -A criptografia, decodificar e manipulação de todos os de suporte de dados (comprimidos e descomprimidos) deve ser tratados dentro de um ambiente de execução de confiança de hardware de segurança. |
| content_key_specs <br/> required_output_protection.hdc |cadeia de caracteres, um dos HDCP_V2 HDCP_NONE, HDCP_V1, |Indica se a HDCP é necessária. |
| content_key_specs <br/>key |Base64-<br/>cadeia de caracteres codificada |Chave de conteúdo a utilizar para este Roteiro. Se for especificado, é necessário o track_type ou key_id. O fornecedor de conteúdos pode utilizar esta opção para inserir a chave de conteúdo para este Roteiro em vez de permitir que o servidor de licenças do Widevine gerar ou pesquisar uma chave. |
| content_key_specs.key_id |Binário de cadeia de caracteres codificada em Base64, 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de diretiva
| Name | Value | Descrição |
| --- | --- | --- |
| policy_overrides. can_play |Booleano, VERDADEIRO ou FALSO |Indica que a reprodução do conteúdo é permitida. A predefinição é falso. |
| policy_overrides. can_persist |Booleano, VERDADEIRO ou FALSO |Indica que a licença pode ser persistentes para o armazenamento não volátil para utilização offline. A predefinição é falso. |
| policy_overrides. can_renew |Booleano, VERDADEIRO ou FALSO |Indica que a renovação desta licença é permitida. Se for VERDADEIRO, a duração da licença pode ser estendida por heartbeat. A predefinição é falso. |
| policy_overrides. license_duration_seconds |int64 |Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides. rental_duration_seconds |int64 |Indica a janela de tempo enquanto reprodução é permitida. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides. playback_duration_seconds |int64 |A janela de visualização de tempo depois de reprodução começar dentro da duração de licença. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. |
| policy_overrides. renewal_server_url |string |Todos os pedidos de heartbeat (renovação) para esta licença são direcionados para o URL especificado. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides. renewal_delay_seconds |int64 |Quantos segundos após license_start_time antes de renovação em primeiro lugar é tentada. Este campo é utilizado apenas se can_renew for verdadeira. A predefinição é 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre pedidos de renovação de licença subsequentes, em caso de falha. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |A janela de tempo no qual a reprodução pode continuar enquanto renovação é tentada, mas sem êxito devido a problemas de back-end com o servidor de licenças. Um valor de 0 indica que não existe nenhum limite para a duração. Este campo é utilizado apenas se can_renew for verdadeira. |
| policy_overrides. renew_with_usage |Booleano, VERDADEIRO ou FALSO |Indica que a licença é enviada para a renovação quando inicia de utilização. Este campo é utilizado apenas se can_renew for verdadeira. |

## <a name="session-initialization"></a>Inicialização de sessão
| Name | Value | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em Base64 |Este token de sessão é passado de volta na licença e se existe na renovações subseqüentes. O token de sessão não persiste além sessões. |
| provider_client_token |Cadeia de caracteres codificada em Base64 |Token de cliente para enviar novamente na resposta da licença. Se o pedido de licença contém um token de cliente, este valor é ignorado. O token de cliente persiste para além de sessões de licença. |
| override_provider_client_token |Booleano, VERDADEIRO ou FALSO |Se false e o pedido de licença contém um token de cliente, utilize o token do pedido, mesmo se um token de cliente foi especificado nesta estrutura. Se for VERDADEIRO, utilize sempre o token especificado nesta estrutura. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Configurar as suas licenças do Widevine ao utilizar tipos .NET
Os Media Services fornecem APIs do .NET que pode utilizar para configurar as suas licenças do Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes, conforme definido no SDK de .NET de serviços de multimédia
As classes seguintes são as definições desses tipos:

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
O exemplo seguinte mostra como utilizar as APIs do .NET para configurar uma licença do Widevine simple:

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

## <a name="see-also"></a>Consulte também
[Utilizar a encriptação comum dinâmica PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)

