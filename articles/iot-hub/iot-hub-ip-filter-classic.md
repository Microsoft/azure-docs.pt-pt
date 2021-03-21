---
title: Filtro IP clássico do Azure IoT Hub (precotado) | Microsoft Docs
description: Como atualizar a partir do filtro IP clássico e quais são os benefícios
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661159"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>Filtro IP clássico do IoT Hub e como atualizar 

O filtro IP atualizado para o IoT Hub protege o ponto final incorporado e é seguro por defeito. Embora nos esforcemos para nunca fazer alterações de rutura, o modelo de segurança melhorado do filtro IP atualizado é incompatível com o filtro IP clássico, por isso anunciamos a sua aposentadoria. Para saber mais sobre o novo filtro IP atualizado, consulte os novos filtros IP do [hub de IoT e IoT.](iot-hub-ip-filtering.md) [](#whats-new)

Para evitar perturbações de serviço, deve efetuar a atualização guiada antes do prazo de migração, altura em que a atualização será executada automaticamente. Para saber mais sobre a linha temporal da migração, consulte [a atualização do Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Como atualizar

1.  Visite o portal Azure
2.  Navegue até ao seu hub IoT.
3.  Selecione **Networking** a partir do menu do lado esquerdo.
4.  Deverá ver um banner que o leva a atualizar o filtro IP para o novo modelo. Selecione **Sim** para continuar.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Imagem mostrando o pedido de banner para atualizar a partir do filtro IP clássico":::
5.  Uma vez que o novo Filtro IP bloqueia todo o IP por padrão, a atualização remove as suas regras de negação individual, mas dá-lhe a oportunidade de as rever antes de economizar. Reveja cuidadosamente as regras para garantir que funcionam para si.
6.  Siga as instruções para terminar a atualização.

## <a name="whats-new"></a>Novidades

### <a name="secure-by-default"></a>Proteger por defeito

O filtro IP clássico permite implicitamente que todos os endereços IP se conectem ao IoT Hub por padrão, o que não se alinha bem com os cenários de segurança de rede mais comuns. Normalmente, você gostaria que apenas endereços IP confiáveis para ser capaz de conectar ao seu hub IoT e rejeitar tudo o resto. Para atingir este objetivo com o filtro IP clássico, é um processo em várias etapas. Por exemplo, se quiser apenas aceitar o tráfego `192.168.100.0/22` de, deve

1. Configure uma regra *de permitir* uma única regra para `192.168.100.0/22` .
1. Configure uma regra *de bloco* diferente para `0.0.0.0/0` (a regra "bloquear tudo")
1. Certifique-se de que as regras são ordenadas corretamente, com a regra de permitir a regra de permitir acima da regra do bloco.

Na prática, este processo em várias etapas causa confusão. Os utilizadores não configuraram a regra "bloquear tudo" ou não ordenaram corretamente as regras, resultando numa exposição não intencional. 

O novo filtro IP bloqueia todos os endereços IP por predefinição. Apenas as gamas IP que adiciona explicitamente podem ligar-se ao IoT Hub. No exemplo acima, os passos 2 e 3 já não são necessários. Este novo comportamento simplifica a configuração e respeita o [princípio seguro por defeito](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Proteja o ponto final compatível do Event Hub incorporado

O filtro IP clássico não pode ser aplicado no ponto final incorporado. Esta limitação significa que, evento com um bloco toda a regra (bloco `0.0.0.0/0` ) configurado, o ponto final incorporado ainda está acessível a partir de qualquer endereço IP.

O novo filtro IP oferece uma opção de aplicar regras ao ponto final incorporado, o que reduz a exposição a ameaças à segurança da rede.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Imagem mostrando o toggle para aplicar ao ponto final incorporado ou não":::

> [!NOTE]
> Esta opção não está disponível para hubs IoT gratuitos (F1). Para aplicar as regras do filtro IP no ponto final incorporado, utilize um hub IoT pago.

### <a name="api-impact"></a>Impacto da API

O filtro IP atualizado está disponível na API de recursos IoT Hub a partir `2020-08-31` de (bem `2020-08-31-preview` como) e em diante. O filtro IP clássico ainda está disponível em todas as versões API, mas será removido numa futura versão API perto do prazo de migração. Para saber mais sobre a linha temporal da migração, consulte [a atualização do Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Dica: experimente as alterações antes de se aplicarem

Uma vez que o novo filtro IP bloqueia todos os endereços IP por padrão, as regras individuais de bloqueio já não são compatíveis. Assim, o processo de atualização guiado remove estas regras individuais de bloqueio. 

Para tentar a mudança com o filtro IP clássico:

1. Visite o **separador Networking** no seu hub IoT
1. Note a configuração do filtro IP existente (clássico), caso pretenda voltar
1. Ao lado das regras com **Bloco,** selecione o ícone do lixo para removê-las
1. Adicione outra regra na parte inferior com `0.0.0.0/0` , e escolha **Bloco**
1. Selecione **Guardar**

Esta configuração imita como o novo filtro IP se comporta após a atualização do clássico. Uma exceção é a proteção do ponto final incorporado, que não é possível tentar usar o filtro IP clássico. No entanto, esta funcionalidade é opcional, por isso não tens de a usar se achares que pode quebrar alguma coisa.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Dica: verifique registos de diagnóstico de todas as ligações IP ao seu hub IoT

Para garantir uma transição suave, verifique os seus registos de diagnóstico na categoria Ligações. Procure a `maskedIpAddress` propriedade para ver se os intervalos são como você espera. Lembre-se: o novo filtro IP bloqueará todos os endereços IP que não tenham sido explicitamente adicionados.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Documentação clássica do filtro IP do IoT Hub (aposentado)

> [!IMPORTANT]
> Abaixo está a documentação original para o filtro IP clássico, que está a ser retirado.

A segurança é um aspeto importante de qualquer solução IoT baseada no Azure IoT Hub. Por vezes, como parte da sua configuração de segurança, tem de especificar explicitamente os endereços IP a partir dos quais os dispositivos se podem ligar. A função *de filtro IP* permite-lhe configurar regras para rejeitar ou aceitar tráfego a partir de endereços IPv4 específicos.

### <a name="when-to-use"></a>Quando utilizar

Existem dois casos de utilização específicos quando é útil bloquear os pontos finais do IoT Hub para determinados endereços IP:

* O seu hub IoT só deve receber tráfego a partir de uma gama especificada de endereços IP e rejeitar tudo o resto. Por exemplo, está a usar o seu hub IoT com [a Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) para criar ligações privadas entre um hub IoT e a sua infraestrutura no local.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do hub IoT.

### <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras do filtro IP são aplicadas ao nível de serviço IoT Hub. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações de back-end utilizando qualquer protocolo suportado. No entanto, os clientes que lêem diretamente a partir do [ponto final compatível com](iot-hub-devguide-messages-read-builtin.md) o Event Hub (não através da cadeia de ligação IoT Hub) não estão ligados às regras do filtro IP. 

Qualquer tentativa de ligação a partir de um endereço IP que corresponda a uma regra IP rejeitada no seu hub IoT recebe um código de estado 401 não autorizado e descrição. A mensagem da resposta não menciona a regra de IP. Rejeitar endereços IP pode impedir que outros serviços Azure Stream, como a Azure Stream Analytics, Azure Virtual Machines ou o portal Device Explorer in Azure interajam com o hub IoT.

> [!NOTE]
> Se tiver de utilizar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com filtro IP ativado, utilize o nome e o ponto final compatíveis com o hub do seu hub IoT para adicionar manualmente uma entrada de [stream de Centros de Eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) na ASA.

### <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha **de filtro IP** no portal para um hub IoT está vazia. Esta definição predefinida significa que o seu hub aceita ligações a partir de qualquer endereço IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

Para aceder à página de definições do Filtro IP, **selecione Networking**, **Acesso público,** em seguida, escolha **Gamas IP Selecionadas**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Definições de filtro IP padrão IoT Hub":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro de IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar Regra de Filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Adicione uma regra de filtro IP a um hub IoT":::

Depois de selecionar **Adicionar Regra de Filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Depois de selecionar Adicionar uma regra de Filtro de IP":::

* Indique um **nome** para a regra de Filtro de IP. O nome tem de ser uma cadeia alfanumérica exclusiva, sensível a maiúsculas e minúsculas com um máximo de 128 carateres. Só são aceites os carateres alfanuméricos ASCII 7-bit mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Indique um endereço IPv4 individual ou um bloco de endereços IP em notação CIDR. Por exemplo, em notação CIDR, 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** da regra de filtro de IP.

Depois de preencher os campos, selecione **Guardar** para guardar a regra. Pode ver um alerta que o notifica que a atualização está em curso.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Notificação relativa a guardar uma regra de filtro de IP":::

Quando alcança o máximo de dez regras de filtros de IP, a opção **Adicionar** é desativada.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e selecione **Guardar** para guardar a edição.

### <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro de IP

Para eliminar uma regra de filtro de IP, selecione o ícone de caixote do lixo nessa linha e selecione **Guardar**. A regra é removida e a alteração guardada.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Eliminar uma regra de filtro IP do Hub IoT":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recupere e atualize filtros IP usando O Azure CLI

Os filtros IP do seu IoT Hub podem ser recuperados e atualizados através do [Azure CLI](/cli/azure/).

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

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recupere e atualize filtros IP usando Azure PowerShell

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

### <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro IP usando REST

Também pode recuperar e modificar o filtro IP do seu Hub IoT utilizando o ponto final REST do Fornecedor de Recursos Azure. Veja `properties.ipFilterRules` no [método createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Avaliação das regras de filtros de IP

As regras de filtros de IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se quiser aceitar os endereços no intervalo 192.168.100.0/22 e rejeitar todos os outros, a primeira regra na grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A regra seguinte deve rejeitar todos os endereços mediante a utilização do intervalo 0.0.0.0/0.

Pode alterar a ordem das regras dos filtros de IP na grelha ao clicar nos três pontos verticais no início de uma linha e arrastar e largar.

Para guardar a ordem nova das regras dos filtros de IP, clique em **Guardar**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Altere a ordem das suas regras de filtro IP IoT Hub":::

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Utilizar filtros de IP](iot-hub-ip-filtering.md)