---
title: Filtros de ligação IP Azure IoT Hub / Microsoft Docs
description: Como utilizar a filtragem IP para bloquear ligações de endereços IP específicos para o seu hub Azure IoT. Pode bloquear ligações de endereços IP individuais ou de intervalos de endereços.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: a9a95c0151ed6dd3a2ad4a9d548723cdff0bcfb8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147085"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT baseada no Azure IoT Hub. Por vezes, como parte da sua configuração de segurança, tem de especificar explicitamente os endereços IP a partir dos quais os dispositivos se podem ligar. A função *de filtro IP* permite-lhe configurar regras para rejeitar ou aceitar tráfego a partir de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Existem dois casos de utilização específicos quando é útil bloquear os pontos finais do IoT Hub para determinados endereços IP:

* O seu hub IoT só deve receber tráfego a partir de uma gama especificada de endereços IP e rejeitar tudo o resto. Por exemplo, está a usar o seu hub IoT com [a Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) para criar ligações privadas entre um hub IoT e a sua infraestrutura no local.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do hub IoT.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras do filtro IP são aplicadas ao nível de serviço IoT Hub. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações de back-end utilizando qualquer protocolo suportado. No entanto, os clientes que lêem diretamente a partir do [ponto final compatível com](iot-hub-devguide-messages-read-builtin.md) o Event Hub (não através da cadeia de ligação IoT Hub) não estão ligados às regras do filtro IP. 

Qualquer tentativa de ligação a partir de um endereço IP que corresponda a uma regra IP rejeitada no seu hub IoT recebe um código de estado 401 não autorizado e descrição. A mensagem da resposta não menciona a regra de IP. Rejeitar endereços IP pode impedir que outros serviços Azure Stream, como a Azure Stream Analytics, Azure Virtual Machines ou o portal Device Explorer in Azure interajam com o hub IoT.

> [!NOTE]
> Se tiver de utilizar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com filtro IP ativado, utilize o nome e o ponto final compatíveis com o hub do seu hub IoT para adicionar manualmente uma entrada de [stream de Centros de Eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) na ASA.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha **de filtro IP** no portal para um hub IoT está vazia. Esta definição predefinida significa que o seu hub aceita ligações a partir de qualquer endereço IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

Para aceder à página de definições do Filtro IP, **selecione Networking**, **Acesso público,** em seguida, escolha **Gamas IP Selecionadas**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Definições de filtro IP padrão IoT Hub":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro de IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar Regra de Filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Definições de filtro IP padrão IoT Hub":::

Depois de selecionar **Adicionar Regra de Filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Definições de filtro IP padrão IoT Hub":::

* Indique um **nome** para a regra de Filtro de IP. O nome tem de ser uma cadeia alfanumérica exclusiva, sensível a maiúsculas e minúsculas com um máximo de 128 carateres. Só são aceites os carateres alfanuméricos ASCII 7-bit mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Indique um endereço IPv4 individual ou um bloco de endereços IP em notação CIDR. Por exemplo, em notação CIDR, 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** da regra de filtro de IP.

Depois de preencher os campos, selecione **Guardar** para guardar a regra. Pode ver um alerta que o notifica que a atualização está em curso.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Definições de filtro IP padrão IoT Hub":::

Quando alcança o máximo de dez regras de filtros de IP, a opção **Adicionar** é desativada.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e selecione **Guardar** para guardar a edição.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro de IP

Para eliminar uma regra de filtro de IP, selecione o ícone de caixote do lixo nessa linha e selecione **Guardar**. A regra é removida e a alteração guardada.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Definições de filtro IP padrão IoT Hub":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recupere e atualize filtros IP usando O Azure CLI

Os filtros IP do seu IoT Hub podem ser recuperados e atualizados através do [Azure CLI](/cli/azure/?view=azure-cli-latest).

Para recuperar os filtros IP atuais do seu Hub IoT, corra:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isto irá devolver um objeto JSON onde os filtros IP existentes estão listados na `properties.ipFilterRules` chave:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Para adicionar um novo filtro IP para o seu IoT Hub, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Para remover um filtro IP existente no seu Hub IoT, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Note que `<ipFilterIndexToRemove>` deve corresponder à encomenda de filtros IP no seu IoT Hub's `properties.ipFilterRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recupere e atualize filtros IP usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros IP do seu IoT Hub podem ser recuperados e definidos através do [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro IP usando REST

Também pode recuperar e modificar o filtro IP do seu Hub IoT utilizando o ponto final REST do Fornecedor de Recursos Azure. Veja `properties.ipFilterRules` no [método createorupdate](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Avaliação das regras de filtros de IP

As regras de filtros de IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se quiser aceitar os endereços no intervalo 192.168.100.0/22 e rejeitar todos os outros, a primeira regra na grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A regra seguinte deve rejeitar todos os endereços mediante a utilização do intervalo 0.0.0.0/0.

Pode alterar a ordem das regras dos filtros de IP na grelha ao clicar nos três pontos verticais no início de uma linha e arrastar e largar.

Para guardar a ordem nova das regras dos filtros de IP, clique em **Guardar**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Definições de filtro IP padrão IoT Hub":::

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Métricas IoT Hub](iot-hub-metrics.md)