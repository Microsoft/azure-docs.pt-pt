---
title: Configurar IP de saída estática
description: Configure firewall Azure e rotas definidas pelo utilizador para cargas de trabalho de contentores Azure que utilizam o endereço IP público da firewall para entrada e saída
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89566573"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Configure um único endereço IP público para o tráfego de saída e de entrada para um grupo de contentores

A criação de um grupo de [contentores](container-instances-container-groups.md) com um endereço IP virado para o exterior permite que os clientes externos utilizem o endereço IP para aceder a um contentor no grupo. Por exemplo, um navegador pode aceder a uma aplicação web em execução num recipiente. No entanto, atualmente, um grupo de contentores utiliza um endereço IP diferente para tráfego de saída. Este endereço IP de saída não é exposto programáticamente, o que torna a monitorização do grupo de contentores e a configuração das regras de firewall do cliente mais complexas.

Este artigo fornece passos para configurar um grupo de contentores numa [rede virtual](container-instances-virtual-network-concepts.md) integrada com [a Azure Firewall.](../firewall/overview.md) Ao configurar uma rota definida pelo utilizador para as regras do grupo de contentores e firewall, pode encaminhar e identificar o tráfego de e para o grupo de contentores. O grupo de contentores entra e a saída utilizam o endereço IP público da firewall. Um único endereço IP de saída pode ser utilizado por vários grupos de contentores implantados na sub-rede da rede virtual delegada em Instâncias de Contentores Azure.

Neste artigo utiliza-se o CLI Azure para criar os recursos para este cenário:

* Grupos de contentores implantados numa sub-rede delegada [na rede virtual](container-instances-vnet.md) 
* Uma firewall Azure implantada na rede com um endereço IP público estático
* Uma rota definida pelo utilizador na sub-rede dos grupos de contentores
* Uma regra NAT para entrada de firewall e uma regra de aplicação para saída

Em seguida, valida-se a entrada e saída de grupos de contentores de exemplo através da firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>Implementar O ACI numa rede virtual

Num caso típico, pode já ter uma rede virtual Azure na qual se implanta um grupo de contentores. Para fins de demonstração, os seguintes comandos criam uma rede virtual e uma sub-rede quando o grupo de contentores é criado. A sub-rede é delegada em Instâncias de Contentores Azure. 

O grupo de contentores executa uma pequena aplicação web a partir da `aci-helloworld` imagem. Como mostrado em outros artigos na documentação, esta imagem embala uma pequena aplicação web escrita em Node.js que serve uma página de HTML estática.

Se precisar de um, primeiro crie um grupo de recursos Azure com o [grupo az criar][az-group-create] comando. Por exemplo:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para simplificar os seguintes exemplos de comando, utilize uma variável ambiental para o nome do grupo de recursos:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Criar o grupo de contentores com o [recipiente az criar][az-container-create] comando:

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Ajuste o valor do `--subnet address-prefix` espaço de endereço IP necessário na sua sub-rede. A sub-rede suportada mais pequena é /29, que fornece oito endereços IP. Alguns endereços IP são reservados para uso pela Azure.

Para ser utilizado posteriormente, obtenha o endereço IP privado do grupo de contentores executando o comando [az-container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Implementar firewall Azure na rede

Nas secções seguintes, utilize o CLI Azure para implantar uma firewall Azure na rede virtual. Para obter antecedentes, consulte [Tutorial: Implemente e configuure Firewall através do portal Azure](../firewall/deploy-cli.md).

Em primeiro lugar, utilize a [sub-rede de rede az para][az-network-vnet-subnet-create] adicionar uma sub-rede chamada AzureFirewallSubnet para a firewall. AzureFirewallSubnet é o nome *necessário* desta sub-rede.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Utilize os [seguintes comandos Azure CLI](../firewall/deploy-cli.md) para criar uma firewall na sub-rede.

Se ainda não estiver instalado, adicione a extensão de firewall ao CLI Azure utilizando o comando [de adicionar extensão az:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

Criar os recursos de firewall:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Atualize a configuração da firewall utilizando o comando de atualização da [firewall da rede az:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Obtenha o endereço IP privado da firewall utilizando o comando [da lista ip-config da rede az.][az-network-firewall-ip-config-list] Este endereço IP privado é utilizado num comando posterior.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Obtenha o endereço IP público da firewall usando o comando [de show público-ip da rede Az.][az-network-public-ip-show] Este endereço IP público é utilizado num comando posterior.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definir rota definida pelo utilizador na sub-rede ACI

Defina uma rota definida pela utilização na sub-rede ACI, para desviar o tráfego para a firewall do Azure. Para mais informações, consulte [o tráfego da rede De Rota.](../virtual-network/tutorial-create-route-table-cli.md) 

### <a name="create-route-table"></a>Criar tabela de rotas

Em primeiro lugar, executar a [seguinte tabela de rotas de rede az criar][az-network-route-table-create] comando para criar a tabela de rotas. Crie a tabela de rotas na mesma região que a rede virtual.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Criar rota

Executar [a rota de rota de rede az criar][az-network-route-table-route-create] para criar uma rota na tabela de rotas. Para encaminhar o tráfego para a firewall, desaperte o próximo tipo de lúpulo `VirtualAppliance` para , e passe o endereço IP privado da firewall como o próximo endereço de lúpulo.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Tabela de rota associada à sub-rede ACI

Executar o comando [de atualização da sub-rede de rede az][az-network-vnet-subnet-update] para associar a tabela de rotas com a sub-rede delegada em Instâncias de Contentores Azure.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Configure regras sobre firewall

Por defeito, a Azure Firewall nega (blocos) tráfego de entrada e saída. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Configure a regra NAT na firewall para a sub-rede ACI

Crie uma [regra NAT](../firewall/rule-processing.md) na firewall para traduzir e filtrar o tráfego de internet de entrada para o recipiente de aplicação que começou anteriormente na rede. Para mais detalhes, consulte [o tráfego de internet de entrada de filtro com ADN da Azure Firewall](../firewall/tutorial-firewall-dnat.md)

Crie uma regra e recolha de NAT utilizando o comando [de criação de firewall de firewall de rede az:][az-network-firewall-nat-rule-create]

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Adicione as regras NAT, conforme necessário para filtrar o tráfego para outros endereços IP na sub-rede. Por exemplo, outros grupos de contentores na sub-rede podem expor endereços IP para tráfego de entrada, ou outros endereços IP internos poderiam ser atribuídos ao grupo de contentores após um reinício.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Criar regra de aplicação de saída na firewall

Executar a seguinte [regra de aplicação de firewall de rede az criar][az-network-firewall-application-rule-create] comando para criar uma regra de saída na firewall. Esta regra da amostra permite o acesso da sub-rede delegada a Instâncias do Contentor Azure à FQDN `checkip.dyndns.org` . O acesso HTTP ao site é utilizado num passo posterior para confirmar o endereço IP da saída das Instâncias do Contentor de Azure.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Teste de acesso do grupo de recipiente através da firewall

As seguintes secções verificam se a sub-rede delegada em Instâncias de Contentores Azure está corretamente configurada atrás da firewall Azure. Os passos anteriores encaminham o tráfego de entrada para a sub-rede e o tráfego de saída da sub-rede através da firewall.

### <a name="test-ingress-to-a-container-group"></a>Teste de entrada para um grupo de contentores

Teste o acesso ao *appcontainer* que está a ser gerido na rede virtual navegando para o endereço IP público da firewall. Anteriormente, guardou o endereço IP público em variável $FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

A saída é semelhante a:

```console
52.142.18.133
```

Se a regra NAT na firewall estiver configurada corretamente, verá o seguinte quando introduz o endereço IP público da firewall no seu navegador:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Navegue para o endereço IP público da firewall":::

### <a name="test-egress-from-a-container-group"></a>Saída de teste de um grupo de contentores


Deite o seguinte recipiente de amostra na rede virtual. Quando é executado, envia um único pedido HTTP para `http://checkip.dyndns.org` , que exibe o endereço IP do remetente (o endereço IP da saída). Se a regra de aplicação na firewall estiver configurada corretamente, o endereço IP público da firewall é devolvido.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Ver os registos do contentor para confirmar que o endereço IP é o mesmo que o endereço IP público da firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

A saída é semelhante a:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você configura grupos de contentores numa rede virtual atrás de uma firewall Azure. Configuraste uma rota definida pelo utilizador e regras de NAT e aplicação na firewall. Ao utilizar esta configuração, configura um único endereço IP estático para entrada e saída de Instâncias de Contentores Azure.

Para obter mais informações sobre a gestão do tráfego e a proteção dos recursos da Azure, consulte a documentação do [Azure Firewall.](../firewall/index.yml)



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






