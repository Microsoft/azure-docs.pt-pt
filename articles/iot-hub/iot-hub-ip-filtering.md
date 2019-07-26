---
title: Filtros de conexão IP do Hub IoT do Azure | Microsoft Docs
description: Como usar a filtragem de IP para bloquear conexões de endereços IP específicos para o Hub IoT do Azure. Você pode bloquear conexões de endereços IP individuais ou intervalos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414358"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspecto importante de qualquer solução de IoT baseada no Hub IoT do Azure. Às vezes, você precisa especificar explicitamente os endereços IP dos quais os dispositivos podem se conectar como parte da sua configuração de segurança. O recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar o tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Há dois casos de uso específicos em que é útil bloquear os pontos de extremidade do Hub IoT para determinados endereços IP:

* O Hub IoT deve receber tráfego somente de um intervalo especificado de endereços IP e rejeitar todo o resto. Por exemplo, você está usando o Hub IoT com o [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um hub IOT e sua infraestrutura local.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do Hub IoT.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível de serviço do Hub IoT. Portanto, as regras de filtro IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end usando qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que corresponda a uma regra de IP rejeitada no Hub IoT recebe um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra de IP.

## <a name="default-setting"></a>Definição predefinida

Por padrão, a grade de **filtro IP** no portal para um hub IOT está vazia. Essa configuração padrão significa que o Hub aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Configurações de filtro IP padrão do Hub IoT](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro IP, selecione **+ Adicionar regra de filtro IP**.

![Adicionar uma regra de filtro IP a um hub IoT](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar **Adicionar regra de filtro IP**, preencha os campos.

![Depois de selecionar Adicionar uma regra de filtro IP](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra de filtro IP. Deve ser uma cadeia de caracteres alfanumérica, não diferencia maiúsculas de minúsculas, de até 128 caracteres de comprimento. Somente os caracteres alfanuméricos ASCII de 7 bits `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` mais são aceitos.

* Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 para 192.168.103.255.

* Selecione **permitir** ou **Bloquear** como a **ação** para a regra de filtro IP.

Depois de preencher os campos, selecione **salvar** para salvar a regra. Você verá um alerta notificando que a atualização está em andamento.

![Notificação sobre como salvar uma regra de filtro IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desabilitada quando você atinge o máximo de 10 regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **salvar** para salvar a edição.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros serviços do Azure (como Azure Stream Analytics, máquinas virtuais do Azure ou o Device Explorer no Portal) interajam com o Hub IoT.

> [!WARNING]
> Se você usar Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com a filtragem de IP habilitada, use o nome compatível com o Hub de eventos e o ponto de extremidade do Hub IoT na cadeia de conexão do ASA.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro IP, selecione o ícone de lixeira na linha e, em seguida, selecione **salvar**. A regra é removida e a alteração é salva.

![Excluir uma regra de filtro IP do Hub IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperar e atualizar filtros IP usando CLI do Azure

Os filtros IP do Hub IoT podem ser recuperados e atualizados por meio de [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Para recuperar os filtros IP atuais do Hub IoT, execute:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isso retornará um objeto JSON em que os filtros IP existentes são listados na `properties.ipFilterRules` chave:

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

Para adicionar um novo filtro IP para o Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Para remover um filtro IP existente em seu hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observe que `<ipFilterIndexToRemove>` o deve corresponder à ordenação de filtros IP em seu `properties.ipFilterRules`Hub IOT.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperar e atualizar filtros IP usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros IP do Hub IoT podem ser recuperados e definidos por meio de [Azure PowerShell](/powershell/azure/overview).

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

Você também pode recuperar e modificar o filtro IP do Hub IoT usando o ponto de extremidade REST do provedor de recursos do Azure. Consulte `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas em ordem e a primeira regra que corresponde ao endereço IP determina a ação aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar tudo o mais, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A seguinte regra deve rejeitar a todos os endereços mediante a utilização do intervalo 0.0.0.0/0.

Você pode alterar a ordem das regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar sua nova ordem de regra de filtro IP, clique em **salvar**.

![Alterar a ordem de suas regras de filtro IP do Hub IoT](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passos Seguintes

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Monitorização de operações](iot-hub-operations-monitoring.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
