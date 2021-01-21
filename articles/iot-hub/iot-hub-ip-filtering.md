---
title: Filtros IP do Hub Azure IoT | Microsoft Docs
description: Como utilizar a filtragem IP para permitir ligações de endereços IP específicos para o seu hub Azure IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: c6544e8ac00744602476207a89567aea5afe5b1d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632416"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT baseada no Azure IoT Hub. Por vezes, como parte da sua configuração de segurança, tem de especificar explicitamente os endereços IP a partir dos quais os dispositivos se podem ligar. A função *de filtro IP* permite-lhe configurar regras para rejeitar ou aceitar tráfego a partir de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Utilize o filtro IP para receber tráfego apenas a partir de uma gama especificada de endereços IP e rejeite tudo o resto. Por exemplo, está a usar o seu hub IoT com [a Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar ligações privadas entre um hub IoT e a sua infraestrutura no local.

## <a name="default-setting"></a>Definição predefinida

Para aceder à página de definições do Filtro IP, **selecione Networking**, **Acesso público,** em seguida, escolha **Gamas IP Selecionadas**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Definições de filtro IP padrão IoT Hub":::

Por predefinição, a grelha **de filtro IP** no portal para um hub IoT está vazia. Esta definição predefinida significa que o seu hub bloqueia ligações de todos os endereços IP. Esta definição predefinida é equivalente a uma regra que bloqueia o `0.0.0.0/0` intervalo de endereços IP.

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro de IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar Regra de Filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Adicione uma regra de filtro IP a um hub IoT":::

Depois de selecionar **Adicionar Regra de Filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Depois de selecionar Adicionar uma regra de Filtro de IP":::

* Indique um **nome** para a regra de Filtro de IP. Este nome deve ser uma corda alfanumérica única, insensível até 128 caracteres. Só são aceites os carateres alfanuméricos ASCII 7-bit mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Indique um endereço IPv4 individual ou um bloco de endereços IP em notação CIDR. Por exemplo, em notação CIDR, 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 a 192.168.103.255.

Depois de preencher os campos, selecione **Guardar** para guardar a regra. Pode ver um alerta que o notifica que a atualização está em curso.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notificação relativa a guardar uma regra de filtro de IP":::

Quando alcança o máximo de dez regras de filtros de IP, a opção **Adicionar** é desativada.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e selecione **Guardar** para guardar a edição.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro de IP

Para eliminar uma regra de filtro de IP, selecione o ícone de caixote do lixo nessa linha e selecione **Guardar**. A regra é removida e a alteração guardada.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Eliminar uma regra de filtro IP do Hub IoT":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Aplicar regras de filtro IP no ponto final compatível com o Event Hub incorporado

Para aplicar as regras do filtro IP no ponto final compatível com o Event Hub incorporado, verifique a caixa ao lado dos **filtros IP da Aplicação do ponto final incorporado?**

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Imagem mostrando o toggle para o ponto final incorporado e salvar":::

> [!NOTE]
> Esta opção não está disponível para hubs IoT gratuitos (F1). Para aplicar as regras do filtro IP no ponto final incorporado, utilize um hub IoT pago.

Ao ativar esta opção, as suas regras de filtro IP são replicadas no ponto final incorporado, pelo que apenas as gamas IP fidedignas podem aceder a ela.

Se desativar esta opção, o ponto final incorporado está acessível a todos os endereços IP. Este comportamento pode ser útil se quiser ler a partir do ponto final com serviços com endereços IP alterando como a Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras do filtro IP são aplicadas ao nível de serviço IoT Hub. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações de back-end utilizando qualquer protocolo suportado. Além disso, pode escolher se o [ponto final compatível com](iot-hub-devguide-messages-read-builtin.md) o Event Hub incorporado (não através da cadeia de ligação IoT Hub) está ligado a estas regras. 

Qualquer tentativa de ligação de um endereço IP que não seja explicitamente permitida recebe um código de estado 401 não autorizado e descrição. A mensagem da resposta não menciona a regra de IP. Rejeitar endereços IP pode impedir que outros serviços Azure Stream, como a Azure Stream Analytics, Azure Virtual Machines ou o portal Device Explorer in Azure interajam com o hub IoT.

> [!NOTE]
> Se tiver de utilizar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com filtro IP ativado, **desative** os **filtros IP da** opção de ponto final incorporado e, em seguida, utilize o nome e ponto final compatíveis com o hub do seu hub IoT para adicionar manualmente uma entrada de [fluxo de fluxo de Eventos hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) na ASA.

### <a name="ordering"></a>Ordenação

As regras do filtro IP são *permitir* regras e aplicadas sem encomendar. Apenas endereços IP que adicionar são permitidos ligar ao IoT Hub. 

Por exemplo, se quiser aceitar endereços na gama e rejeitar tudo o `192.168.100.0/22` resto, só precisa de adicionar uma regra na grelha com intervalo de `192.168.100.0/22` endereços .

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recupere e atualize filtros IP usando O Azure CLI

Os filtros IP do seu IoT Hub podem ser recuperados e atualizados através do [Azure CLI](/cli/azure/).

Para recuperar os filtros IP atuais do seu Hub IoT, corra:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isto irá devolver um objeto JSON onde os filtros IP existentes estão listados na `properties.networkRuleSets` chave:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Para adicionar um novo filtro IP para o seu IoT Hub, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Para remover um filtro IP existente no seu Hub IoT, corra:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Aqui, `<ipFilterIndexToRemove>` deve corresponder à encomenda de filtros IP no seu IoT Hub's `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recupere e atualize filtros IP usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros IP do seu IoT Hub podem ser recuperados e definidos através do [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro IP usando REST


Também pode recuperar e modificar o filtro IP do seu Hub IoT utilizando o ponto final REST do Fornecedor de Recursos Azure. Veja `properties.networkRuleSets` no [método createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Aposentadoria do filtro IP (clássico)

O filtro IP clássico foi retirado. Para saber mais, consulte o [filtro IP clássico do IoT Hub e como fazer o upgrade.](iot-hub-ip-filter-classic.md)

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Métricas IoT Hub](iot-hub-metrics.md)
* [Suporte ioT Hub para redes virtuais com Ligação Privada e Identidade Gerida](virtual-network-support.md)
* [Gerir o acesso à rede pública para o seu hub IoT](iot-hub-public-network-access.md)
* [Monitorizar o Hub IoT](monitor-iot-hub.md)
