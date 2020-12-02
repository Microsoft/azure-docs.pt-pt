---
title: Azure API Management soft-delete (pré-visualização) Microsoft Docs
description: O soft-delete permite-lhe recuperar instâncias de Gestão de API eliminadas.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: fca98414a87f3b8a4f3c0969a28ee95c7ed47dc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501577"
---
# <a name="api-management-soft-delete-preview"></a>API Management soft-delete (pré-visualização)

Com a API Management soft-delete (pré-visualização), pode recuperar e restaurar casos de Gestão de API (APIM) recentemente eliminados.

> [!IMPORTANT]
> Apenas as instâncias de Gestão da API eliminadas utilizando `2020-01-01-preview` e versões API posteriores serão eliminadas e recuperáveis usando os passos descritos neste artigo. As instâncias APIM eliminadas utilizando versões API anteriores continuarão a ser duramente eliminadas. A Azure PowerShell e Azure CLI não utilizam atualmente a `2020-06-01-preview` versão e também resultarão em comportamentos de eliminação dura.

## <a name="supporting-interfaces"></a>Interfaces de apoio

A funcionalidade de eliminação suave está disponível através da [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Consulte a [Azure REST API Reference](/rest/api/azure/) para obter dicas e ferramentas para chamar APIs de REPOUSO Azure.

| Operação | Descrição | Espaço de nome de gestão API | Versão API mínima |
|--|--|--|--|
| [Criar ou Atualizar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Cria ou atualiza um serviço de Gestão API.  | Serviço de Gestão da API | Qualquer |
| [Criar ou Atualizar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) com `restore` conjunto de propriedade para **verdadeiro** | Undeletes Serviço de Gestão API se anteriormente foi apagado. Se `restore` for especificado e definido para todas as `true` outras propriedades será ignorado.  | Serviço de Gestão da API |  Antevisão de 2020-06-01 |
| [Eliminar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Elimina um serviço de Gestão de API existente. | Serviço de Gestão da API | Antevisão 2020-01-01|
| [Obter pelo nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Obtenha o Serviço de Gestão Api com o nome de Api. | Serviços Eliminados | Antevisão de 2020-06-01 |
| [Lista por subscrição](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Lista todos os serviços de soft-deles disponíveis para não ser entregue para a subscrição dada. | Serviços Eliminados | Antevisão de 2020-06-01
| [Remover](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Purga o Serviço de Gestão da API (elimina-o sem opção de desdelete). | Serviços Eliminados | Antevisão de 2020-06-01

## <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

Pode utilizar qualquer versão API para criar a sua instância de Gestão API, no entanto terá de utilizar `2020-01-01-preview` ou versões posteriores para eliminar suavemente a sua instância APIM (e ter a opção de a recuperar).

Ao eliminar uma instância de Gestão da API, o serviço existirá num estado apagado, tornando-o inacessível a quaisquer operações da APIM. Enquanto neste estado, a instância APIM só pode ser listada, recuperada ou purgada (permanentemente eliminada).

Ao mesmo tempo, o Azure irá agendar a supressão dos dados subjacentes correspondentes à instância APIM para execução após o intervalo de retenção pré-determinado (48 horas). O registo DNS correspondente ao caso também é mantido durante o intervalo de retenção. Não é possível reutilizar o nome de uma instância de Gestão da API que tenha sido apagada suavemente até que o período de retenção tenha passado.

Se a sua instância APIM não for recuperada dentro de 48 horas, será duramente eliminada (irrecuperável). Também pode optar por [purgar](#purge-a-soft-deleted-apim-instance) (eliminar permanentemente) o seu exemplo APIM, renunciando ao período de retenção de eliminação suave.

## <a name="list-deleted-apim-instances"></a>Lista de casos APIM eliminados

Pode verificar se uma instância APIM de apagamento suave está disponível para restaurar (undelete) usando as operações [de Obter por Nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) ou Lista por [Subscrição.](/deletedservices/listbysubscription)

### <a name="get-a-soft-deleted-instance-by-name"></a>Obtenha um exemplo de apagação suave pelo nome

Utilize a operação API Management [Get By Name,](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) substituindo, `{subscriptionId}` e com a sua `{location}` `{serviceName}` subscrição Azure, localização de recursos e nome de instância de gestão da API:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Se estiver disponível para não serdelete, a Azure devolverá um registo do caso APIM mostrando o seu `deletionDate` `scheduledPurgeDate` e, por exemplo:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Listar todos os casos de apagação suave para uma determinada subscrição

Utilize a Lista de Gestão da API [por operação por subscrição,](/deletedservices/listbysubscription) substituindo-a `{subscriptionId}` pelo seu ID de subscrição:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Isto devolverá uma lista de todos os serviços de soft-deles disponíveis para não-delete ao abrigo da subscrição dada, mostrando o `deletionDate` e `scheduledPurgeDate` para cada um.

## <a name="recover-a-deleted-apim-instance"></a>Recuperar uma instância APIM eliminada

Utilize a operação API Management [Create or Update,](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) substituindo, `{subscriptionId}` e com a sua `{resourceGroup}` `{apimServiceName}` subscrição Azure, nome de grupo de recursos e nome de Gestão API:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . e definir a `restore` propriedade para no corpo de `true` pedido. (Quando esta bandeira for especificada e definida como *verdadeira,* todas as outras propriedades serão ignoradas.) Por exemplo:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Purgue um exemplo APIM apagado suave

Utilize a operação [de purga de gestão](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) da API, substituindo, `{subscriptionId}` , e com a sua `{location}` `{serviceName}` subscrição Azure, localização de recursos e nome de Gestão API:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Isto eliminará permanentemente a sua instância de Gestão API da Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre opções de backup e recuperação da API Management a longo prazo:

- [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](api-management-howto-disaster-recovery-backup-restore.md)