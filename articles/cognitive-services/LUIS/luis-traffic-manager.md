---
title: Aumentar a quota de ponto final
titleSuffix: Azure Cognitive Services
description: Compreensão de idiomas (LUIS) oferece a capacidade para aumentar a quota de pedido de ponto de extremidade para além de quota de uma chave única. Isso é feito através da criação de mais chaves para LUIS e adicioná-los para a aplicação do LUIS no **Publish** página no **recursos e as chaves** secção.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.openlocfilehash: a71b09ba8b3e7fa7299c34c3cdc64503ae4e9857
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736554"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Utilizar o Gestor de tráfego do Microsoft Azure para gerir a quota de ponto final através de chaves
Compreensão de idiomas (LUIS) oferece a capacidade para aumentar a quota de pedido de ponto de extremidade para além de quota de uma chave única. Isso é feito através da criação de mais chaves para LUIS e adicioná-los para a aplicação do LUIS no **Publish** página no **recursos e as chaves** secção. 

A aplicação cliente tem de gerir o tráfego entre as chaves. LUIS não faz isso. 

Este artigo explica como gerir o tráfego entre chaves com o Azure [Gestor de tráfego][traffic-manager-marketing]. Já tem de ter uma aplicação de LUIS treinada e publicada. Se não tiver um, siga o domínio pré-criado [guia de introdução](luis-get-started-create-app.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ligar ao PowerShell, no portal do Azure
Na [Azure] [ azure-portal] portal, abra a janela do PowerShell. O ícone para a janela do PowerShell é o **> _** na barra de navegação superior. Com o PowerShell a partir do portal, obtém a versão mais recente do PowerShell e está autenticado. O PowerShell no portal do requer uma [armazenamento do Azure](https://azure.microsoft.com/services/storage/) conta. 

![Abra a captura de ecrã do portal do Azure com a janela do Powershell](./media/traffic-manager/azure-portal-powershell.png)

As secções seguintes utilizam [cmdlets do PowerShell do Gestor de tráfego](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Criar grupo de recursos do Azure com o PowerShell
Antes de criar os recursos do Azure, crie um grupo de recursos para conter todos os recursos. Nome do grupo de recursos `luis-traffic-manager` e utilize a região é `West US`. A região do grupo de recursos armazena metadados sobre o grupo. Ele não abrandar os recursos que estejam noutra região. 

Criar grupo de recursos com **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Criar chaves para aumentar a quota do total de ponto de extremidade de LUIS
1. No portal do Azure, criar dois **compreensão de idiomas** chaves, uma no `West US` e um no `East US`. Utilizar o grupo de recursos existente, criado na secção anterior, com o nome `luis-traffic-manager`. 

    ![Captura de ecrã do portal do Azure com duas chaves de LUIS no grupo de recursos do Gestor de tráfego de luis](./media/traffic-manager/luis-keys.png)

2. Na [LUIS] [ LUIS] Web site, no **gerir** na secção o **chaves e os pontos finais** página, atribuir chaves à aplicação e voltar a publicar a aplicação por Selecionar o **publicar** botão no menu superior direito. 

    O URL de exemplo na **ponto final** coluna usa um pedido GET com a chave de ponto de extremidade como um parâmetro de consulta. Copie o URL de ponto final de duas novas chaves. São utilizados como parte da configuração do Gestor de tráfego neste artigo.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Gerir pedidos de ponto final de LUIS entre chaves com o Gestor de tráfego
O Gestor de tráfego cria um novo ponto de acesso DNS para os pontos finais. Não atua como um gateway ou proxy, mas estritamente ao nível do DNS. Neste exemplo não altera os registos DNS. Ele usa uma biblioteca DNS para comunicar com o Gestor de tráfego para obter o ponto final correto para essa solicitação específica. _Cada_ pedido voltado para o LUIS primeiro exige um pedido de Gestor de tráfego para determinar qual ponto de extremidade do LUIS para utilizar. 

### <a name="polling-uses-luis-endpoint"></a>Consulta utiliza o ponto final de LUIS
O Gestor de tráfego consulta os pontos de extremidade periodicamente para se certificar de que o ponto final está ainda disponível. O URL do Gestor de tráfego consultados tem de estar acessíveis com um pedido GET e retornar um 200. O URL do ponto final no **publicar** página faz isso. Uma vez que cada chave de ponto final tem uma rota diferente e parâmetros de cadeia de caracteres de consulta, cada chave de ponto final tem um caminho de consulta diferentes. Sempre que consulta o Gestor de tráfego, custo um pedido de quota. O parâmetro de cadeia de caracteres de consulta **p** do LUIS ponto final é a expressão enviado para o LUIS. Este parâmetro, em vez de enviar uma expressão, é utilizado para adicionar consulta do Gestor de tráfego para o registo do ponto final de LUIS como uma técnica de depuração ao obter o Gestor de tráfego configurado.

Uma vez que cada ponto de extremidade do LUIS tem seu próprio caminho, ele precisa de seu próprio perfil do Gestor de tráfego. Para gerir através de perfis, criar um [ _aninhada_ Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) arquitetura. Um perfil de principal aponta para os perfis de filhos e gerir o tráfego por elas.

Quando o Gestor de tráfego estiver configurado, não se esqueça de alterar o caminho a utilizar o registo de = o parâmetro de cadeia de caracteres de consulta false, para não ser o início de sessão é preenchida com a consulta.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Configurar o Gestor de tráfego com perfis aninhados
As secções seguintes criar dois perfis de subordinado, uma para a chave de LUIS leste e outro para a chave de LUIS oeste. Em seguida, é criado um perfil de principal e os perfis de dois subordinados são adicionados ao perfil de principal. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Criar o perfil do Gestor de tráfego do E.U.A. leste, com o PowerShell
Para criar o perfil do Gestor de tráfego do E.U.A. leste, existem vários passos: criar o perfil, adicionar ponto final e definir o ponto final. Um perfil do Gestor de tráfego pode ter muitos pontos de extremidade, mas cada ponto de extremidade tem o mesmo caminho de validação. Uma vez que os URLs de ponto final de LUIS para as subscrições orientais e ocidentais forem diferentes devido a chave de região e o ponto final, cada ponto de extremidade do LUIS tem de ser um único ponto final no perfil. 

1. Criar perfil com **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet

    Utilize o cmdlet seguinte para criar o perfil. Certifique-se de alterar o `appIdLuis` e `subscriptionKeyLuis`. O subscriptionKey destina-se a chave de LUIS do Leste-nos. Se o caminho é não está correto, incluindo o LUIS ponto final de ID e chave da aplicação, a consulta do Gestor de tráfego é um Estado de `degraded` porque não é possível gerir o tráfego de pedir com êxito o ponto de extremidade do LUIS. Certificar-se de que o valor de `q` é `traffic-manager-east` para que possa ver este valor nos registos de ponto final do LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-Nome|Luis-perfil-eastus|Nome do Gestor de tráfego no portal do Azure|
    |-ResourceGroupName|Gestor de tráfego de Luis|Criado na secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [métodos de encaminhamento do Gestor de tráfego][routing-methods]. Se utilizar o desempenho, a solicitação de URL para o Gestor de tráfego deverão ser provenientes da região do utilizador. Se passar por um chatbot ou outro aplicativo, é responsabilidade do chatbot para imitar a região na chamada para o Gestor de tráfego. |
    |-RelativeDnsName|Luis-dns-eastus|Este é o subdomínio para o serviço: luis-dns-eastus.trafficmanager.net|
    |-O valor de Ttl|30|Intervalo de consulta, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Portas e protocolos para LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Substitua <appIdLuis> e <subscriptionKeyLuis> pelos seus próprios valores.|
    
    Um pedido com êxito tem sem resposta.

2. Adicionar ponto final de E.U.A. Leste com **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|Luis-Leste-ponto final|Nome do ponto final apresentado sob o perfil|
    |-TrafficManagerProfile|$eastprofile|Utilizar o objeto de perfil que criou no passo 1|
    |-Type|ExternalEndpoints|Para obter mais informações, consulte [ponto final do Gestor de tráfego][traffic-manager-endpoints] |
    |-Destino|eastus.API.cognitive.microsoft.com|Este é o domínio para o ponto de final do LUIS.|
    |-EndpointLocation|"eastus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando é criado|

    A resposta bem-sucedida é semelhante a:

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

3. Definir o ponto final de E.U.A. Leste com **[conjunto AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Uma resposta com êxito será a mesma resposta como passo 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Criar o perfil do Gestor de tráfego do Oeste dos E.U.A. com o PowerShell
Para criar o perfil do Gestor de tráfego do Oeste dos E.U.A., siga os mesmos passos: criar o perfil, adicionar ponto final e definir o ponto final.

1. Criar perfil com **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    Utilize o cmdlet seguinte para criar o perfil. Certifique-se de alterar o `appIdLuis` e `subscriptionKeyLuis`. O subscriptionKey destina-se a chave de LUIS do Leste-nos. Se o caminho não estiver correto, incluindo a chave de ID e o ponto final de aplicação do LUIS, a consulta do Gestor de tráfego é um Estado de `degraded` porque não é possível gerir o tráfego de pedir com êxito o ponto de extremidade do LUIS. Certificar-se de que o valor de `q` é `traffic-manager-west` para que possa ver este valor nos registos de ponto final do LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-Nome|Luis-perfil-westus|Nome do Gestor de tráfego no portal do Azure|
    |-ResourceGroupName|Gestor de tráfego de Luis|Criado na secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [métodos de encaminhamento do Gestor de tráfego][routing-methods]. Se utilizar o desempenho, a solicitação de URL para o Gestor de tráfego deverão ser provenientes da região do utilizador. Se passar por um chatbot ou outro aplicativo, é responsabilidade do chatbot para imitar a região na chamada para o Gestor de tráfego. |
    |-RelativeDnsName|Luis-dns-westus|Este é o subdomínio para o serviço: luis-dns-westus.trafficmanager.net|
    |-O valor de Ttl|30|Intervalo de consulta, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Portas e protocolos para LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Substitua <appId> e <subscriptionKey> pelos seus próprios valores. Lembre-se de que esta chave de ponto final é diferente da chave do ponto final de Leste|
    
    Um pedido com êxito tem sem resposta.

2. Adicionar ponto final do Oeste dos E.U.A. com **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|Luis-oeste-ponto final|Nome do ponto final apresentado sob o perfil|
    |-TrafficManagerProfile|$westprofile|Utilizar o objeto de perfil que criou no passo 1|
    |-Type|ExternalEndpoints|Para obter mais informações, consulte [ponto final do Gestor de tráfego][traffic-manager-endpoints] |
    |-Destino|westus.API.cognitive.microsoft.com|Este é o domínio para o ponto de final do LUIS.|
    |-EndpointLocation|"westus"|Região do ponto final|
    |-EndpointStatus|Ativado|Ativar o ponto final quando é criado|

    A resposta bem-sucedida é semelhante a:

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

3. Definir o ponto final do Oeste dos E.U.A. com **[conjunto AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Uma resposta com êxito é a mesma resposta como passo 2.

### <a name="create-parent-traffic-manager-profile"></a>Criar perfil do Gestor de tráfego de principal
Criar o principal de perfil do Traffic Manager e ligar dois perfis de Gestor de tráfego subordinado para o elemento principal.

1. Criar perfil de principal com **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-Nome|Luis-perfil-principal|Nome do Gestor de tráfego no portal do Azure|
    |-ResourceGroupName|Gestor de tráfego de Luis|Criado na secção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [métodos de encaminhamento do Gestor de tráfego][routing-methods]. Se utilizar o desempenho, a solicitação de URL para o Gestor de tráfego deverão ser provenientes da região do utilizador. Se passar por um chatbot ou outro aplicativo, é responsabilidade do chatbot para imitar a região na chamada para o Gestor de tráfego. |
    |-RelativeDnsName|Luis-dns-principal|Este é o subdomínio para o serviço: luis-dns-parent.trafficmanager.net|
    |-O valor de Ttl|30|Intervalo de consulta, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Portas e protocolos para LUIS é HTTPS/443|
    |-MonitorPath|`/`|Este caminho não importa porque os caminhos de ponto final de subordinados são utilizados em vez disso.|

    Um pedido com êxito tem sem resposta.

2. Adicionar perfil de subordinados deste dos E.U.A. a principal com o **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e **NestedEndpoints** tipo

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|ponto final de subordinado-useast|Perfil do leste|
    |-TrafficManagerProfile|$parentprofile|Perfil para atribuir este ponto final para|
    |-Type|NestedEndpoints|Para obter mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. ID|ID do perfil subordinado|
    |-EndpointStatus|Ativado|Estado do ponto final depois de adicionar ao principal|
    |-EndpointLocation|"eastus"|[Nome da região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) do recurso|
    |-MinChildEndpoints|1|Número mínimo de pontos finais subordinados|

    A aparência de resposta com êxito como a seguir e inclui o novo `child-endpoint-useast` ponto final:    

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

3. Adicionar perfil de subordinados do Oeste dos E.U.A. a principal com o **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet e **NestedEndpoints** tipo

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome da variável ou valor|Objetivo|
    |--|--|--|
    |-EndpointName|ponto final de subordinado-uswest|Perfil de oeste|
    |-TrafficManagerProfile|$parentprofile|Perfil para atribuir este ponto final para|
    |-Type|NestedEndpoints|Para obter mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. ID|ID do perfil subordinado|
    |-EndpointStatus|Ativado|Estado do ponto final depois de adicionar ao principal|
    |-EndpointLocation|"westus"|[Nome da região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) do recurso|
    |-MinChildEndpoints|1|Número mínimo de pontos finais subordinados|

    A aparência de resposta com êxito, como e inclui ambos os anterior `child-endpoint-useast` ponto final e a nova `child-endpoint-uswest` ponto final:

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

4. Defina pontos de extremidade com **[conjunto AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Uma resposta com êxito é a mesma resposta como passo 3.

### <a name="powershell-variables"></a>Variáveis do PowerShell
Nas seções anteriores, foram criadas três variáveis do PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Estas variáveis são utilizadas em direção ao final da configuração do Gestor de tráfego. Se optar por não criar as variáveis, se esqueceu de ou janela do PowerShell exceder o tempo limite, pode utilizar o cmdlet do PowerShell  **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, para obter novamente o perfil e atribuí-la para um variável. 

Substitua os itens de colchetes angulares, `<>`, com os valores corretos para cada um dos três perfis que precisa. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifique se o Gestor de tráfego funciona
Para verificar se o Gestor de tráfego perfis de trabalho, os perfis tem de ter o estado de `Online` este estado é baseado no caminho de consulta do ponto de extremidade. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Modo de exibição novos perfis no portal do Azure
Pode verificar que todos os três perfis são criados ao observar os recursos a `luis-traffic-manager` grupo de recursos.

![Captura de ecrã do Azure resource grupo luis--Gestor de tráfego](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verifique se que o estado do perfil está Online
O Gestor de tráfego consulta o caminho de cada ponto final para se certificar de que está online. Se estiver online, o estado dos perfis de subordinados são `Online`. Eles são exibidos na **descrição geral** de cada perfil. 

![Mostrar o Monitor de estado de Online de descrição geral do perfil de captura de ecrã do Gestor de tráfego do Azure](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validar a consulta funciona o Gestor de tráfego
É outra forma de validar o Gestor de tráfego consulta funciona com os registos de ponto final do LUIS. Sobre o [LUIS] [ LUIS] lista de aplicações do Web site página, exportar o registo do ponto final para a aplicação. Uma vez que o Gestor de tráfego, muitas vezes, consulta os dois pontos de extremidade, existem entradas nos registos, mesmo que elas foram apenas em alguns minutos. Lembre-se em busca de entradas em que a consulta começa com `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Validar a resposta DNS do Gestor de tráfego funciona
Para validar que a resposta DNS retorna um ponto de extremidade do LUIS, solicite o perfil de Gestor de tráfego principal DNS com uma biblioteca de cliente DNS. Os nomes do DNS para o perfil de principal é `luis-dns-parent.trafficmanager.net`.

O seguinte código de node. js faz um pedido para o perfil de principal e retorna um ponto de extremidade do LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A resposta com êxito com o ponto de final do LUIS é:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Utilizar o perfil de principal do Gestor de tráfego
Para gerir o tráfego em pontos finais, terá de inserir uma chamada para o DNS do Gestor de tráfego para encontrar o ponto de final do LUIS. Esta chamada é feita para cada solicitação de ponto final do LUIS e precisa simular a localização geográfica do utilizador da aplicação de cliente do LUIS. Adicione o código de resposta DNS entre a aplicação de cliente do LUIS e o pedido para o LUIS para a predição de ponto final. 

## <a name="resolving-a-degraded-state"></a>Resolução de um Estado degradado

Ativar [registos de diagnóstico](../../traffic-manager/traffic-manager-diagnostic-logs.md) para o Gestor de tráfego para ver por que o estado do ponto final está degradado.

## <a name="clean-up"></a>Limpeza
Remova as duas chaves de ponto final do LUIS, os três perfis do Gestor de tráfego e o grupo de recursos que continha estes recursos de cinco. Isso é feito a partir do portal do Azure. Eliminar os recursos de cinco na lista de recursos. Em seguida, elimine o grupo de recursos. 

## <a name="next-steps"></a>Passos Seguintes

Revisão [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) opções no BotFramework v4 para compreender a forma como este código de gestão de tráfego pode ser adicionado a um bot BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
