---
title: Configurar túnel forçado
description: Saiba como permitir que o seu Ambiente de Serviço de Aplicações funcione quando o tráfego de saída é forçado a fazer um túnel na sua rede virtual.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: c8a4b6998d1471a79dd789ed6528e22b07f2015c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540980"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar o Ambiente de Serviço de Aplicações com túnel forçado

O Ambiente de Serviço de Aplicações (ASE) é uma implementação do Serviço de Aplicações do Azure numa Rede Virtual do Azure do cliente. Muitos clientes configuram as suas redes virtuais do Azure para serem extensões das respetivas redes no local com VPNs ou ligações do ExpressRoute do Azure. Um túnel forçado é quando redireciona o tráfego vinculado à Internet para a VPN ou uma aplicação virtual. Os aparelhos virtuais são frequentemente utilizados para inspecionar e auditar o tráfego da rede de saída. 

O ASE tem um número de dependências externas, que são descritas no documento [Arquitetura de rede do Ambiente de Serviço de Aplicações][network]. Normalmente, todo o tráfego de dependência de saída do ASE deve seguir o VIP aprovisionado com o ASE. Se alterar o encaminhamento para o tráfego de ou para o ASE sem seguir as informações abaixo, o ASE deixa de funcionar.

Numa rede virtual do Azure, o encaminhamento é feito com base no LPM (Longest Prefix Match). Se houver mais de uma rota com a mesma correspondência LPM, uma rota é selecionada com base na respetiva origem pela seguinte ordem:

* Rota definida pelo utilizador (UDR)
* Rota BGP (quando é utilizado o ExpressRoute)
* Rota de sistema

Para saber mais sobre o encaminhamento numa rede virtual, veja [Rotas definidas pelo utilizador e reencaminhamento IP][routes]. 

Se quiser encaminhar o tráfego de saída do ASE sem ser diretamente para a Internet, tem as seguintes opções:

* Ativar o ASE para ter acesso direto à Internet
* Configurar a sub-rede do ASE para ignorar as rotas BGP
* Configurar a sub-rede do ASE para utilizar Pontos Finais de Serviço para o SQL do Azure e o Armazenamento do Azure
* Adicionar os seus próprios IPs à firewall do SQL do Azure do ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Ativar o Ambiente de Serviço de Aplicações para ter acesso direto à Internet

Para ativar o ASE para aceder diretamente à Internet mesmo que a rede virtual do Azure esteja configurada com o ExpressRoute, pode:

* Configurar o ExpressRoute para anunciar 0.0.0.0/0. Por predefinição, encaminha todo o tráfego de saída no local.
* Criar um UDR com um prefixo de endereço de 0.0.0.0/0, um tipo de salto seguinte da Internet e aplicá-lo à sub-rede do ASE.

Se fizer estas duas alterações, o tráfego destinado à Internet que provém da sub-rede do Ambiente de Serviço de Aplicações não fica forçado à ligação do ExpressRoute.

Se a rede já estiver a encaminhar tráfego no local, em seguida, terá de criar a sub-rede para alojar a sua ASE e configurar o UDR para a mesma antes de tentar implementar o ASE.  

> [!IMPORTANT]
> As rotas definidas num UDR têm de ser suficientemente específicas para terem precedência sobre quaisquer rotas anunciadas pela configuração do ExpressRoute. O exemplo anterior utiliza o intervalo de endereços abrangente 0.0.0.0/0. Podem ser potencialmente substituídas por acidente por anúncios de rota que utilizam intervalos de endereço mais específicos.
>
> Os Ambientes de Serviço de Aplicações não são suportados com configurações do ExpressRoute que anunciem transversalmente rotas do caminho de peering público para o caminho de peering privado. As configurações do ExpressRoute com o peering público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um vasto conjunto de intervalos de endereços do Microsoft Azure. Se os intervalos de endereços forem anunciados transversalmente no caminho de peering privado, todos os pacotes de rede de saída da sub-rede do Ambiente de Serviço de Aplicações são encaminhados para a infraestrutura de rede no local do cliente. Este fluxo de rede não é suportado por predefinição com Ambientes de Serviço de Aplicações. Uma solução para este problema é deixar de anunciar transversalmente as rotas do caminho de peering público para o caminho de peering privado. Outra solução é ativar o Ambiente de Serviço de Aplicações para funcionar numa configuração de túnel forçado.

![Acesso direto à Internet][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Configurar a sub-rede do ASE para ignorar as rotas BGP ## 

Pode configurar a sub-rede do ASE para ignorar todas as rotas BGP.  Quando configurado para ignorar as rotas BGP, o ASE poderá aceder às suas dependências sem problemas.  No entanto, terá de criar UDRs para ativar as suas aplicações para acederem a recursos no local.

Para configurar a sub-rede do ASE para ignorar as rotas BGP:

* crie um UDR e atribua-o à sua sub-rede do ASE, se ainda não tinha um.
* No portal do Azure, abra a IU para a tabela de rotas atribuída à sua sub-rede do ASE.  Selecione a Configuração.  Desative a propagação da rota de gateway de rede virtual para desativado.  Clique em Guardar. A documentação sobre a desativação da mesma está no documento [Criar uma tabela de rotas][routetable].

Depois de configurar a sub-rede ASE para ignorar todas as rotas BGP, as suas aplicações deixarão de poder chegar ao local. Para permitir que as suas aplicações acedam a recursos no local, edite a UDR atribuída à sua sub-rede ASE e adicione rotas para o seu conjunto de endereços nas instalações. O Próximo tipo de salto deve ser definido para o Gateway de rede virtual. 


## <a name="configure-your-ase-with-service-endpoints"></a>Configurar o ASE com Pontos Finais de Serviço ##

Para encaminhar todo o tráfego de saída do ASE, exceto o que vai para o SQL do Azure e o Armazenamento do Azure, efetue os seguintes passos:

1. Crie uma tabela de rotas e atribua-a à sub-rede do ASE. Localize os endereços que correspondem à sua região em [Endereços de gestão do Ambiente de Serviço de Aplicações][management]. Crie rotas para esses endereços com um salto seguinte da Internet. Estas rotas são necessárias porque o tráfego de gestão de entrada do App Service Environment deve responder a partir do mesmo endereço para o que foi enviado.   

2. Ativar Pontos Finais de Serviço com o SQL do Azure e o Armazenamento do Azure através da sub-rede do ASE.  Depois de concluído este passo, pode configurar a VNet com túnel forçado.

Para obter detalhes sobre a implementação de um ASE com um modelo, leia [criar um Ambiente de Serviço de Aplicações utilizando um modelo][template].

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Pode ler mais sobre Pontos Finais de Serviço na documentação [Pontos Finais de Serviço de Rede Virtual][serviceendpoints]. 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar Pontos Finais de Serviço com um ASE com túnel forçado, o tráfego de gestão do SQL do Azure e do Armazenamento do Azure não fica com um túnel forçado. O outro tráfego de dependência do ASE tem um túnel forçado e não pode ser perdido ou o ASE não funcionará corretamente.

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra.  O Armazenamento do Azure não tem um comportamento semelhante ao SQL do Azure.  Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

Se configurar um túnel forçado com um dispositivo de filtro de rede, lembre-se de que o ASE tem dependências além do SQL do Azure e do Armazenamento do Azure. Se o tráfego estiver bloqueado a essas dependências, o ASE não funcionará corretamente.

![Túnel forçado com pontos finais de serviço][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Adicionar os seus próprios IPs à firewall do SQL do Azure do ASE ##

Para criar um túnel de todo o tráfego de saída do ASE, exceto o que vai para o Armazenamento do Azure, efetue os seguintes passos:

1. Crie uma tabela de rotas e atribua-a à sub-rede do ASE. Localize os endereços que correspondem à sua região em [Endereços de gestão do Ambiente de Serviço de Aplicações][management]. Crie rotas para esses endereços com um salto seguinte da Internet. Estas rotas são necessárias porque o tráfego de gestão de entrada do App Service Environment deve responder a partir do mesmo endereço para o que foi enviado. 

2. Ativar Pontos Finais de Serviço com o Armazenamento do Azure através da sub-rede do ASE

3. Obtenha os endereços que serão utilizados para todo o tráfego de saída do Ambiente de Serviço de Aplicações para a Internet. Se estiver a encaminhar o tráfego no local, estes endereços são os NATs ou os IPs de gateway. Se pretender encaminhar o tráfego de saída do Ambiente de Serviço de Aplicações através de uma NVA, o endereço de saída é o IP público da NVA.

4. _Para definir os endereços de saída num Ambiente de Serviço de Aplicações existente:_ Vá a resources.azure.com e vá a Subscription/ \<subscription id> /resourceGroups/ \<ase resource group> /providers/Microsoft.Web/hostingEnvironments/ \<ase name> . Depois, poderá ver o JSON que descreve o Ambiente de Serviço de Aplicações. Certifique-se de que diz **leitura/escrita** na parte superior. Selecione **Editar** . Desloque-se para baixo até à parte inferior. Altere o valor de **userWhitelistedIpRanges** de **nulo** para algo semelhante ao seguinte. Utilize os endereços que pretende definir como o intervalo de endereços de saída. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   Selecione **PUT** na parte superior. Esta opção aciona uma operação de dimensionamento no seu Ambiente de Serviço de Aplicações e ajusta a firewall.

_Para criar o ASE com os endereços de saída_ : siga as indicações em [Criar um Ambiente de Serviço de Aplicações com um modelo][template] e obtenha o modelo apropriado.  Edite a secção "resources" no ficheiro azuredeploy.json, mas não no bloco "properties" e inclua uma linha para **userWhitelistedIpRanges** com os seus valores.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Estas alterações enviam o tráfego para o Armazenamento do Azure diretamente a partir do ASE e permitem o acesso ao SQL do Azure a partir de endereços adicionais que não sejam o VIP do ASE.

   ![Túnel forçado com lista de permissões de SQL][3]

## <a name="preventing-issues"></a>Evitar problemas ##

Se a comunicação entre o ASE e as respetivas dependências for interrompida, o ASE não funcionará corretamente.  Se este comportamento se prolongar por demasiado tempo, o ASE será suspenso. Para anular a suspensão do ASE, siga as instruções no portal do ASE.

Além de simplesmente interromper a comunicação, pode afetar negativamente o ASE ao introduzir demasiada latência. Pode ocorrer demasiada latência se o ASE estiver muito distante da rede no local.  Exemplos desta situação incluem atravessar um oceano ou continente para alcançar a rede no local. A latência também pode ser introduzida devido a congestionamento da intranet ou restrições de largura de banda de saída.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
