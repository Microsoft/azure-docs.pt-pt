---
title: Com o PowerShell para gerir o Gestor de tráfego no Azure
description: Com o PowerShell para o Gestor de tráfego com o Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 2b480df0100690a7a5064044d435a34845516fa6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442108"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Com o PowerShell para gerir o Gestor de tráfego

O Azure Resource Manager é a interface de gestão preferenciais para serviços do Azure. Perfis do Gestor de tráfego do Azure podem ser geridos através de APIs baseadas no Azure Resource Manager e ferramentas.

## <a name="resource-model"></a>Modelo de recursos

O Gestor de tráfego do Azure está configurado com uma coleção de definições denominado um perfil do Gestor de tráfego. Este perfil contém as definições DNS, definições de encaminhamento de tráfego, as definições de monitorização do ponto de extremidade e uma lista de pontos finais de serviço ao qual o tráfego é encaminhado.

Cada perfil de Gestor de tráfego é representado por um recurso do tipo 'TrafficManagerProfiles'. Ao nível da REST API, o URI para cada perfil é o seguinte:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Configurar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Estas instruções utilizam o Microsoft Azure PowerShell. O seguinte artigo explica como instalar e configurar o Azure PowerShell.

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

Os exemplos neste artigo partem do princípio de que tem um grupo de recursos existente. Pode criar um grupo de recursos com o seguinte comando:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> O Azure Resource Manager requer que todos os grupos de recursos tem uma localização. Esta localização é utilizada como predefinição para os recursos criados nesse grupo de recursos. No entanto, uma vez que os recursos de perfil do Gestor de tráfego são globais, não regionais, a escolha da localização do grupo de recursos não tem impacto no Gestor de tráfego do Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de tráfego

Para criar um perfil do Gestor de tráfego, utilize o `New-AzTrafficManagerProfile` cmdlet:

```powershell
$profile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela seguinte descreve os parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| Name |O nome de recurso para o recurso de perfil do Gestor de tráfego. Perfis no mesmo grupo de recursos tem de ter nomes exclusivos. Este nome está separado do nome DNS utilizado para consultas DNS. |
| ResourceGroupName |O nome do grupo de recursos que contém o recurso de perfil. |
| TrafficRoutingMethod |Especifica o método de encaminhamento de tráfego de mensagens em fila utilizado para determinar qual ponto de extremidade é devolvido na resposta de uma consulta DNS. Valores possíveis são "Performance", 'Ponderado' ou 'Priority'. |
| RelativeDnsName |Especifica a parte do nome de anfitrião do nome DNS fornecido por este perfil do Gestor de tráfego. Este valor é combinado com o nome de domínio DNS utilizado pelo Gestor de tráfego do Azure para formar o nome de domínio completamente qualificado (FQDN) do perfil. Por exemplo, definir o valor de "contoso" se torna "contoso.trafficmanager.net." |
| TTL |Especifica o DNS Time-to-Live (TTL), em segundos. Este valor de TTL informa os clientes DNS quanto a respostas DNS de cache para este perfil do Gestor de tráfego e as resoluções Local DNS. |
| MonitorProtocol |Especifica o protocolo a utilizar para monitorizar o estado de funcionamento do ponto final. Valores possíveis são "HTTP" e "HTTPS". |
| MonitorPort |Especifica a porta TCP utilizada para monitorizar o estado de funcionamento do ponto final. |
| MonitorPath |Especifica o caminho relativo para o nome de domínio de ponto final utilizado para a sonda de estado de funcionamento do ponto final. |

O cmdlet cria um perfil do Gestor de tráfego no Azure e retorna um objeto de perfil correspondente para o PowerShell. Neste momento, o perfil não contém quaisquer pontos de extremidade. Para obter mais informações sobre como adicionar pontos de extremidade para um perfil do Gestor de tráfego, consulte Adicionar pontos finais do Gestor de tráfego.

## <a name="get-a-traffic-manager-profile"></a>Obter um perfil do Gestor de tráfego

Para obter um objeto de perfil do Traffic Manager existente, utilize o `Get-AzTrafficManagerProfle` cmdlet:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Este cmdlet devolve um objeto de perfil do Gestor de tráfego.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil do Gestor de tráfego

Modificar perfis do Gestor de tráfego segue um processo passo 3:

1. Obter o perfil a utilizar `Get-AzTrafficManagerProfile` ou utilizar o perfil retornado pelo `New-AzTrafficManagerProfile`.
2. Modificar o perfil. Pode adicionar e remover pontos de extremidade ou alterar os parâmetros de ponto final ou o perfil. Estas alterações são operações off-line. Está a alterar apenas o objeto local na memória que representa o perfil.
3. Consolidar as alterações usando o `Set-AzTrafficManagerProfile` cmdlet.

Todas as propriedades de perfil podem ser alteradas, exceto RelativeDnsName o perfil. Para alterar o RelativeDnsName, tem de eliminar o perfil e um novo perfil com um novo nome.

O exemplo seguinte demonstra como alterar o TTL do perfil:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

Existem três tipos de pontos finais do Gestor de tráfego:

1. **Pontos finais do Azure** são os serviços alojados no Azure
2. **Pontos finais externos** são serviços alojados fora do Azure
3. **Aninhados pontos finais** são usados para criar hierarquias aninhadas de perfis do Gestor de tráfego. Pontos de extremidade aninhados habilitar configurações de encaminhamento de tráfego de mensagens em fila avançadas para aplicativos complexos.

Em todos os três casos, os pontos de extremidade podem ser adicionados de duas formas:

1. Usando um processo de 3 etapas descrito anteriormente. A vantagem deste método é que podem ser feitas várias alterações de ponto final numa única atualização.
2. Usando o cmdlet New-AzTrafficManagerEndpoint. Este cmdlet adiciona um ponto final a um perfil do Gestor de tráfego existentes numa única operação.

## <a name="adding-azure-endpoints"></a>Adicionar pontos finais do Azure

Pontos finais do Azure fazem referência a serviços alojados no Azure. Dois tipos de pontos finais do Azure são suportados:

1. Serviço de Aplicações do Azure
2. Recursos PublicIpAddress do Azure (que podem ser anexados a um balanceador de carga ou uma máquina virtual NIC). O PublicIpAddress tem de ter um nome DNS atribuído a ser utilizado no Gestor de tráfego.

Em cada caso:

* O serviço for especificado com o parâmetro de "targetResourceId" do `Add-AzTrafficManagerEndpointConfig` ou `New-AzTrafficManagerEndpoint`.
* O 'Target' e 'EndpointLocation' são tidos pelo TargetResourceId.
* A especificação de "Weight" é opcional. Pesos são utilizados apenas se o perfil está configurado para utilizar o método de encaminhamento de tráfego de 'Ponderado'. Caso contrário, serão ignoradas. Se for especificado, o valor tem de ser um número entre 1 e 1000. O valor predefinido é '1'.
* A especificação de 'Priority' é opcional. As prioridades são utilizadas apenas se o perfil está configurado para utilizar o método de encaminhamento de tráfego de 'Priority'. Caso contrário, serão ignoradas. Valores válidos são entre 1 a 1000 com valores mais baixos que indica uma prioridade mais alta. Se especificada para um ponto final, tem de ser especificados para todos os pontos finais. Se for omitido, valores padrão a partir de '1' são aplicadas pela ordem em que os pontos finais estão listados.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Exemplo 1: Adicionar pontos finais de serviço de aplicações a utilizar `Add-AzTrafficManagerEndpointConfig`

Neste exemplo, vamos criar um perfil do Gestor de tráfego e dois pontos finais de serviço de aplicações através de adicionar o `Add-AzTrafficManagerEndpointConfig` cmdlet.

```powershell
$profile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar um através de ponto final de publicIpAddress `New-AzTrafficManagerEndpoint`

Neste exemplo, um recurso de endereço IP público é adicionado para o perfil do Gestor de tráfego. O endereço IP público tem de ter um nome DNS configurado e pode ser vinculado ao NIC de uma VM ou a um balanceador de carga.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Adicionar pontos finais externos

O Gestor de tráfego utiliza pontos finais externos para direcionar o tráfego para serviços alojados fora do Azure. Como com pontos finais do Azure, os pontos finais externos podem ser adicionados usando `Add-AzTrafficManagerEndpointConfig` seguido `Set-AzTrafficManagerProfile`, ou `New-AzTrafficManagerEndpoint`.

Quando especificar pontos finais externos:

* O nome de domínio de ponto final tem de ser especificado utilizando o parâmetro de "Destino"
* Se for utilizado o método de encaminhamento de tráfego de "Performance", 'EndpointLocation' é necessário. Caso contrário, é opcional. O valor tem de ser um [nome da região do Azure válida](https://azure.microsoft.com/regions/).
* O 'Peso' e 'Priority' são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais externos usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, vamos criar um perfil do Gestor de tráfego, adicione dois pontos de extremidade externos e consolidar as alterações.

```powershell
$profile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais externos com `New-AzTrafficManagerEndpoint`

Neste exemplo, podemos adicionar um ponto final externo para um perfil existente. O perfil é especificado com os nomes de grupo de recursos e perfil.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Adicionar pontos finais de 'Aninhados'

Cada perfil do Traffic Manager Especifica um único método de encaminhamento de tráfego. No entanto, há cenários que exigem que o encaminhamento fornecidos por um único perfil do Gestor de tráfego a encaminhamento de tráfego mais sofisticados. Pode aninhar perfis do Gestor de tráfego para combinar os benefícios de mais de um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento de Gestor de tráfego predefinido para suporte maior e implementações de aplicações mais complexas. Para obter exemplos mais detalhados, consulte [perfis do Gestor de tráfego aninhados](traffic-manager-nested-profiles.md).

Pontos de extremidade aninhados são configurados no perfil de principal, com um tipo de ponto final específico, 'NestedEndpoints'. Ao especificar os pontos de extremidade aninhados:

* O ponto final tem de ser especificado utilizando o parâmetro de "targetResourceId"
* Se for utilizado o método de encaminhamento de tráfego de "Performance", 'EndpointLocation' é necessário. Caso contrário, é opcional. O valor tem de ser um [nome da região do Azure válida](https://azure.microsoft.com/regions/).
* O 'Peso' e 'Priority' são opcionais, como para pontos finais do Azure.
* O parâmetro de "MinChildEndpoints" é opcional. O valor predefinido é '1'. Se o número de pontos finais disponíveis cai abaixo deste limiar, o perfil de principal considera o perfil de subordinados "degradado" e diverts tráfego para os pontos finais no perfil de principal.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais aninhados com `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

Neste exemplo, podemos criar novos principais e subordinados de Gestor de tráfego perfis, adicionar o filho como um ponto final aninhado para o pai e consolidar as alterações.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

Para fins de brevidade neste exemplo, não adicionamos outros pontos de extremidade para os perfis de subordinado ou principal.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais aninhados com `New-AzTrafficManagerEndpoint`

Neste exemplo, podemos adicionar um perfil existente do filho como um ponto final aninhado para um perfil de principal existente. O perfil é especificado com os nomes de grupo de recursos e perfil.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Adicionar pontos finais a partir de outra subscrição

O Gestor de tráfego pode trabalhar com pontos finais de subscrições diferentes. Terá de mudar para a subscrição com o ponto final que pretende adicionar ao obter a entrada necessária para o Gestor de tráfego. Em seguida, terá de mudar para as subscrições com o perfil do Gestor de tráfego e adicionar o ponto final ao mesmo. O exemplo abaixo mostra como fazer isso com um endereço IP público.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto de final do Gestor de tráfego

Existem duas maneiras de atualizar um ponto de extremidade existentes do Gestor de tráfego:

1. Obter o perfil do Gestor de tráfego utilizando `Get-AzTrafficManagerProfile`, Atualize as propriedades de ponto final no perfil e consolidar as alterações usando `Set-AzTrafficManagerProfile`. Este método tem a vantagem de ser possível atualizar a mais do que um ponto final numa única operação.
2. Obter o ponto final do Gestor de tráfego utilizando `Get-AzTrafficManagerEndpoint`, Atualize as propriedades do ponto final e consolidar as alterações usando `Set-AzTrafficManagerEndpoint`. Esse método é mais simples, uma vez que não requer a indexação na matriz de pontos finais no perfil.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Exemplo 1: A atualizar pontos de extremidade usando `Get-AzTrafficManagerProfile` e `Set-AzTrafficManagerProfile`

Neste exemplo, podemos modificar a prioridade em dois pontos finais dentro de um perfil existente.

```powershell
$profile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Exemplo 2: Como atualizar um ponto de extremidade usando `Get-AzTrafficManagerEndpoint` e `Set-AzTrafficManagerEndpoint`

Neste exemplo, podemos modificar o peso de um único ponto final num perfil existente.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Ativar e desativar pontos finais e perfis

O Gestor de tráfego permite que os pontos finais individuais seja ativado e desativado, bem como permite ativar e desativar dos perfis de todos.
Estas alterações podem ser feitas, obter/atualizar/definir o ponto final ou o perfil de recursos. Para simplificar essas operações comuns, também são suportadas através de cmdlets dedicados.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Ativar e desativar um perfil do Gestor de tráfego

Para ativar um perfil do Gestor de tráfego, utilize `Enable-AzureRmTrafficManagerProfile`. O perfil pode ser especificado com um objeto de perfil. O objeto de perfil pode ser passado via o pipeline ou utilizando o '-TrafficManagerProfile' parâmetro. Neste exemplo, especificamos o perfil, o nome do grupo de recursos e perfil.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desativar um perfil do Gestor de tráfego:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O cmdlet Disable-AzTrafficManagerProfile solicita a confirmação. Esta linha de comandos pode ser suprimida usando o "-forçar" parâmetro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Ativar e desativar um ponto de final do Gestor de tráfego

Para ativar um ponto de final do Gestor de tráfego, utilize `Enable-AzureRmTrafficManagerEndpoint`. Existem duas formas para especificar o ponto final

1. Utilizando um objeto de TrafficManagerEndpoint transmitido através do pipeline ou utilizando o '-TrafficManagerEndpoint' parâmetro
2. Usando o nome do ponto final, o tipo de ponto final, o nome do perfil e o nome do grupo de recursos:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, para desativar um ponto de final do Gestor de tráfego:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Tal como acontece com `Disable-AzTrafficManagerProfile`, o `Disable-AzTrafficManagerEndpoint` cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida usando o "-forçar" parâmetro.

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminar um ponto de final do Gestor de tráfego

Para remover os pontos finais individuais, utilize o `Remove-AzTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Este cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida usando o "-forçar" parâmetro.

## <a name="delete-a-traffic-manager-profile"></a>Eliminar um perfil do Gestor de tráfego

Para eliminar um perfil do Gestor de tráfego, utilize o `Remove-AzTrafficManagerProfile` cmdlet, especificando os nomes de grupo de recursos e de perfil:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Este cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida usando o "-forçar" parâmetro.

O perfil a ser eliminados também pode ser especificado usando um objeto de perfil:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Também pode ser direcionada esta sequência:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passos Seguintes

[Monitorização do Gestor de tráfego](traffic-manager-monitoring.md)

[Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
