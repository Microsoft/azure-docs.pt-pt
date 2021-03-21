---
title: Aumentar quota de ponto final - LUIS
titleSuffix: Azure Cognitive Services
description: A Compreensão linguística (LUIS) oferece a capacidade de aumentar a quota de pedido de ponto final para além da quota de uma única chave. Isto é feito criando mais chaves para o LUIS e adicionando-as à aplicação LUIS na página **Publicar** na secção **Recursos e Chaves.**
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609656"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Utilize o Gestor de Tráfego do Microsoft Azure para gerir a quota de ponto final através das teclas
A Compreensão linguística (LUIS) oferece a capacidade de aumentar a quota de pedido de ponto final para além da quota de uma única chave. Isto é feito criando mais chaves para o LUIS e adicionando-as à aplicação LUIS na página **Publicar** na secção **Recursos e Chaves.**

A aplicação do cliente tem de gerir o tráfego através das chaves. Luis não faz isto.

Este artigo explica como gerir o tráfego através das chaves com o Azure [Traffic Manager][traffic-manager-marketing]. Já deve ter uma aplicação LUIS treinada e publicada. Se não tiver um, siga o rápido arranque do domínio [pré-construído](luis-get-started-create-app.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ligue-se ao PowerShell no portal Azure
No portal [Azure,][azure-portal] abra a janela PowerShell. O ícone da janela PowerShell é o **>_** na barra de navegação superior. Ao utilizar o PowerShell a partir do portal, obtém a versão mais recente do PowerShell e é autenticado. PowerShell no portal requer uma conta [de Armazenamento Azure.](https://azure.microsoft.com/services/storage/)

![Screenshot do portal Azure com janela Powershell aberta](./media/traffic-manager/azure-portal-powershell.png)

As seguintes secções utilizam [cmdlets PowerShell do Gestor de Tráfego](/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Criar grupo de recursos Azure com PowerShell
Antes de criar os recursos Azure, crie um grupo de recursos para conter todos os recursos. Nomeie o grupo de recursos `luis-traffic-manager` e utilize a `West US` região. A região do grupo de recursos armazena metadados sobre o grupo. Não vai abrandar os seus recursos se estiverem noutra região.

Criar grupo de recursos com cmdlet **[New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Criar chaves LUIS para aumentar a quota total de pontos finais
1. No portal Azure, crie duas teclas **de Compreensão linguística,** uma na `West US` e outra na `East US` . Utilize o grupo de recursos existente, criado na secção anterior, denominado `luis-traffic-manager` .

    ![Screenshot do portal Azure com duas chaves LUIS no grupo de recursos luis-gerente de tráfego](./media/traffic-manager/luis-keys.png)

2. No site da [LUIS,][LUIS] na secção **Gerir,** na página **Azure Resources,** atribua as chaves à aplicação e republica a aplicação selecionando o botão **Publicar** no menu superior direito.

    O URL de exemplo na coluna **de ponto final** utiliza um pedido GET com a chave de ponto final como parâmetro de consulta. Copie os URLs do ponto final das duas novas chaves. São utilizados como parte da configuração do Gestor de Tráfego mais tarde neste artigo.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Gerir pedidos de ponto final da LUIS através de chaves com o Gestor de Tráfego
O Traffic Manager cria um novo ponto de acesso ao DNS para os seus pontos finais. Não funciona como uma porta de entrada ou procuração, mas estritamente ao nível do DNS. Este exemplo não altera nenhum registo de DNS. Utiliza uma biblioteca DNS para comunicar com o Traffic Manager para obter o ponto final correto para esse pedido específico. _Cada_ pedido destinado à LUIS requer primeiro um pedido de Gerente de Tráfego para determinar qual o ponto final da LUIS a utilizar.

### <a name="polling-uses-luis-endpoint"></a>Sondagem usa ponto final do LUIS
O Gestor de Tráfego sonda periodicamente os pontos finais para garantir que o ponto final ainda está disponível. O URL do Traffic Manager sondado precisa de ser acessível com um pedido GET e devolver um 200. O URL de ponto final na página **publicar** faz isto. Uma vez que cada chave de ponto final tem uma rota diferente e parâmetros de cadeia de consulta, cada chave de ponto final precisa de um caminho de votação diferente. Cada vez que o Gerente de Tráfego faz sondagens, custa um pedido de quota. O parâmetro de cadeia de consulta **q** do ponto final LUIS é a expressão enviada para LUIS. Este parâmetro, em vez de enviar uma expressão, é usado para adicionar sondagens do Traffic Manager ao registo de ponto final LUIS como uma técnica de depuração enquanto configura o Traffic Manager.

Como cada ponto final da LUIS precisa do seu próprio caminho, precisa do seu próprio perfil de Gestor de Tráfego. Para gerir através de perfis, crie uma arquitetura [ _aninhada_ de Gestor de Tráfego.](../../traffic-manager/traffic-manager-nested-profiles.md) Um perfil dos pais aponta para os perfis das crianças e gere o tráfego através deles.

Uma vez configurado o Gestor de Tráfego, lembre-se de alterar o caminho para utilizar o parâmetro de cadeia de consulta de registo=falso para que o seu registo não esteja a encher-se de sondagens.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Configure gestor de tráfego com perfis aninhados
As secções seguintes criam dois perfis infantis, um para a chave East LUIS e outro para a chave West LUIS. Em seguida, um perfil dos pais é criado e os dois perfis da criança são adicionados ao perfil dos pais.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Crie o perfil de Gestor de Tráfego dos EUA com PowerShell
Para criar o perfil de Gerente de Tráfego dos EUA, existem vários passos: criar perfil, adicionar ponto final e definir ponto final. Um perfil de Gestor de Tráfego pode ter muitos pontos finais, mas cada ponto final tem o mesmo caminho de validação. Como os URLs de ponta LUIS para as assinaturas leste e oeste são diferentes devido à chave de região e ponto final, cada ponto final LUIS tem de ser um único ponto final no perfil.

1. Criar perfil com o novo cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Utilize o cmdlet seguinte para criar o perfil. Certifique-se de mudar o `appIdLuis` e `subscriptionKeyLuis` . . A subscriçãoKey é para a chave East US LUIS. Se o caminho não estiver correto, incluindo a iD da aplicação LUIS e a chave de ponta final, a sondagem do Traffic Manager é um estado de `degraded` porque a Traffic Manage não pode solicitar com sucesso o ponto final do LUIS. Certifique-se de que o valor `q` é para que possa ver este valor nos `traffic-manager-east` registos do ponto final DO LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-eastus|Nome do Gestor de Tráfego no portal Azure|
    |-Nome do Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [os métodos de encaminhamento do Gestor de Tráfego.][routing-methods] Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo|luis-dns-eastus|Este é o subdomínio para o serviço: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Intervalo de votação, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |- MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Substitua `<appIdLuis>` e por os seus `<subscriptionKeyLuis>` próprios valores.|

    Um pedido bem sucedido não tem resposta.

2. Adicione o ponto final dos EUA com **[o Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|Nome do ponto final exibido no perfil|
    |-TrafficManagerProfile|$eastprofile|Use objeto de perfil criado no passo 1|
    |-Tipo|Pontos de final externos|Para mais informações, consulte [o ponto final do Gestor de Tráfego][traffic-manager-endpoints] |
    |-Alvo|eastus.api.cognitive.microsoft.com|Este é o domínio para o ponto final LUIS.|
    |-EndpointLocação|"Eastus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando for criado|

    A resposta bem sucedida parece:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Definir ponto final leste dos EUA com **[Set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Uma resposta bem sucedida será a mesma resposta que o passo 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Crie o perfil de Gestor de Tráfego dos EUA com PowerShell
Para criar o perfil de Gestor de Tráfego dos EUA Ocidentais, siga os mesmos passos: crie perfil, adicione ponto final e estabeleça o ponto final.

1. Criar perfil com o novo cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Utilize o cmdlet seguinte para criar o perfil. Certifique-se de mudar o `appIdLuis` e `subscriptionKeyLuis` . . A subscriçãoKey é para a chave East US LUIS. Se o caminho não estiver correto, incluindo a iD da aplicação LUIS e a chave de ponta final, a sondagem do Traffic Manager é um estado de `degraded` porque a Traffic Manage não pode solicitar com sucesso o ponto final do LUIS. Certifique-se de que o valor `q` é para que possa ver este valor nos `traffic-manager-west` registos do ponto final DO LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-westus|Nome do Gestor de Tráfego no portal Azure|
    |-Nome do Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [os métodos de encaminhamento do Gestor de Tráfego.][routing-methods] Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo|luis-dns-westus|Este é o subdomínio para o serviço: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Intervalo de votação, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |- MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Substitua `<appId>` e por os seus `<subscriptionKey>` próprios valores. Lembre-se que esta chave de ponto final é diferente da chave de ponta leste|

    Um pedido bem sucedido não tem resposta.

2. Adicione o ponto final dos EUA com **[o add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|Nome do ponto final exibido no perfil|
    |-TrafficManagerProfile|$westprofile|Use objeto de perfil criado no passo 1|
    |-Tipo|Pontos de final externos|Para mais informações, consulte [o ponto final do Gestor de Tráfego][traffic-manager-endpoints] |
    |-Alvo|westus.api.cognitive.microsoft.com|Este é o domínio para o ponto final LUIS.|
    |-EndpointLocação|"Westus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando for criado|

    A resposta bem sucedida parece:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Definir ponto final dos EUA com **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Uma resposta bem sucedida é a mesma resposta que o passo 2.

### <a name="create-parent-traffic-manager-profile"></a>Criar perfil de Gestor de Tráfego parental
Crie o perfil do Gestor de Tráfego parental e ligue dois perfis de Gerente de Tráfego infantil ao progenitor.

1. Crie o perfil dos pais com **[o cmdlet New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-pai|Nome do Gestor de Tráfego no portal Azure|
    |-Nome do Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [os métodos de encaminhamento do Gestor de Tráfego.][routing-methods] Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo|luis-dns-pai|Este é o subdomínio para o serviço: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Intervalo de votação, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |- MonitorPath|`/`|Este caminho não importa porque os caminhos do ponto final da criança são usados em vez disso.|

    Um pedido bem sucedido não tem resposta.

2. Adicione o perfil infantil do Leste dos EUA aos pais com o tipo **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|criança-endpoint-useast|Perfil leste|
    |-TrafficManagerProfile|$parentprofile|Perfil para atribuir este ponto final a|
    |-Tipo|NestedEndpoints|Para mais informações, consulte [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. ID|ID do perfil da criança|
    |-EndpointStatus|Ativado|Estado do ponto final após a adição ao progenitor|
    |-EndpointLocação|"Eastus"|Nome de recurso da [região](https://azure.microsoft.com/global-infrastructure/regions/) azul|
    |-MinChildEndpoints|1|Número mínimo para os pontos finais da criança|

    A resposta bem sucedida parece-se com o seguinte e inclui o novo `child-endpoint-useast` ponto final:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Adicione o perfil infantil dos EUA ao pai com o tipo **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet e **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|criança-ponta-uswest|Perfil oeste|
    |-TrafficManagerProfile|$parentprofile|Perfil para atribuir este ponto final a|
    |-Tipo|NestedEndpoints|Para mais informações, consulte [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. ID|ID do perfil da criança|
    |-EndpointStatus|Ativado|Estado do ponto final após a adição ao progenitor|
    |-EndpointLocação|"Westus"|Nome de recurso da [região](https://azure.microsoft.com/global-infrastructure/regions/) azul|
    |-MinChildEndpoints|1|Número mínimo para os pontos finais da criança|

    A resposta bem sucedida parece e inclui tanto o ponto final anterior como `child-endpoint-useast` o novo `child-endpoint-uswest` ponto final:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Definir pontos finais com **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Uma resposta bem sucedida é a mesma resposta que o passo 3.

### <a name="powershell-variables"></a>Variáveis PowerShell
Nas secções anteriores, foram criadas três variáveis PowerShell: `$eastprofile` `$westprofile` . . `$parentprofile` Estas variáveis são utilizadas no final da configuração do Gestor de Tráfego. Se optar por não criar as variáveis, ou se esqueceu, ou os tempos de janela do PowerShell, pode utilizar o cmdlet PowerShell, **[Get-AzTrafficManagerProfile,](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** para obter o perfil novamente e atribuí-lo a uma variável.

Substitua os itens em suportes `<>` angulares, com os valores corretos para cada um dos três perfis de que necessita.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifique os trabalhos do Gestor de Tráfego
Para verificar se os perfis do Gestor de Tráfego funcionam, os perfis precisam de ter o estado `Online` deste estado baseia-se na trajetória de votação do ponto final.

### <a name="view-new-profiles-in-the-azure-portal"></a>Ver novos perfis no portal Azure
Pode verificar se os três perfis são criados olhando para os recursos do `luis-traffic-manager` grupo de recursos.

![Screenshot do grupo de recursos Azure luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verifique se o estado do perfil está Online
O Gestor de Tráfego pesquisa o caminho de cada ponto final para se certificar de que está online. Se estiver online, o estado dos perfis da criança é `Online` . Isto é apresentado na **visão geral** de cada perfil.

![Screenshot do perfil do gestor de tráfego Azure mostrando o estado do monitor de online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validar trabalhos de sondagens do Gestor de Tráfego
Outra forma de validar os trabalhos de sondagens do gestor de tráfego é com os registos do ponto final da LUIS. Na página da lista de aplicações do site [LUIS,][LUIS] exporte o registo de ponto final para a aplicação. Como as sondagens do Traffic Manager muitas vezes para os dois pontos finais, há entradas nos registos, mesmo que tenham estado apenas em poucos minutos. Lembre-se de procurar entradas onde a consulta começa `traffic-manager-` com .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Validar resposta ao DNS das obras do Traffic Manager
Para validar que a resposta do DNS devolve um ponto final LUIS, solicite ao DNS de gestão de tráfego através de uma biblioteca de clientes DNS. O nome DNS para o perfil dos pais é `luis-dns-parent.trafficmanager.net` .

O seguinte código Node.js faz um pedido para o perfil dos pais e devolve um ponto final LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A resposta bem sucedida com o ponto final do LUIS é:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Utilize o perfil dos pais do Gestor de Tráfego
Para gerir o tráfego através dos pontos finais, é necessário inserir uma chamada para o DNS do Gestor de Tráfego para encontrar o ponto final do LUIS. Esta chamada é feita para cada pedido de ponto final DA LUIS e precisa de simular a localização geográfica do utilizador da aplicação do cliente LUIS. Adicione o código de resposta DNS entre a sua aplicação de cliente LUIS e o pedido à LUIS para a previsão do ponto final.

## <a name="resolving-a-degraded-state"></a>Resolução de um estado degradado

Ativar [os registos de diagnóstico](../../traffic-manager/traffic-manager-diagnostic-logs.md) do Gestor de Tráfego para ver por que razão o estado do ponto final está degradado.

## <a name="clean-up"></a>Limpeza
Remova as duas chaves de ponto final LUIS, os três perfis do Gestor de Tráfego, e o grupo de recursos que continha estes cinco recursos. Isto é feito a partir do portal Azure. Apaga-se os cinco recursos da lista de recursos. Em seguida, apague o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

Reveja as opções [de middleware](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) no BotFramework v4 para entender como este código de gestão de tráfego pode ser adicionado a um botFramework.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md