---
title: Aumentar quota de ponto final - LUIS
titleSuffix: Azure Cognitive Services
description: A Compreensão da Linguagem (LUIS) oferece a capacidade de aumentar a quota de pedido de ponto final para além da quota de uma única chave. Isto é feito através da criação de mais chaves para a LUIS e adicionando-as à aplicação DONA na página **Publicar** na secção **Recursos e Chaves.**
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "70256596"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Utilize o Microsoft Azure Traffic Manager para gerir a quota de ponto final através das teclas
A Compreensão da Linguagem (LUIS) oferece a capacidade de aumentar a quota de pedido de ponto final para além da quota de uma única chave. Isto é feito através da criação de mais chaves para a LUIS e adicionando-as à aplicação DONA na página **Publicar** na secção **Recursos e Chaves.** 

A aplicação do cliente tem de gerir o tráfego através das chaves. Luis não faz isto. 

Este artigo explica como gerir o tráfego através das chaves com o [Azure Traffic Manager.][traffic-manager-marketing] Já deve ter uma app LUIS treinada e publicada. Se não tiver um, siga o domínio pré-construído [rapidamente.](luis-get-started-create-app.md) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ligue-se à PowerShell no portal Azure
No portal [Azure,][azure-portal] abra a janela PowerShell. O ícone da janela PowerShell é o>_ na barra **de** navegação superior. Ao utilizar o PowerShell a partir do portal, obtém-se a versão powerShell mais recente e é autenticado. A PowerShell no portal requer uma conta [de Armazenamento Azure.](https://azure.microsoft.com/services/storage/) 

![Screenshot do portal Azure com janela Powershell aberta](./media/traffic-manager/azure-portal-powershell.png)

As seguintes secções utilizam [cmdlets PowerShell do Gestor de Tráfego](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Criar grupo de recursos Azure com powerShell
Antes de criar os recursos Azure, crie um grupo de recursos para conter todos os recursos. Nomeie `luis-traffic-manager` o grupo de `West US`recursos e use a região é . A região do grupo de recursos armazena metadados sobre o grupo. Não vai abrandar os seus recursos se estiverem em outra região. 

Criar grupo de recursos com **[cmdlet New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Criar chaves LUIS para aumentar quota total de ponto final
1. No portal Azure, crie duas teclas `West US` **de Compreensão linguística,** uma na e outra na `East US`. Utilize o grupo de recursos existente, criado `luis-traffic-manager`na secção anterior, denominado . 

    ![Screenshot do portal Azure com duas chaves LUIS no grupo de recursos do gestor de tráfego luis-tráfego](./media/traffic-manager/luis-keys.png)

2. No site da [LUIS,][LUIS] na secção **Gerir,** na página **De recursos azure,** atribuir chaves à app e reeditar a aplicação selecionando o botão **Publicar** no menu superior direito. 

    O URL de exemplo na coluna **de ponto final** utiliza um pedido GET com a chave de ponto final como parâmetro de consulta. Copie os URLs finais das duas chaves novas. São utilizados como parte da configuração do Gestor de Tráfego mais tarde neste artigo.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Gerir pedidos de ponto final da LUIS através de chaves com Gestor de Tráfego
Traffic Manager cria um novo ponto de acesso DNS para os seus pontos finais. Não funciona como um portal ou procuração, mas estritamente ao nível do DNS. Este exemplo não altera nenhum registo dNS. Usa uma biblioteca DNS para comunicar com o Traffic Manager para obter o ponto final correto para esse pedido específico. _Cada_ pedido destinado à LUIS requer primeiro um pedido do Gestor de Tráfego para determinar qual o ponto final da LUIS a utilizar. 

### <a name="polling-uses-luis-endpoint"></a>Sondagens usam ponto final luis
Traffic Manager sondagem os pontos finais periodicamente para garantir que o ponto final ainda está disponível. O URL do Gestor de Tráfego inquirido precisa de ser acessível com um pedido GET e devolver um 200. O URL de ponto final na página **Publicar** faz isto. Uma vez que cada chave de ponto final tem uma rota diferente e parâmetros de corda de consulta, cada chave de ponto final precisa de um caminho de votação diferente. Cada vez que o Traffic Manager vota, custa um pedido de quota. O parâmetro de corda de consulta **q** do ponto final luis é a expressão enviada para LUIS. Este parâmetro, em vez de enviar uma expressão, é usado para adicionar sondagens do Gestor de Tráfego ao registo final do LUIS como uma técnica de depuração enquanto se configura o Gestor de Tráfego.

Como cada ponto final luis precisa do seu próprio caminho, precisa do seu próprio perfil de Gestor de Tráfego. Para gerir através de perfis, crie uma arquitetura [ _aninhada_ do Traffic Manager.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) Um perfil dos pais aponta para os perfis das crianças e gere o tráfego através deles.

Uma vez configurado o Gestor de Tráfego, lembre-se de alterar o caminho para utilizar o parâmetro de corda de consulta falsa para que o seu registo não esteja a encher-se de sondagens.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Configure Traffic Manager com perfis aninhados
As seguintes secções criam dois perfis infantis, um para a chave EAST LUIS e outro para a chave West LUIS. Em seguida, é criado um perfil dos pais e os dois perfis infantis são adicionados ao perfil dos pais. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Crie o perfil do Gestor de Tráfego dos EUA com a PowerShell
Para criar o perfil do East US Traffic Manager, existem vários passos: criar perfil, adicionar ponto final e definir ponto final. Um perfil do Gestor de Tráfego pode ter muitos pontos finais, mas cada ponto final tem o mesmo caminho de validação. Como os URLs de ponto final LUIS para as assinaturas leste e oeste são diferentes devido à chave da região e do ponto final, cada ponto final luis tem que ser um ponto final único no perfil. 

1. Criar perfil com **[new-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet

    Utilize o seguinte cmdlet para criar o perfil. Certifique-se de `appIdLuis` `subscriptionKeyLuis`mudar o e . A chave de subscrição é para a chave LUIS dos EUA Orientais. Se o caminho não estiver correto, incluindo o ID da app LUIS `degraded` e a chave endpoint, a sondagem do Gestor de Tráfego é um estado de porque a Gestão de Tráfego não pode solicitar com sucesso o ponto final do LUIS. Certifique-se de `q` `traffic-manager-east` que o valor é para que possa ver este valor nos registos finais do LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-eastus|Nome do Gestor de Tráfego no portal Azure|
    |-Nome de Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-Método de Atropelamento|Desempenho|Para mais informações, consulte os métodos de [encaminhamento do Gestor de Tráfego][routing-methods]. Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo dister|luis-dns-eastus|Este é o subdomínio do serviço: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Intervalo de sondagens, 30 segundos|
    |-MonitorProtocolo<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|`<appIdLuis>` Substitua `<subscriptionKeyLuis>` e com os seus próprios valores.|
    
    Um pedido bem sucedido não tem resposta.

2. Adicione o ponto final dos EUA leste com **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-EndpointName|luis-leste-endpoint|Nome final exibido sob o perfil|
    |-Perfil de Gestor de Tráfego|$eastprofile|Use objeto de perfil criado no passo 1|
    |-Tipo|Pontos Final externos|Para mais informações, consulte o [ponto final do Gestor de Tráfego][traffic-manager-endpoints] |
    |-Alvo|eastus.api.cognitive.microsoft.com|Este é o domínio para o ponto final do LUIS.|
    |-EndpointLocation|"Eastus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando é criado|

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

3. Set East US endpoint com **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Uma resposta bem sucedida será a mesma resposta que o passo 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Crie o perfil do West US Traffic Manager com a PowerShell
Para criar o perfil do West US Traffic Manager, siga os mesmos passos: criar perfil, adicionar ponto final e definir ponto final.

1. Criar perfil com **[new-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    Utilize o seguinte cmdlet para criar o perfil. Certifique-se de `appIdLuis` `subscriptionKeyLuis`mudar o e . A chave de subscrição é para a chave LUIS dos EUA Orientais. Se o caminho não estiver correto, incluindo o ID da app `degraded` LUIS e a chave endpoint, a sondagem do Gestor de Tráfego é um estado de porque a Gestão de Tráfego não pode solicitar com sucesso o ponto final da LUIS. Certifique-se de `q` `traffic-manager-west` que o valor é para que possa ver este valor nos registos finais do LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-westus|Nome do Gestor de Tráfego no portal Azure|
    |-Nome de Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-Método de Atropelamento|Desempenho|Para mais informações, consulte os métodos de [encaminhamento do Gestor de Tráfego][routing-methods]. Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo dister|luis-dns-westus|Este é o subdomínio do serviço: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Intervalo de sondagens, 30 segundos|
    |-MonitorProtocolo<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|`<appId>` Substitua `<subscriptionKey>` e com os seus próprios valores. Lembre-se que esta chave de ponto final é diferente da chave final do ponto final leste|
    
    Um pedido bem sucedido não tem resposta.

2. Adicione o ponto final dos EUA ocidental com **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|Nome final exibido sob o perfil|
    |-Perfil de Gestor de Tráfego|$westprofile|Use objeto de perfil criado no passo 1|
    |-Tipo|Pontos Final externos|Para mais informações, consulte o [ponto final do Gestor de Tráfego][traffic-manager-endpoints] |
    |-Alvo|westus.api.cognitive.microsoft.com|Este é o domínio para o ponto final do LUIS.|
    |-EndpointLocation|"Westus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando é criado|

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

3. Definir ponto final dos EUA com **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Uma resposta bem sucedida é a mesma resposta que o passo 2.

### <a name="create-parent-traffic-manager-profile"></a>Criar o perfil do Gestor de Tráfego dos Pais
Crie o perfil do Gestor de Tráfego parental e ligue dois perfis do Gestor de Tráfego infantil ao progenitor.

1. Criar o perfil dos pais com **[o Cmdlet New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-Name|luis-perfil-pai|Nome do Gestor de Tráfego no portal Azure|
    |-Nome de Grupo de Recursos|luis-traffic-manager|Criado em secção anterior|
    |-Método de Atropelamento|Desempenho|Para mais informações, consulte os métodos de [encaminhamento do Gestor de Tráfego][routing-methods]. Se utilizar o desempenho, o pedido de URL ao Gestor de Tráfego deve vir da região do utilizador. Se passar por um chatbot ou outra aplicação, é da responsabilidade do chatbot imitar a região na chamada para o Gestor de Tráfego. |
    |-Nome relativo dister|luis-dns-pai|Este é o subdomínio do serviço: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Intervalo de sondagens, 30 segundos|
    |-MonitorProtocolo<BR>-MonitorPort|HTTPS<br>443|Porto e protocolo para LUIS é HTTPS/443|
    |-MonitorPath|`/`|Este caminho não importa porque os caminhos finais da criança são usados em vez disso.|

    Um pedido bem sucedido não tem resposta.

2. Adicione o perfil infantil do Leste dos EUA ao progenitor com o tipo **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-EndpointName|criança-endpoint-useast|Perfil leste|
    |-Perfil de Gestor de Tráfego|$parentprofile|Perfil para atribuir este ponto final a|
    |-Tipo|Pontos Aninhados|Para mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. Id|ID do perfil da criança|
    |-EndpointStatus|Ativado|Estado do ponto final após adicionar ao progenitor|
    |-EndpointLocation|"Eastus"|Nome de recurso da [região de Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Número mínimo para pontos finais para crianças|

    A resposta bem sucedida parece a `child-endpoint-useast` seguinte e inclui o novo ponto final:    

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

3. Adicione o perfil infantil do Oeste dos EUA ao progenitor com **[add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet e **nestedEndpoints** tipo

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou Valor Variável|Objetivo|
    |--|--|--|
    |-EndpointName|criança-endpoint-uswest|Perfil ocidental|
    |-Perfil de Gestor de Tráfego|$parentprofile|Perfil para atribuir este ponto final a|
    |-Tipo|Pontos Aninhados|Para mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. Id|ID do perfil da criança|
    |-EndpointStatus|Ativado|Estado do ponto final após adicionar ao progenitor|
    |-EndpointLocation|"Westus"|Nome de recurso da [região de Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Número mínimo para pontos finais para crianças|

    A resposta bem sucedida parece `child-endpoint-useast` e inclui tanto `child-endpoint-uswest` o ponto final anterior como o novo ponto final:

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

4. Definir pontos finais com **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Uma resposta bem sucedida é a mesma resposta que o passo 3.

### <a name="powershell-variables"></a>Variáveis PowerShell
Nas secções anteriores, foram criadas `$eastprofile`três `$westprofile` `$parentprofile`variáveis PowerShell: . . Estas variáveis são usadas no final da configuração do Gestor de Tráfego. Se optou por não criar as variáveis, ou esqueceu-se, ou se a janela powerShell passar, pode utilizar o cmdlet PowerShell, **[Get-AzTrafficManagerProfile,](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** para obter o perfil novamente e atribuí-lo a uma variável. 

Substitua os itens em suportes angulares, `<>`com os valores corretos para cada um dos três perfis de que necessita. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verificar obras do Gestor de Tráfego
Para verificar se os perfis do Gestor de Tráfego funcionam, os perfis precisam de ter o estatuto `Online` deste estado com base na via de sondagens do ponto final. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Ver novos perfis no portal Azure
Pode verificar se os três perfis são criados `luis-traffic-manager` olhando para os recursos do grupo de recursos.

![Screenshot do grupo de recursos Azure luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verifique se o estado do perfil está online
O Gestor de Tráfego faz sondagens no caminho de cada ponto final para garantir que está online. Se estiver online, o estado dos `Online`perfis da criança é . Isto é apresentado na **visão geral** de cada perfil. 

![Screenshot do perfil do Gestor de Tráfego Azure visão geral mostrando o estado do monitor do online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validar as sondagens do Gestor de Tráfego
Outra forma de validar as sondagens do gestor de tráfego é com os registos do ponto final do LUIS. Na página da lista de aplicações do site da [LUIS,][LUIS] exporte o registo final para a aplicação. Como o Traffic Manager sondagens frequentemente para os dois pontos finais, há entradas nos registos mesmo que tenham estado apenas em alguns minutos. Lembre-se de procurar entradas onde `traffic-manager-`a consulta começa com .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Validar a resposta do DNS das obras do Gestor de Tráfego
Para validar que a resposta do DNS devolve um ponto final luis, solicite o perfil de gestão de tráfego DNS usando uma biblioteca de clientes DNS. O nome DNS para `luis-dns-parent.trafficmanager.net`o perfil dos pais é .

O seguinte código Node.js faz um pedido para o perfil dos pais e devolve um ponto final luis:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A resposta bem sucedida com o ponto final da LUIS é:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Utilize o perfil dos pais do Gestor de Tráfego
Para gerir o tráfego em pontos finais, é necessário inserir uma chamada ao DNS do Gestor de Tráfego para encontrar o ponto final do LUIS. Esta chamada é feita para cada pedido de ponto final da LUIS e precisa de simular a localização geográfica do utilizador da aplicação cliente LUIS. Adicione o código de resposta dNS entre a sua aplicação de cliente LUIS e o pedido à LUIS para a previsão do ponto final. 

## <a name="resolving-a-degraded-state"></a>Resolver um estado degradado

Ative [os registos](../../traffic-manager/traffic-manager-diagnostic-logs.md) de diagnóstico para o Gestor de Tráfego para ver por que razão o estado do ponto final está degradado.

## <a name="clean-up"></a>Limpeza
Remova as duas chaves de ponto final luis, os três perfis do Gestor de Tráfego, e o grupo de recursos que continha estes cinco recursos. Isto é feito a partir do portal Azure. Elimina os cinco recursos da lista de recursos. Em seguida, apague o grupo de recursos. 

## <a name="next-steps"></a>Passos seguintes

Reveja as opções [de middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) no BotFramework v4 para entender como este código de gestão de tráfego pode ser adicionado a um bot BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
