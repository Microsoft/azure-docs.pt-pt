---
title: Monitorar logs de diagnóstico dos serviços de mídia via Azure Monitor | Microsoft Docs
description: Este artigo demonstra como rotear e exibir logs de diagnóstico por meio de Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: bf83c87c71c8e05dc74d7754c82c76489de1bd85
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750894"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorar logs de diagnóstico dos serviços de mídia

[Azure monitor](../../azure-monitor/overview.md) permite que você monitore as métricas e os logs de diagnóstico que ajudam a entender como seus aplicativos estão sendo executados. Para obter uma descrição detalhada desse recurso e ver por que você desejaria usar os logs de diagnóstico e as métricas dos serviços de mídia do Azure, consulte [monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

Este artigo mostra como rotear dados para a conta de armazenamento e exibir os dados. 

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services](create-account-cli-how-to.md).
- Revise [monitorar métricas de serviços de mídia e logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Rotear dados para a conta de armazenamento usando o portal

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue até sua conta dos serviços de mídia no e clique em **configurações de diagnóstico** em **Monitor**. Aqui, pode ver uma lista de todos os recursos na sua subscrição que produzem dados de monitorização através do Azure Monitor. 

    ![Secção Definições de diagnóstico](media/media-services-diagnostic-logs/logs01.png)

1. Clique em **Adicionar configuração de diagnóstico**.

   As definições de diagnóstico de recursos são definições *dos* dados de monitorização que devem ser encaminhados a partir de um determinado recurso e para *onde* é que devem ser encaminhados.

1. Na secção que aparece, dê um **nome** à definição e selecione a caixa para **Arquivar numa conta de armazenamento**.

    Selecione a conta de armazenamento para a qual você deseja enviar os logs e pressione **OK**.
1. Selecione todas as caixas em **Registo** e **Métrica**. Dependendo do tipo de recurso, poderá ter apenas uma destas opções. Estas caixas de verificação controlam quais as categorias de dados de registos e de métricas que estão disponíveis para esse tipo de recurso que são enviadas para o destino que selecionou, neste caso, uma conta de armazenamento.

   ![Secção Definições de diagnóstico](media/media-services-diagnostic-logs/logs02.png)
1. Defina o controlo de deslize **Retenção (dias)** para 30. Este controlo de deslize define um número de dias durante os quais os dados de monitorização vão ser retidos na conta de armazenamento. O Azure Monitor elimina automaticamente os dados mais antigos em comparação com o número de dias especificado. A retenção de zero dias armazena os dados indefinidamente.
1. Clique em **Guardar**.

Os dados de monitorização do seu recurso estão agora a ser encaminhados para a conta de armazenamento.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>Rotear dados para a conta de armazenamento usando a CLI

Para habilitar o armazenamento de logs de diagnóstico em uma conta de armazenamento, você executaria o seguinte comando da CLI `az monitor diagnostic-settings`: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Por exemplo:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Exibir dados na conta de armazenamento usando o portal

Se tiver seguido os passos anteriores, os dados começaram a fluir para a sua conta de armazenamento.

Poderá ter de aguardar até cinco minutos para que o evento apareça na conta de armazenamento.

1. No portal, navegue para a secção **Contas de Armazenamento**, que estão disponíveis na barra de navegação esquerda.
1. Identifique a conta de armazenamento que criou na secção anterior e clique na mesma.
1. Clique em **BLOBs**e, em seguida, no contêiner rotulado como **insights-logs-keydeliveryrequests**. Esse é o contêiner que tem seus logs. Os dados de monitoramento são divididos em contêineres por ID de recurso e, em seguida, por data e hora.
1. Clique nos contentores de ID de recurso, data e hora para navegar para o ficheiro PT1H.json. Clique no ficheiro PT1H.json e clique em **Transferir**.

 Pode ver agora o evento JSON que foi armazenado na conta de armazenamento.

### <a name="examples-of-pt1hjson"></a>Exemplos de PT1H. JSON

#### <a name="clear-key-delivery-log"></a>Limpar o log de distribuição de chaves

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Log de distribuição de chaves criptografadas Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="see-also"></a>Ver também

* [Métricas de Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor logs de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md)
* [Como coletar e consumir dados de log de seus recursos do Azure](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Passos seguintes

[Monitorar métricas](media-services-metrics-howto.md)
