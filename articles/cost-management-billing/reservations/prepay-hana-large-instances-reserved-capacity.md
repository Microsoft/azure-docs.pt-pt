---
title: Poupe no SAP HANA nas Instâncias Grandes com uma reserva do Azure
description: Compreenda aquilo que tem de saber antes de comprar uma reserva de Instância Grande do HANA e como fazer a compra.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ea1ee2311d8655ce17017c73309bc69e89f4b5f4
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599118"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Poupe no SAP HANA nas Instâncias Grandes com uma reserva do Azure

Pode poupar nos custos do SAP HANA nas Instâncias Grandes (HLI) quando pré-comprar reservas do Azure para um ou três anos. O desconto de reserva é aplicado ao SKU do HLI aprovisionado correspondente à instância reservada comprada. Este artigo ajuda-o a compreender aquilo que tem de saber antes de comprar uma reserva e como fazer a compra.

Ao comprar uma reserva, compromete-se a utilizar o HLI durante um ou três anos. A compra de capacidade reservada do HLI abrange a computação e o armazenamento NFS incluídos no SKU. A reserva não inclui custos de licenciamento de software, como o sistema operativo, SAP ou custos adicionais de armazenamento. O desconto de reserva é aplicado automaticamente ao SAP HLI aprovisionado. Quando o termo da reserva termina, são aplicadas tarifas pay as you go ao recurso aprovisionado.

## <a name="purchase-considerations"></a>Considerações relacionadas com a compra

Um SKU do HLI tem de ser aprovisionado antes de passar pela compra de capacidade reservada. A reserva é paga antecipadamente ou com pagamentos mensais. Aplicam-se as seguintes restrições à capacidade reservada do HLI:

- São aplicados descontos de reserva apenas às subscrições do Contrato Enterprise e do Contrato de Cliente Microsoft. Não são suportadas outras subscrições.
- A flexibilidade de tamanho de instância não é suportada para a capacidade reservada do HLI. Uma reserva aplica-se apenas ao SKU e à região aos quais se destina a compra.
- A troca e o cancelamento self-service não são suportados.
- O âmbito da capacidade reservada é único, pelo que se aplica a uma subscrição individual e a um único grupo de recursos. A capacidade comprada não pode ser atualizada para utilização por outra subscrição.
- Não pode ter um âmbito de reserva partilhado para a capacidade reservada do HANA. Não pode dividir, unir ou atualizar o âmbito de reserva.
- Pode comprar um único HLI de cada vez através das chamadas à API de capacidade reservada. Faça chamadas à API adicionais para comprar mais quantidades.

Pode comprar capacidade reservada no portal do Azure ou através da [API REST](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Comprar uma reserva de Instância Grande do HANA

Utilize as seguintes informações para comprar uma reserva HLI com as [APIs REST de Encomenda de Reserva](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Obter a encomenda de reserva e o preço

Primeiro, obtenha a encomenda de reserva e o preço do SKU de instância grande do HANA aprovisionado com a API [Calcular Preço](/rest/api/reserved-vm-instances/reservationorder/calculate).

O exemplo seguinte utiliza [armclient](https://github.com/projectkudu/ARMClient) para fazer chamadas à API REST com o PowerShell. Eis o aspeto da encomenda de reserva, do pedido da API Calcular Preço e do corpo do pedido:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Para obter mais informações sobre os campos de dados e as respetivas descrições, veja [Campos de reserva do HLI](#hli-reservation-fields).

A resposta de exemplo seguinte assemelha-se ao que é devolvido. Tenha em atenção o valor devolvido para `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Fazer a compra

Faça a compra com o valor `quoteId` devolvido e o valor `reservationOrderId` que obteve da secção [Obter a encomenda de reserva e o preço](#get-the-reservation-order-and-price) anterior.

Segue-se um pedido de exemplo:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Segue-se uma resposta de exemplo. Se a encomenda for feita com êxito, o valor `provisioningState` deverá ser `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Verificar o êxito do estado de compra

Execute o pedido GET da Encomenda de reserva para ver o estado de encomenda da compra. `provisioningState` deverá ser `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

A resposta deve assemelhar-se ao seguinte exemplo.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Campos de reserva do HLI

As informações seguintes explicam o significado de vários campos de reserva.

  **SKU**: nome do SKU do HLI. É semelhante a `SAP_HANA_On_Azure_<SKUname>`.

  **Localização**: as regiões do HLI disponíveis. Veja [SKUs para o SAP HANA no Azure (Instâncias Grandes)](../../virtual-machines/workloads/sap/hana-available-skus.md) para obter as regiões disponíveis. Para obter o formato de cadeia de localização, utilize a [chamada à API para obter localizações](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Tipo de Recurso Reservado** `SapHana`

  **Subscrição**: a subscrição utilizada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição tem de ser um contrato Enterprise (número da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente Microsoft. Os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária), se disponível, ou cobrados como utilização excedida.

  **Âmbito**: o âmbito da reserva deve ser único.

  **Termo**: um ou três anos. É semelhante a `P1Y` ou `P3Y`.

  **Quantidade**: o número de instâncias a comprar para a reserva. A quantidade a comprar é um HLI de cada vez. Para reservas adicionais, repita a chamada à API com os campos correspondentes.

## <a name="troubleshoot-errors"></a>Resolver erros

Pode receber um erro como o exemplo seguinte quando efetuar uma compra de reserva. A causa possível é o HLI não estar aprovisionado para compra. Em caso afirmativo, contacte a sua equipa de conta da Microsoft para obter um HLI aprovisionado antes de tentar fazer uma compra de reserva.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Como chamar as APIs REST do Azure com o Postman e cURL ](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Veja [SKUs para o SAP HANA no Azure (Instâncias Grandes)](../../virtual-machines/workloads/sap/hana-available-skus.md) para obter a lista de SKUs e as regiões disponíveis.