---
title: Utilização da PowerShell para gerir o Gestor de Tráfego em Azure
description: Com este caminho de aprendizagem, começa a usar o Azure PowerShell para Traffic Manager.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400573"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Utilização do PowerShell para gerir o Gestor de Tráfego

O Azure Resource Manager é a interface de gestão preferida para serviços em Azure. Os perfis do Azure Traffic Manager podem ser geridos usando APIs e ferramentas baseadas em Recursos Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Modelo de recursos

O Azure Traffic Manager está configurado usando uma coleção de configurações chamadas de perfil de Gestor de Tráfego. Este perfil contém definições de DNS, definições de encaminhamento de tráfego, definições de monitorização de pontos finais e uma lista de pontos finais de serviço para os quais o tráfego é encaminhado.

Cada perfil de Gestor de Tráfego é representado por um recurso do tipo 'TrafficManagerProfiles'. Ao nível da API REST, o URI para cada perfil é o seguinte:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Criação do Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Estas instruções utilizam o Microsoft Azure PowerShell. O seguinte artigo explica como instalar e configurar a Azure PowerShell.

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/)

Os exemplos deste artigo pressupõem que tem um grupo de recursos existente. Pode criar um grupo de recursos utilizando o seguinte comando:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> O Gestor de Recursos Azure exige que todos os grupos de recursos tenham uma localização. Esta localização é usada como o padrão para os recursos criados nesse grupo de recursos. No entanto, uma vez que os recursos de perfil do Gestor de Tráfego são globais, não regionais, a escolha da localização do grupo de recursos não tem impacto no Gestor de Tráfego da Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil de gestor de tráfego

Para criar um perfil de Gestor de Tráfego, utilize o `New-AzTrafficManagerProfile` cmdlet:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela a seguir descreve os parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| Nome |O nome de recurso para o recurso de perfil do Gestor de Tráfego. Os perfis do mesmo grupo de recursos devem ter nomes únicos. Este nome é separado do nome DNS usado para consultas DNS. |
| ResourceGroupName |O nome do grupo de recursos que contém o recurso de perfil. |
| TrafficRoutingMethod |Especifica o método de encaminhamento de tráfego utilizado para determinar qual o ponto final devolvido em resposta a uma consulta de DNS. Os valores possíveis são 'Performance', 'Ponderado' ou 'Prioridade'. |
| Nome relativo |Especifica a parte do nome anfitrião do nome DNS fornecido por este perfil de Gestor de Tráfego. Este valor é combinado com o nome de domínio DNS usado pelo Azure Traffic Manager para formar o nome de domínio totalmente qualificado (FQDN) do perfil. Por exemplo, definir o valor de 'contoso' torna-se "contoso.trafficmanager.net". |
| TTL |Especifica o DNS Time-to-Live (TTL), em segundos. Este TTL informa os clientes dns locais e DNS quanto tempo demoram a cache respostas DNS para este perfil de Gestor de Tráfego. |
| MonitorProtocol |Especifica o protocolo a utilizar para monitorizar a saúde do ponto final. Os valores possíveis são 'HTTP' e 'HTTPS'. |
| MonitorPort |Especifica a porta TCP utilizada para monitorizar a saúde do ponto final. |
| MonitorPath |Especifica o caminho relativo ao nome de domínio do ponto final usado para sondar para a saúde do ponto final. |

O cmdlet cria um perfil de Gestor de Tráfego em Azure e devolve um objeto de perfil correspondente ao PowerShell. Neste ponto, o perfil não contém quaisquer pontos finais. Para obter mais informações sobre a adição de pontos finais a um perfil de Gestor de Tráfego, consulte adicionar pontos de final do Gestor de Tráfego.

## <a name="get-a-traffic-manager-profile"></a>Obtenha um perfil de gestor de tráfego

Para recuperar um objeto de perfil do Gestor de Tráfego existente, utilize o `Get-AzTrafficManagerProfle` cmdlet:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Este cmdlet devolve um objeto de perfil do Gestor de Tráfego.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil de gestor de tráfego

Modificar os perfis do Gestor de Tráfego segue um processo de 3 etapas:

1. Recupere o perfil utilizando `Get-AzTrafficManagerProfile` ou utilize o perfil devolvido por `New-AzTrafficManagerProfile` .
2. Modifique o perfil. Pode adicionar e remover pontos finais ou alterar parâmetros de ponto final ou perfil. Estas alterações são operações fora de linha. Só está a mudar o objeto local na memória que representa o perfil.
3. Cometa as alterações utilizando o `Set-AzTrafficManagerProfile` cmdlet.

Todas as propriedades de perfil podem ser alteradas, exceto o nome relativo do perfil. Para alterar o Nome RelativoDns, tem de eliminar o perfil e um novo perfil com um novo nome.

O exemplo a seguir demonstra como alterar o TTL do perfil:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Existem três tipos de pontos finais do Gestor de Tráfego:

1. **Pontos finais do Azure** são serviços hospedados em Azure
2. **Pontos finais externos** são serviços hospedados fora de Azure
3. **Os pontos finais aninhados** são usados para construir hierarquias aninhadas de perfis de Gestor de Tráfego. Os pontos finais aninhados permitem configurações avançadas de encaminhamento de tráfego para aplicações complexas.

Em todos os três casos, os pontos finais podem ser adicionados de duas formas:

1. Utilizando um processo de 3 passos descrito anteriormente. A vantagem deste método é que várias alterações no ponto final podem ser feitas numa única atualização.
2. Utilizando o New-AzTrafficManagerEndpoint cmdlet. Este cmdlet adiciona um ponto final a um perfil de Gestor de Tráfego existente numa única operação.

## <a name="adding-azure-endpoints"></a>Adicionar pontos finais Azure

Azure endpoints serviços de referência hospedados em Azure. São suportados dois tipos de pontos finais Azure:

1. Serviço de Aplicações do Azure
2. Recursos Azure PublicIpAddress (que podem ser ligados a um equilibrador de carga ou a uma máquina virtual NIC). O PublicIpAddress deve ter um nome DNS atribuído para ser usado no Traffic Manager.

Em cada caso:

* O serviço é especificado utilizando o parâmetro 'targetResourceId' de `Add-AzTrafficManagerEndpointConfig` ou `New-AzTrafficManagerEndpoint` .
* O 'Target' e 'EndpointLocation' são implícitos pelo TargetResourceId.
* Especificar o 'Peso' é opcional. Os pesos só são utilizados se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego "ponderado". Caso contrário, são ignorados. Se especificado, o valor deve ser um número entre 1 e 1000. O valor predefinido é '1'.
* Especificar a "Prioridade" é opcional. As prioridades só são utilizadas se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego "Prioritário". Caso contrário, são ignorados. Os valores válidos são de 1 a 1000 com valores mais baixos indicando uma prioridade maior. Se especificados para um ponto final, devem ser especificados para todos os pontos finais. Se omitidos, os valores predefinidos a partir de '1' são aplicados na ordem em que os pontos finais estão listados.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Exemplo 1: Adicionar pontos finais do Serviço de Aplicações usando `Add-AzTrafficManagerEndpointConfig`

Neste exemplo, criamos um perfil de Gestor de Tráfego e adicionamos dois pontos finais do Serviço de Aplicações utilizando o `Add-AzTrafficManagerEndpointConfig` cmdlet.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar um ponto final doIpAddress publicitário usando `New-AzTrafficManagerEndpoint`

Neste exemplo, é adicionado um recurso de endereço IP público ao perfil de Gestor de Tráfego. O endereço IP público deve ter um nome DNS configurado, e pode ser ligado ao NIC de um VM ou a um equilibrador de carga.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Adicionar pontos de final externos

O Gestor de Tráfego utiliza pontos finais externos para direcionar o tráfego para os serviços alojados fora de Azure. Tal como acontece com os pontos finais do Azure, os pontos finais externos podem ser adicionados quer através da `Add-AzTrafficManagerEndpointConfig` utilização seguida por `Set-AzTrafficManagerProfile` , ou `New-AzTrafficManagerEndpoint` .

Ao especificar pontos finais externos:

* O nome de domínio do ponto final deve ser especificado utilizando o parâmetro 'Target'
* Se for utilizado o método de encaminhamento de tráfego 'Performance', é necessário o 'EndpointLocation'. Caso contrário, é opcional. O valor deve ser um [nome válido da região Azure.](https://azure.microsoft.com/regions/)
* O "Peso" e "Prioridade" são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais externos utilizando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, criamos um perfil de Gestor de Tráfego, adicionamos dois pontos finais externos e cometemos as alterações.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais externos usando `New-AzTrafficManagerEndpoint`

Neste exemplo, adicionamos um ponto final externo a um perfil existente. O perfil é especificado usando os nomes de grupo de perfil e recursos.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Adicionar pontos finais 'Aninhados'

Cada perfil do Gestor de Tráfego especifica um único método de encaminhamento de tráfego. No entanto, existem cenários que requerem um encaminhamento de tráfego mais sofisticado do que o encaminhamento fornecido por um único perfil de Gestor de Tráfego. Pode nidificar os perfis do Traffic Manager para combinar os benefícios de mais de um método de encaminhamento de tráfego. Os perfis aninhados permitem-lhe anular o comportamento padrão do Gestor de Tráfego para suportar implementações de aplicações maiores e mais complexas. Para obter exemplos mais detalhados, consulte [os perfis do Gestor de Tráfego Nested](traffic-manager-nested-profiles.md).

Os pontos finais aninhados são configurados no perfil dos pais, utilizando um tipo específico de ponto final, 'NestedEndpoints'. Ao especificar os pontos finais aninhados:

* O ponto final deve ser especificado utilizando o parâmetro 'targetResourceId'
* Se for utilizado o método de encaminhamento de tráfego 'Performance', é necessário o 'EndpointLocation'. Caso contrário, é opcional. O valor deve ser um [nome válido da região Azure.](https://azure.microsoft.com/regions/)
* O "Peso" e "Prioridade" são opcionais, como para os pontos finais do Azure.
* O parâmetro 'MinChildEndpoints' é opcional. O valor predefinido é '1'. Se o número de pontos finais disponíveis ficar abaixo deste limiar, o perfil dos pais considera o perfil da criança "degradado" e desvia o tráfego para os outros pontos finais no perfil dos pais.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais aninhados usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, criamos novos perfis de crianças e pais do Traffic Manager, adicionamos a criança como um ponto final aninhado ao progenitor, e cometemos as alterações.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Para a brevidade neste exemplo, não adicionámos quaisquer outros pontos finais aos perfis da criança ou dos pais.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais aninhados usando `New-AzTrafficManagerEndpoint`

Neste exemplo, adicionamos um perfil infantil existente como um ponto final aninhado a um perfil parental existente. O perfil é especificado usando os nomes de grupo de perfil e recursos.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Adicionar pontos finais de outra subscrição

O Gestor de Tráfego pode trabalhar com pontos finais de diferentes subscrições. Tem de mudar para a subscrição com o ponto final que pretende adicionar para recuperar a entrada necessária para o Gestor de Tráfego. Em seguida, tem de mudar para as subscrições com o perfil de Gestor de Tráfego e adicionar-lhe o ponto final. O exemplo abaixo mostra como fazê-lo com um endereço IP público.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto de final do Gestor de Tráfego

Existem duas formas de atualizar um ponto final existente do Gestor de Tráfego:

1. Obtenha o perfil de Gestor de Tráfego `Get-AzTrafficManagerProfile` utilizando, atualize as propriedades do ponto final dentro do perfil e cometa as alterações utilizando `Set-AzTrafficManagerProfile` . Este método tem a vantagem de poder atualizar mais do que um ponto final numa única operação.
2. Obtenha o ponto final do Gestor de Tráfego `Get-AzTrafficManagerEndpoint` utilizando, atualize as propriedades do ponto final e cometa as alterações utilizando `Set-AzTrafficManagerEndpoint` . Este método é mais simples, uma vez que não requer a indexação na matriz de Pontos Finais no perfil.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Atualizar pontos finais utilizando `Get-AzTrafficManagerProfile` e `Set-AzTrafficManagerProfile`

Neste exemplo, modificamos a prioridade em dois pontos finais dentro de um perfil existente.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Exemplo 2: Atualizar um ponto final utilizando `Get-AzTrafficManagerEndpoint` e `Set-AzTrafficManagerEndpoint`

Neste exemplo, modificamos o peso de um único ponto final num perfil existente.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Habilitação e desativação de pontos finais e perfis

O Traffic Manager permite que os pontos finais individuais sejam ativados e desativados, bem como permitir a ativação e desativação de perfis inteiros.
Estas alterações podem ser feitas obtenda/atualização/definição dos recursos do ponto final ou do perfil. Para dinamizar estas operações comuns, são também apoiadas através de cmdlets dedicados.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Habilitar e desativar um perfil do Gestor de Tráfego

Para ativar um perfil de Gestor de Tráfego, `Enable-AzTrafficManagerProfile` utilize. O perfil pode ser especificado usando um objeto de perfil. O objeto de perfil pode ser passado através do oleoduto ou utilizando o parâmetro 'TrafficManagerProfile'. Neste exemplo, especificamos o perfil pelo perfil e nome do grupo de recursos.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desativar um perfil de Gestor de Tráfego:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O Disable-AzTrafficManagerProfile cmdlet solicita confirmação. Esta indicação pode ser suprimida utilizando o parâmetro "Força".

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Permitir e desativar um ponto final do Gestor de Tráfego

Para ativar um ponto final do Traffic Manager, utilize `Enable-AzTrafficManagerEndpoint` . Há duas maneiras de especificar o ponto final

1. Utilizando um objeto TrafficManagerEndpoint passado através do oleoduto ou utilizando o parâmetro 'TrafficManagerEndpoint'
2. Utilizando o nome do ponto final, o tipo de ponto final, o nome do perfil e o nome do grupo de recursos:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, desativar um ponto final do Gestor de Tráfego:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Tal como `Disable-AzTrafficManagerProfile` acontece, o `Disable-AzTrafficManagerEndpoint` cmdlet solicita a confirmação. Esta indicação pode ser suprimida utilizando o parâmetro "Força".

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminar um ponto de final do Gestor de Tráfego

Para remover pontos finais individuais, utilize o `Remove-AzTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Este cmdlet solicita confirmação. Esta indicação pode ser suprimida utilizando o parâmetro "Força".

## <a name="delete-a-traffic-manager-profile"></a>Excluir um perfil de gestor de tráfego

Para eliminar um perfil de Gestor de Tráfego, utilize o `Remove-AzTrafficManagerProfile` cmdlet, especificando os nomes do perfil e do grupo de recursos:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Este cmdlet solicita confirmação. Esta indicação pode ser suprimida utilizando o parâmetro "Força".

O perfil a eliminar também pode ser especificado utilizando um objeto de perfil:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Esta sequência também pode ser canalizada:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passos seguintes

[Monitorização do Gestor de Tráfego](traffic-manager-monitoring.md)

[Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
