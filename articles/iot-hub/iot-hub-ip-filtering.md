---
title: Filtros de ligação IP Azure IoT Hub / Microsoft Docs
description: Como utilizar a filtragem IP para bloquear ligações de endereços IP específicos para o seu hub Azure IoT. Pode bloquear ligações a partir de endereços IP individuais ou ip.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 742706f4daa518faf06e5c8b735e679f345f1279
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849885"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT baseada no Hub Azure IoT. Por vezes, é necessário especificar explicitamente os endereços IP a partir dos quais os dispositivos podem ligar-se como parte da sua configuração de segurança. A função de *filtro IP* permite-lhe configurar regras para rejeitar ou aceitar o tráfego a partir de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Existem dois casos de utilização específicos quando é útil bloquear os pontos finais do IoT Hub para determinados endereços IP:

* O seu hub IoT deve receber tráfego apenas a partir de uma gama especificada de endereços IP e rejeitar tudo o resto. Por exemplo, está a usar o seu hub IoT com a [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar ligações privadas entre um hub IoT e a sua infraestrutura no local.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do centro ioT.

## <a name="how-filter-rules-are-applied"></a>Como as regras do filtro são aplicadas

As regras do filtro IP são aplicadas ao nível do serviço IoT Hub. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações back-end utilizando qualquer protocolo suportado. No entanto, os clientes que lêem diretamente a partir do ponto final compatível do [Event Hub incorporado](iot-hub-devguide-messages-read-builtin.md) (não através da cadeia de ligação IoT Hub) não estão ligados às regras do filtro IP. 

Qualquer tentativa de ligação de um endereço IP que corresponda a uma regra de IP rejeitada no seu hub IoT recebe um código e descrição de estado não autorizados 401. A mensagem de resposta não menciona a regra do PI. A rejeição de endereços IP pode impedir que outros serviços Do Azure, como o Azure Stream Analytics, o Azure Virtual Machines, ou o Device Explorer no portal Azure interajam com o hub IoT.

> [!NOTE]
> Se tiver de utilizar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com filtro IP ativado, utilize o nome e o ponto final compatíveis com o evento do seu hub IoT para adicionar manualmente uma entrada de fluxo de Hubs de [Eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) na ASA.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha do **filtro IP** no portal para um hub IoT está vazia. Esta definição predefinida significa que o seu hub aceita ligações a partir de qualquer endereço IP. Esta definição predefinida equivale a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

Para chegar à página de definições do Filtro IP, selecione **Networking**, **Acesso público**e, em seguida, escolha **intervalos IP selecionados:**

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Definições de filtro IP padrão Do IoT Hub":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra do filtro IP, **selecione + Adicionar regra**do filtro IP .

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Adicione uma regra de filtro IP a um hub IoT":::

Depois de selecionar a regra do **filtro IP,** preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Depois de selecionar Adicionar uma regra de filtro IP":::

* Forneça um **nome** para a regra do filtro IP. Esta deve ser uma corda alfanumérica única, insensível e insensível a 128 caracteres. Apenas os caracteres alfanuméricos a7 bits ASCII plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` são aceites.

* Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no ponto CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como **ação** para a regra do filtro IP.

Depois de preencher os campos, selecione **Guardar** para salvar a regra. Vê um alerta a informar-lhe que a atualização está em andamento.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notificação sobre salvar uma regra de filtro IP":::

A opção **Adicionar** é desativada quando atingir o máximo de 10 regras de filtro IP.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e, em seguida, selecione **Guardar** para salvar a sua edição.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro IP

Para eliminar uma regra do filtro IP, selecione o ícone do caixote do lixo nessa linha e, em seguida, selecione **Guardar**. A regra é removida e a mudança é salva.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Eliminar uma regra de filtro IP IoT Hub":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperar e atualizar filtros IP utilizando o Azure CLI

Os filtros IP do seu IoT Hub podem ser recuperados e atualizados através do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Para recuperar os filtros IP atuais do seu Hub IoT, corra:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isto devolverá um objeto JSON onde os filtros IP existentes estão listados na `properties.ipFilterRules` chave:

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

Para adicionar um novo filtro IP para o seu Hub IoT, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Para remover um filtro IP existente no seu Hub IoT, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Note que `<ipFilterIndexToRemove>` deve corresponder à encomenda de filtros IP no seu IoT `properties.ipFilterRules` Hub's .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperar e atualizar filtros IP utilizando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros IP do seu IoT Hub podem ser recuperados e definidos através do [Azure PowerShell](/powershell/azure/overview).

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

## <a name="update-ip-filter-rules-using-rest"></a>Atualizar regras de filtro IP usando REST

Também pode recuperar e modificar o filtro IP do seu IoT Hub utilizando o ponto final DO Fornecedor de Recursos Azure. Consulte `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra do filtro IP

As regras do filtro IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

Por exemplo, se quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar tudo o resto, a primeira regra da grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A regra seguinte deve rejeitar todos os endereços utilizando o intervalo 0.0.0.0/0.

Pode alterar a ordem das suas regras de filtro IP na grelha clicando nos três pontos verticais no início de uma linha e utilizando arrasto e gota.

Para guardar a sua nova ordem de regra do filtro IP, clique em **Guardar**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Altere a ordem das suas regras de filtro IP IoT HUb":::

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Métricas do Hub IoT](iot-hub-metrics.md)
