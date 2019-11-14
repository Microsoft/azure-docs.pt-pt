---
title: Usando o PowerShell para gerenciar o Gerenciador de tráfego no Azure
description: Com este roteiro de aprendizagem, comece a usar o Azure PowerShell para o Gerenciador de tráfego.
services: traffic-manager
documentationcenter: na
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: f8dd01f22dec58c3345798b391c1c37c968d1025
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038127"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Usando o PowerShell para gerenciar o Gerenciador de tráfego

Azure Resource Manager é a interface de gerenciamento preferencial para serviços no Azure. Os perfis do Gerenciador de tráfego do Azure podem ser gerenciados usando APIs e ferramentas baseadas em Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Modelo de recursos

O Gerenciador de tráfego do Azure é configurado usando uma coleção de configurações chamada de perfil do Gerenciador de tráfego. Esse perfil contém configurações de DNS, configurações de roteamento de tráfego, configurações de monitoramento de ponto de extremidade e uma lista de pontos de extremidade de serviço para os quais o tráfego é roteado.

Cada perfil do Gerenciador de tráfego é representado por um recurso do tipo ' TrafficManagerProfiles '. No nível da API REST, o URI para cada perfil é o seguinte:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Configurando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Essas instruções usam Microsoft Azure PowerShell. O artigo a seguir explica como instalar e configurar o Azure PowerShell.

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

Os exemplos neste artigo pressupõem que você tenha um grupo de recursos existente. Você pode criar um grupo de recursos usando o seguinte comando:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager requer que todos os grupos de recursos tenham um local. Esse local é usado como o padrão para os recursos criados nesse grupo de recursos. No entanto, como os recursos de perfil do Gerenciador de tráfego são globais, e não regionais, a escolha do local do grupo de recursos não afeta o Gerenciador de tráfego do Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de tráfego

Para criar um perfil do Gerenciador de tráfego, use o cmdlet `New-AzTrafficManagerProfile`:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela a seguir descreve os parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| Nome |O nome do recurso para o recurso de perfil do Gerenciador de tráfego. Os perfis no mesmo grupo de recursos devem ter nomes exclusivos. Esse nome é separado do nome DNS usado para consultas DNS. |
| ResourceGroupName |O nome do grupo de recursos que contém o recurso de perfil. |
| TrafficRoutingMethod |Especifica o método de roteamento de tráfego usado para determinar qual ponto de extremidade é retornado em resposta a uma consulta DNS. Os valores possíveis são "desempenho", "ponderado" ou "prioridade". |
| RelativeDnsName |Especifica a parte hostname do nome DNS fornecido por este perfil do Gerenciador de tráfego. Esse valor é combinado com o nome de domínio DNS usado pelo Gerenciador de tráfego do Azure para formar o FQDN (nome de domínio totalmente qualificado) do perfil. Por exemplo, definir o valor de ' contoso ' se torna ' contoso.trafficmanager.net '. |
| TTL |Especifica o tempo de vida (TTL) do DNS, em segundos. Esse TTL informa os resolvedores DNS locais e os clientes DNS por quanto tempo armazenar em cache as respostas de DNS para esse perfil do Gerenciador de tráfego. |
| MonitorProtocol |Especifica o protocolo a ser usado para monitorar a integridade do ponto de extremidade. Os valores possíveis são "HTTP" e "HTTPS". |
| MonitorPort |Especifica a porta TCP usada para monitorar a integridade do ponto de extremidade. |
| MonitorPath |Especifica o caminho relativo ao nome de domínio do ponto de extremidade usado para investigar a integridade do ponto de extremidade. |

O cmdlet cria um perfil do Gerenciador de tráfego no Azure e retorna um objeto de perfil correspondente ao PowerShell. Neste ponto, o perfil não contém nenhum ponto de extremidade. Para obter mais informações sobre como adicionar pontos de extremidade a um perfil do Gerenciador de tráfego, consulte Adicionando pontos de extremidade do Gerenciador de tráfego.

## <a name="get-a-traffic-manager-profile"></a>Obter um perfil do Gerenciador de tráfego

Para recuperar um objeto de perfil do Gerenciador de tráfego existente, use o cmdlet `Get-AzTrafficManagerProfle`:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Esse cmdlet retorna um objeto de perfil do Gerenciador de tráfego.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil do Gerenciador de tráfego

A modificação de perfis do Gerenciador de tráfego segue um processo de 3 etapas:

1. Recupere o perfil usando `Get-AzTrafficManagerProfile` ou use o perfil retornado por `New-AzTrafficManagerProfile`.
2. Modifique o perfil. Você pode adicionar e remover pontos de extremidade ou alterar os parâmetros de ponto final ou de perfil. Essas alterações são operações off-line. Você está apenas alterando o objeto local na memória que representa o perfil.
3. Confirme suas alterações usando o cmdlet `Set-AzTrafficManagerProfile`.

Todas as propriedades de perfil podem ser alteradas, exceto o RelativeDnsName do perfil. Para alterar o RelativeDnsName, você deve excluir o perfil e um novo perfil com um novo nome.

O exemplo a seguir demonstra como alterar o TTL do perfil:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Há três tipos de pontos de extremidade do Gerenciador de tráfego:

1. Os **pontos de extremidade do Azure** são serviços hospedados no Azure
2. Os **pontos de extremidade externos** são serviços hospedados fora do Azure
3. **Pontos de extremidade aninhados** são usados para construir hierarquias aninhadas de perfis do Gerenciador de tráfego. Os pontos de extremidade aninhados permitem configurações avançadas de roteamento de tráfego para aplicativos complexos.

Em todos os três casos, os pontos de extremidade podem ser adicionados de duas maneiras:

1. Usando um processo de 3 etapas descrito anteriormente. A vantagem desse método é que várias alterações de ponto de extremidade podem ser feitas em uma única atualização.
2. Usando o cmdlet New-AzTrafficManagerEndpoint. Esse cmdlet adiciona um ponto de extremidade a um perfil do Gerenciador de tráfego existente em uma única operação.

## <a name="adding-azure-endpoints"></a>Adicionando Pontos de Extremidade do Azure

Os pontos de extremidade do Azure referenciam serviços hospedados no Azure. Há suporte para dois tipos de pontos de extremidade do Azure:

1. Serviço de Aplicações do Azure
2. Recursos do Azure PublicIpAddress (que podem ser anexados a um balanceador de carga ou a uma NIC de máquina virtual). O PublicIpAddress deve ter um nome DNS atribuído para ser usado no Gerenciador de tráfego.

Em cada caso:

* O serviço é especificado usando o parâmetro ' targetResourceId ' de `Add-AzTrafficManagerEndpointConfig` ou `New-AzTrafficManagerEndpoint`.
* Os ' target ' e ' EndpointLocation ' são implícitos pelo TargetResourceId.
* A especificação de ' weight ' é opcional. Os pesos só serão usados se o perfil estiver configurado para usar o método de roteamento de tráfego "ponderado". Caso contrário, eles serão ignorados. Se especificado, o valor deve ser um número entre 1 e 1000. O valor padrão é ' 1 '.
* A especificação de ' priority ' é opcional. As prioridades só serão usadas se o perfil estiver configurado para usar o método de roteamento de tráfego de "prioridade". Caso contrário, eles serão ignorados. Os valores válidos são de 1 a 1000 com valores mais baixos indicando uma prioridade mais alta. Se especificado para um ponto de extremidade, eles deverão ser especificados para todos os pontos de extremidades. Se omitido, os valores padrão que começam de ' 1 ' serão aplicados na ordem em que os pontos de extremidade são listados.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Exemplo 1: adicionando pontos de extremidade do serviço de aplicativo usando `Add-AzTrafficManagerEndpointConfig`

Neste exemplo, criamos um perfil do Gerenciador de tráfego e adicionamos dois pontos de extremidade do serviço de aplicativo usando o cmdlet `Add-AzTrafficManagerEndpointConfig`.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: adicionando um ponto de extremidade publicIpAddress usando `New-AzTrafficManagerEndpoint`

Neste exemplo, um recurso de endereço IP público é adicionado ao perfil do Gerenciador de tráfego. O endereço IP público deve ter um nome DNS configurado e pode ser associado à NIC de uma VM ou a um balanceador de carga.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Adicionando Pontos de Extremidade Externos

O Gerenciador de tráfego usa pontos de extremidade externos para direcionar o tráfego para serviços hospedados fora do Azure. Assim como os pontos de extremidade do Azure, os pontos de extremidade externos podem ser adicionados usando `Add-AzTrafficManagerEndpointConfig` seguido por `Set-AzTrafficManagerProfile`ou `New-AzTrafficManagerEndpoint`.

Ao especificar pontos de extremidade externos:

* O nome de domínio do ponto de extremidade deve ser especificado usando o parâmetro ' target '
* Se o método de roteamento de tráfego de "desempenho" for usado, o "EndpointLocation" será necessário. Caso contrário, é opcional. O valor deve ser um [nome de região do Azure válido](https://azure.microsoft.com/regions/).
* ' Weight ' e ' priority ' são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: adicionando pontos de extremidade externos usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, criamos um perfil do Gerenciador de tráfego, adicionamos dois pontos de extremidade externos e confirmamos as alterações.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: adicionando pontos de extremidade externos usando `New-AzTrafficManagerEndpoint`

Neste exemplo, adicionamos um ponto de extremidade externo a um perfil existente. O perfil é especificado usando os nomes do grupo de recursos e do perfil.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Adicionando pontos de extremidade ' aninhados '

Cada perfil do Gerenciador de tráfego especifica um único método de roteamento de tráfego. No entanto, há cenários que exigem um roteamento de tráfego mais sofisticado do que o roteamento fornecido por um único perfil do Gerenciador de tráfego. Você pode aninhar perfis do Gerenciador de tráfego para combinar os benefícios de mais de um método de roteamento de tráfego. Os perfis aninhados permitem que você substitua o comportamento padrão do Gerenciador de tráfego para dar suporte a implantações de aplicativos maiores e mais complexas. Para obter exemplos mais detalhados, consulte [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).

Os pontos de extremidade aninhados são configurados no perfil pai, usando um tipo de ponto final específico, ' NestedEndpoints '. Ao especificar pontos de extremidade aninhados:

* O ponto de extremidade deve ser especificado usando o parâmetro ' targetResourceId '
* Se o método de roteamento de tráfego de "desempenho" for usado, o "EndpointLocation" será necessário. Caso contrário, é opcional. O valor deve ser um [nome de região do Azure válido](https://azure.microsoft.com/regions/).
* ' Weight ' e ' priority ' são opcionais, como para pontos de extremidade do Azure.
* O parâmetro ' MinChildEndpoints ' é opcional. O valor padrão é ' 1 '. Se o número de pontos de extremidade disponíveis cair abaixo desse limite, o perfil pai considerará o perfil filho ' degradado ' e o tráfego para os outros pontos de extremidade no perfil pai.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: adicionando pontos de extremidade aninhados usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, criamos novos perfis filho e pai do Gerenciador de tráfego, adicionamos o filho como um ponto de extremidade aninhado ao pai e confirmamos as alterações.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Para fins de brevidade neste exemplo, não adicionamos nenhum outro ponto de extremidade aos perfis filho ou pai.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: adicionando pontos de extremidade aninhados usando `New-AzTrafficManagerEndpoint`

Neste exemplo, adicionamos um perfil filho existente como um ponto de extremidade aninhado a um perfil pai existente. O perfil é especificado usando os nomes do grupo de recursos e do perfil.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Adicionando pontos de extremidade de outra assinatura

O Gerenciador de tráfego pode trabalhar com pontos de extremidade de assinaturas diferentes. Você precisa alternar para a assinatura com o ponto de extremidade que deseja adicionar para recuperar a entrada necessária para o Gerenciador de tráfego. Em seguida, você precisa alternar para as assinaturas com o perfil do Gerenciador de tráfego e adicionar o ponto de extremidade a ele. O exemplo abaixo mostra como fazer isso com um endereço IP público.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto de extremidade do Gerenciador de tráfego

Há duas maneiras de atualizar um ponto de extremidade existente do Gerenciador de tráfego:

1. Obtenha o perfil do Gerenciador de tráfego usando `Get-AzTrafficManagerProfile`, atualize as propriedades do ponto de extremidade dentro do perfil e confirme as alterações usando `Set-AzTrafficManagerProfile`. Esse método tem a vantagem de ser capaz de atualizar mais de um ponto de extremidade em uma única operação.
2. Obtenha o ponto de extremidade do Gerenciador de tráfego usando `Get-AzTrafficManagerEndpoint`, atualize as propriedades do ponto de extremidade e confirme as alterações usando `Set-AzTrafficManagerEndpoint`. Esse método é mais simples, pois não requer a indexação na matriz de pontos de extremidade no perfil.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Atualizando pontos de extremidade usando `Get-AzTrafficManagerProfile` e `Set-AzTrafficManagerProfile`

Neste exemplo, modificamos a prioridade em dois pontos de extremidade em um perfil existente.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Exemplo 2: atualizando um ponto de extremidade usando `Get-AzTrafficManagerEndpoint` e `Set-AzTrafficManagerEndpoint`

Neste exemplo, modificamos o peso de um único ponto de extremidade em um perfil existente.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Habilitando e desabilitando os pontos de extremidade e perfis

O Gerenciador de tráfego permite que os pontos de extremidade individuais sejam habilitados e desabilitados, além de permitir a habilitação e a desabilitação de perfis inteiros.
Essas alterações podem ser feitas ao obter/atualizar/definir os recursos de ponto de extremidade ou perfil. Para simplificar essas operações comuns, elas também têm suporte por meio de cmdlets dedicados.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Habilitando e desabilitando um perfil do Gerenciador de tráfego

Para habilitar um perfil do Gerenciador de tráfego, use `Enable-AzTrafficManagerProfile`. O perfil pode ser especificado usando um objeto de perfil. O objeto de perfil pode ser passado por meio do pipeline ou usando o parâmetro '-TrafficManagerProfile '. Neste exemplo, especificamos o perfil pelo nome do perfil e do grupo de recursos.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desabilitar um perfil do Gerenciador de tráfego:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O cmdlet Disable-AzTrafficManagerProfile solicita confirmação. Esse prompt pode ser suprimido usando o parâmetro '-Force '.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Habilitando e desabilitando um ponto de extremidade do Gerenciador de tráfego

Para habilitar um ponto de extremidade do Gerenciador de tráfego, use `Enable-AzTrafficManagerEndpoint`. Há duas maneiras de especificar o ponto de extremidade

1. Usando um objeto TrafficManagerEndpoint passado pelo pipeline ou usando o parâmetro '-TrafficManagerEndpoint '
2. Usando o nome do ponto de extremidade, tipo de ponto de extremidade, nome do perfil e nome do grupo de recursos:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, para desabilitar um ponto de extremidade do Gerenciador de tráfego:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Assim como ocorre com `Disable-AzTrafficManagerProfile`, o cmdlet `Disable-AzTrafficManagerEndpoint` solicita confirmação. Esse prompt pode ser suprimido usando o parâmetro '-Force '.

## <a name="delete-a-traffic-manager-endpoint"></a>Excluir um ponto de extremidade do Gerenciador de tráfego

Para remover pontos de extremidade individuais, use o cmdlet `Remove-AzTrafficManagerEndpoint`:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Esse cmdlet solicita confirmação. Esse prompt pode ser suprimido usando o parâmetro '-Force '.

## <a name="delete-a-traffic-manager-profile"></a>Excluir um perfil do Gerenciador de tráfego

Para excluir um perfil do Gerenciador de tráfego, use o cmdlet `Remove-AzTrafficManagerProfile`, especificando os nomes do grupo de recursos e do perfil:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Esse cmdlet solicita confirmação. Esse prompt pode ser suprimido usando o parâmetro '-Force '.

O perfil a ser excluído também pode ser especificado usando um objeto de perfil:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Essa sequência também pode ser canalizada:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passos seguintes

[Monitoramento do Traffic Manager](traffic-manager-monitoring.md)

[Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
