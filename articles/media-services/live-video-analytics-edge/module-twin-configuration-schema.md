---
title: Módulo twin JSON schema - Azure
description: Este tópico descreve o esquema do JSON twin do vídeo ao vivo no IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87053072"
---
# <a name="module-twin-json-schema"></a>Módulo twin JSON schema

Os gémeos do dispositivo são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo duplo para cada dispositivo que ligar ao serviço. Para obter uma explicação detalhada, consulte [Compreender e utilizar gémeos módulos no IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md)

Este tópico descreve o esquema do JSON twin do vídeo ao vivo no IoT Edge.

> [!NOTE]
> Para obter autorização para aceder aos recursos dos Serviços de Multimédia e à API dos Serviços de Multimédia, primeiro tem de se autenticar. Para mais informações, consulte [Acesso à Azure Media Services API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Propriedades gémeas do módulo

Live Video Analytics on IoT Edge expõe as seguintes propriedades gémeas do módulo. 

|Propriedade |Necessário |Dinâmica |Descrição |
|---|---|---|---|
|aplicaçãoDataDirectório |Sim |Não |Caminho para um volume montado para uma configuração persistente. |
|azureMediaServicesArmId |Sim |Não |Identificador exclusivo de Gestão de Recursos Azure para a Conta de Serviços de Mídia.|
|aadTenantId |Sim |Não |Cliente Azure Ad Inquilina do Inquilino.|
|aadServicePrincipalAppId |Sim |Sim |O cliente criou o Azure AD AppId.|
|aadServicePrincipalCertificate |Sim, é o seu<sup>*</sup>  |Sim |O cliente criou o certificado Azure AD AppId.|
|aadServicePrincipalPassword |Sim, é o seu<sup>*</sup>  |Sim |O cliente criou a senha Azure AD AppId.|
|aadEndpoint |Não |Não |Ponto final Azure AD específico da nuvem. <br/>Predefinição: `https://login.microsoftonline.com` |
|aadResourceId |Não |Não |Audiência/ID de recursos Azure específicos em nuvem <br/>Predefinição: `https://management.core.windows.net/` |
|armEndpoint |Não |Não |Fonte específica de recursos Azure Gerencie o ponto final. <br/>Predefinição: `https://management.azure.com/` |
|diagnósticosLevel |Não |Sim |Verbosidade dos eventos: <br/>Informação &#x02758; Aviso &#x02758; Erro &#x02758; & crítico#x02758; Nenhum. |
|diagnósticoSEventsOutputName |Não |Sim |Saída do hub para eventos de diagnóstico. <br/>(Vazio significa que os diagnósticos não são publicados)|
|operacionalEventsOutputName|Não|Sim|Produção do hub para eventos operacionais.<br/>(Vazio significa que os eventos operacionais não são publicados)
|logLevel|Não|Sim|Um dos seguintes: <br/>&#x000B7; Verbose<br/>&#x000B7; Informação (Padrão)<br/>&#x000B7; Aviso<br/>&#x000B7; Erro<br/>&#x000B7; Nenhum.|
|logCategorias|Não|Sim|Uma lista separada em vírgula do seguinte: Aplicação, MediaPipeline, Eventos <br/>Padrão: Aplicação, Eventos|
|debugLogsDirectory|Não|Sim|Diretório para registos de depurg. Se os registos presentes forem gerados, se não estiverem desativados os registos de depuração.

<sup>*</sup>Deve fornecer o certificado principal de serviço ou a senha. 

As propriedades dinâmicas podem ser atualizadas sem reiniciar o módulo. Pode obter os valores de vários destes imóveis seguindo as instruções no artigo [da API dos Serviços de Comunicação Social.](../latest/access-api-howto.md) 

Consulte o artigo sobre [Monitorização e registo de registos](monitoring-logging.md) para obter mais informações sobre o papel das definições de diagnóstico opcional.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Passos seguintes

[Métodos diretos](direct-methods.md)
