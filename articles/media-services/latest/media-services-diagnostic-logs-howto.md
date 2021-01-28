---
title: Monitorize registos de diagnóstico via Azure Monitor
description: Este artigo demonstra como encaminhar e visualizar registos de diagnóstico através do Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d9252a5fdf7b1c68c1bb64bd22ede8640184a0c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953959"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitor Media Services registos de diagnóstico

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Para uma descrição detalhada desta funcionalidade e para ver por que razão pretende utilizar as métricas e registos de diagnóstico do Azure Media Services, consulte [as métricas dos Serviços de Mídia do Monitor e os registos de diagnóstico](media-services-metrics-diagnostic-logs.md).

Este artigo mostra-lhe como encaminhar os dados para a conta de armazenamento e, em seguida, ver os dados.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.
- Rever  [métricas de Serviços de Mídia monitor e registos de diagnóstico](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Encaminhar os dados para a conta de armazenamento utilizando o portal

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue na sua conta de Serviços de Mídia e clique em **Definições de Diagnóstico** no **Monitor**. Aqui, pode ver uma lista de todos os recursos na sua subscrição que produzem dados de monitorização através do Azure Monitor.

    ![Screenshot que realça as definições de Diagnóstico na secção de Monitorização.](media/media-services-diagnostic-logs/logs01.png)

1. Clique **na definição de diagnóstico de adicionar**.

   As definições de diagnóstico de recursos são definições *dos* dados de monitorização que devem ser encaminhados a partir de um determinado recurso e para *onde* é que devem ser encaminhados.

1. Na secção que aparece, dê um **nome** à definição e selecione a caixa para **Arquivar numa conta de armazenamento**.

    Selecione a conta de armazenamento para a qual deseja enviar registos e prima **OK**.
1. Selecione todas as caixas em **Registo** e **Métrica**. Dependendo do tipo de recurso, poderá ter apenas uma destas opções. Estas caixas de verificação controlam quais as categorias de dados de registos e de métricas que estão disponíveis para esse tipo de recurso que são enviadas para o destino que selecionou, neste caso, uma conta de armazenamento.

   ![Secção Definições de diagnóstico](media/media-services-diagnostic-logs/logs02.png)
1. Defina o controlo de deslize **Retenção (dias)** para 30. Este controlo de deslize define um número de dias durante os quais os dados de monitorização vão ser retidos na conta de armazenamento. O Azure Monitor elimina automaticamente os dados mais antigos em comparação com o número de dias especificado. A retenção de zero dias armazena os dados indefinidamente.
1. Clique em **Guardar**.

Os dados de monitorização do seu recurso estão agora a ser encaminhados para a conta de armazenamento.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Encaminhar os dados para a conta de armazenamento utilizando o Azure CLI

Para permitir o armazenamento de registos de diagnóstico numa Conta de Armazenamento, executaria o seguinte `az monitor diagnostic-settings` comando Azure CLI:

```azurecli-interactive
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

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Ver dados na conta de armazenamento usando o portal

Se tiver seguido os passos anteriores, os dados começaram a fluir para a sua conta de armazenamento.

Poderá ter de aguardar até cinco minutos para que o evento apareça na conta de armazenamento.

1. No portal, navegue para a secção **Contas de Armazenamento**, que estão disponíveis na barra de navegação esquerda.
1. Identifique a conta de armazenamento que criou na secção anterior e clique na mesma.
1. Clique em **Blobs,** em seguida, no recipiente marcado **insights-logs-keydeliveryrequests**. Este é o contentor que tem os seus troncos. Os dados de monitorização são divididos em contentores por identificação de recursos, em seguida, por data e hora.
1. Clique nos contentores de ID de recurso, data e hora para navegar para o ficheiro PT1H.json. Clique no ficheiro PT1H.json e clique em **Transferir**.

 Pode ver agora o evento JSON que foi armazenado na conta de armazenamento.

### <a name="examples-of-pt1hjson"></a>Exemplos de PT1H.jsem

#### <a name="clear-key-delivery-log"></a>Registo de entrega de chave clara

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

#### <a name="widevine-encrypted-key-delivery-log"></a>Registo de entrega de chave encriptado widevine

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

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="see-also"></a>Ver também

* [Métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Registos de diagnóstico do monitor Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Como recolher e consumir dados de registo dos seus recursos Azure](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Passos seguintes

[Monitorizar métricas](media-services-metrics-howto.md)
