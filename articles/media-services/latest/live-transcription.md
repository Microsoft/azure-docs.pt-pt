---
título: Transcrição ao vivo : Descrição do Azure Media Services: Saiba mais sobre a transcrição em direto dos Azure Media Services.  
serviços: media-services documentationcenter: 'author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: ne ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel

---

# <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Service fornece vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço entrega o texto transcrito em TTML compatível com IMSC1.1. A entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços.

Aplicam-se taxas adicionais quando a transcrição ao vivo é ligada. Por favor, reveja as informações sobre os preços na secção vídeo ao vivo da [página de preços dos Serviços de Comunicação](https://azure.microsoft.com/pricing/details/media-services/)Social .

Este artigo descreve como permitir a transcrição ao vivo ao transmitir um Evento Ao Vivo com a Azure Media Services. Antes de continuar, certifique-se de que está familiarizado com a utilização de Media Services v3 REST APIs (consulte [este tutorial](stream-files-tutorial-with-rest.md) para mais detalhes). Você também deve estar familiarizado com o conceito [de streaming ao vivo.](live-streaming-overview.md) Recomenda-se completar o Stream ao vivo com o tutorial [dos Media Services.](stream-live-tutorial-with-api.md)

## <a name="live-transcription-preview-regions-and-languages"></a>Regiões e línguas de pré-visualização da transcrição ao vivo

A transcrição ao vivo está disponível nas seguintes regiões:

- Sudeste Asiático
- Europa Ocidental
- Europa do Norte
- E.U.A. Leste
- E.U.A. Central
- E.U.A. Centro-Sul
- E.U.A. Oeste 2
- Sul do Brasil

Esta é a lista de idiomas disponíveis que podem ser transcritas, use o código de idioma na API.

| Linguagem | Código do idioma |
| -------- | ------------- |
| Catalão  | ca-ES |
| Dinamarquês (Dinamarca) | da-DK |
| Alemão (Alemanha) | de-DE |
| Inglês (Austrália) | en-AU |
| Inglês (Canadá) | en-CA |
| Inglês (Reino Unido) | en-GB |
| Inglês (Índia) | en-IN |
| Inglês (Nova Zelândia) | en-NZ |
| Inglês (Estados Unidos) | en-PT |
| Espanhol (Espanha) | es-ES |
| Espanhol (México) | es-MX |
| Finlandês (Finlândia) | fi-FI |
| Francês (Canadá) | fr-CA |
| Francês (França) | fr-FR |
| Italiano (Itália) | it-IT |
| Neerlandês (Países Baixos) | nl-NL |
| Português (Brasil) | pt-BR |
| Português (Portugal) | pt-PT |
| Sueco (Suécia) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Criar o evento ao vivo com transcrição ao vivo

Para criar um evento ao vivo com a transcrição ligada, envie a operação PUT com a versão API de pré-visualização 2019-05-01, por exemplo:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (onde é criado um Evento Ao Vivo com RTMP como protocolo de ingestão). Note a adição de uma propriedade de transcrições.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Iniciar ou parar a transcrição após o evento ao vivo ter começado

Você pode começar e parar a transcrição ao vivo enquanto o evento ao vivo está em funcionamento. Para obter mais informações sobre o início e a paragem de eventos ao vivo, leia a secção de operações de longa duração em [Develop with Media Services v3 APIs](media-services-apis-overview.md#long-running-operations).

Para ligar transcrições ao vivo ou atualizar o idioma de transcrição, remendo o evento ao vivo para incluir uma propriedade de "transcrições". Para desligar as transcrições ao vivo, remova a propriedade "transcrições" do objeto do evento ao vivo.  

> [!NOTE]
> Ligar ou desligar a transcrição **mais de uma vez** durante o evento ao vivo não é um cenário apoiado.

Esta é a chamada para ligar transcrições ao vivo.

PATCH: ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Entrega e reprodução de transcrição

Reveja o artigo de [visão geral](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) da embalagem Dinâmica de como o nosso serviço utiliza embalagens dinâmicas para entregar vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço entrega o texto transcrito em TTML compatível com IMSC1.1. Esta entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços. Pode utilizar um web player como o [Azure Media Player](use-azure-media-player.md) para reproduzir o stream.  

> [!NOTE]
> Se utilizar o Azure Media Player, utilize a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos

Para pré-visualização, são conhecidos os seguintes problemas com transcrição ao vivo:

- As aplicações precisam de utilizar as APIs de pré-visualização, descritas na [Especificação Do OpenAPI dos Serviços de Mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- A proteção da gestão de direitos digitais (DRM) não se aplica à faixa de texto, apenas a encriptação do envelope AES é possível.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos Serviços de Mídia](media-services-overview.md)
