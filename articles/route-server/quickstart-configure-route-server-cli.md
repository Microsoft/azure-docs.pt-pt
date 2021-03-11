---
title: 'Quickstart: Criar e configurar o Servidor de Rota usando o Azure CLI'
description: Neste arranque rápido, aprende-se a criar e configurar um Servidor de Rota utilizando o Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 73dc54ca04ad6ddb275947663959164cc2e3c019
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547888"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Quickstart: Criar e configurar o Servidor de Rota usando o Azure CLI 

Este artigo ajuda-o a configurar o Azure Route Server para fazer par com aparelhos virtuais de rede (NVA) na sua rede virtual utilizando o Azure CLI. O Azure Route Server aprenderá as rotas a partir do NVA e programá-las-á para as máquinas virtuais da rede virtual. Também irá anunciar as rotas de rede virtual para a NVA. Para mais informações, leia [o Azure Route Server](overview.md).

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Pré-requisitos 

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
* Certifique-se de que tem o mais recente Azure CLI, ou pode utilizar a Azure Cloud Shell no portal. 
* Reveja os limites de [serviço para O Servidor de Rota Azure](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Criar um servidor de rota 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição. 

Para iniciar a sua configuração, inicie sação na sua conta Azure. Se utilizar a Cloud Shell "Try It", está assinado automaticamente. Utilize os seguintes exemplos para o ajudar a ligar:

```azurecli-interactive
az login
```

Verifique as subscrições da conta.

```azurecli-interactive
az account list
```

Selecione a subscrição para a qual pretende criar um circuito ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual 

Antes de criar um Azure Route Server, precisará de uma rede virtual para hospedar a implementação. Utilize o comando seguinte para criar um grupo de recursos e uma rede virtual. Se já tiver uma rede virtual, pode saltar para a secção seguinte.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Adicionar uma sub-rede 

1. Adicione uma sub-rede chamada *RouteServerSubnet* para implementar o Azure Route Server. Esta sub-rede é uma sub-rede dedicada apenas para O Servidor de Rota Azure. O RouteServerSubnet deve ser /27 ou um prefixo mais curto (como /26, /25), ou receberá uma mensagem de erro quando adicionar o Azure Route Server.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Obtenha o ID RouteServerSubnet. Para visualizar o ID de todos os subredes da rede virtual, utilize este comando: 

    ```azurecli-interactive 
    subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

O ID RouteServerSubnet parece o seguinte: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Criar o Servidor de Rota 

Crie o Servidor de Rota com este comando: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

A localização precisa de corresponder à localização da sua rede virtual. O HostedSubnet é o ID RouteServerSubnet que obteve na secção anterior. 

## <a name="create-peering-with-an-nva"></a>Criar olhando com um NVA 

Utilize o seguinte comando para estabelecer o espreguitamento do Servidor de Rota para o NVA: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" é o IP de rede virtual atribuído à NVA. "nva_asn" é o Número do Sistema Autónomo (ASN) configurado na NVA. A ASN pode ser qualquer número de 16 bits que não os do intervalo de 65515-65520. Esta gama de ASNs é reservada pela Microsoft. 

Para configurar o seu espreitamento com diferentes NVA ou outro caso da mesma NVA para redundância, utilize este comando:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Complete a configuração no NVA 

Para completar a configuração no NVA e ativar as sessões de BGP, precisa do IP e do ASN do Azure Route Server. Pode obter esta informação usando este comando: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

A saída tem as seguintes informações. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configure troca de rotas 

Se tiver um gateway ExpressRoute e uma porta de entrada Azure VPN no mesmo VNet e quiser que troquem rotas, pode ativar a troca de rotas no Azure Route Server.

> [!IMPORTANT]
> Para implementações de greenfield certifique-se de criar o gateway Azure VPN antes de criar o Azure Route Server; caso contrário, a implantação do Gateway Azure VPN falhará.
> 

1. Para ativar a troca de rotas entre o Azure Route Server e o gateway(s), utilize este comando:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Para desativar a troca de rotas entre o Azure Route Server e o gateway(s), utilize este comando:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Resolução de problemas 

Pode ver as rotas anunciadas e recebidas pelo Azure Route Server com este comando:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar do Servidor de Rota Azure, utilize estes comandos para remover o perspível BGP e, em seguida, remova o Servidor de Rota. 

1. Remova o espreitamento BGP entre o Azure Route Server e um NVA com este comando:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Remova o Servidor de Rota azul com este comando: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Passos seguintes

Depois de criar o Azure Route Server, continue a aprender como o Azure Route Server interage com o ExpressRoute e o VPN Gateways: 

> [!div class="nextstepaction"]
> [Suporte Azure ExpressRoute e Azure VPN](expressroute-vpn-support.md)
 
