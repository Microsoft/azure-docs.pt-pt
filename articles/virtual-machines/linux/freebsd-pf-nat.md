---
title: Use o filtro de pacotes freeBSD para criar uma firewall em Azure
description: Saiba como implantar uma firewall NAT utilizando o PF do FreeBSD em Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 6a20708c5564075c24eb031a39292b020a2ecc00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371325"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como utilizar o filtro de pacotes freeBSD para criar uma firewall segura em Azure
Este artigo introduz como implementar uma firewall NAT usando o filtro packer do FreeBSD através do modelo Azure Resource Manager para um cenário comum de servidor web.

## <a name="what-is-pf"></a>O que é PF?
PF (Filter de pacotes, também escrito pf) é um filtro de pacotes stateful licenciado em BSD, uma peça central de software para firewalling. Desde então, o PF evoluiu rapidamente e agora tem várias vantagens sobre outras firewalls disponíveis. A Tradução de Endereços de Rede (NAT) está em PF desde o primeiro dia, depois o programador de pacotes e a gestão ativa da fila foram integrados em PF, integrando o ALTQ e tornando-o configurável através da configuração do PF. Funcionalidades como pfsync e CARP para failover e redundância, authpf para autenticação de sessão, e ftp-proxy para facilitar a firewall do difícil protocolo FTP, também alargaram o PF. Em suma, o PF é uma firewall poderosa e rica em recursos. 

## <a name="get-started"></a>Introdução
Se estiver interessado em configurar uma firewall segura na nuvem para os seus servidores web, então vamos começar. Também pode aplicar os scripts utilizados neste modelo de Gestor de Recursos Azure para configurar a sua topologia de networking.
O modelo Azure Resource Manager criou uma máquina virtual FreeBSD que executa NAT /redirection usando PF e duas máquinas virtuais FreeBSD com o servidor web Nginx instalado e configurado. Além de realizar o NAT para os dois servidores web esgresso, a máquina virtual NAT/redirection interceta pedidos HTTP e redireciona-os para os dois servidores web em forma de robin redondo. O VNet utiliza o espaço de endereço IP não roteável privado 10.0.0.2/24 e pode modificar os parâmetros do modelo. O modelo Azure Resource Manager também define uma tabela de rotas para todo o VNet, que é uma coleção de rotas individuais usadas para sobrepor rotas padrão Azure com base no endereço IP de destino. 

![O diagrama mostra um endereço público I P numa instância NAT que redireciona pelo método round-robin para duas máquinas virtuais de backend que hospedam servidores web Nginx.](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementar através do Azure CLI
Precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index). Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um nome de grupo de recursos `myResourceGroup` no `West US` local.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implemente a configuração livre de pf-pf do modelo com [a implementação do grupo az criar](/cli/azure/group/deployment). Faça o download azuredeploy.parameters.jsno mesmo caminho e defina os seus próprios valores de recursos, tais `adminPassword` `networkPrefix` como, e `domainNamePrefix` . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Após cerca de cinco minutos, obterá a informação de `"provisioningState": "Succeeded"` . Em seguida, pode aceder ao frontend VM (NAT) ou aceder ao servidor web Nginx num browser utilizando o endereço IP público ou FQDN do frontend VM (NAT). O exemplo a seguir lista fQDN e endereço IP público que atribuiu ao frontend VM (NAT) no `myResourceGroup` grupo de recursos. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passos seguintes
Quer montar o seu próprio NAT em Azure? Open Source, livre mas poderoso? Então PF é uma boa escolha. Ao utilizar a configuração freebsd-freebsd do modelo, você só precisa de cinco minutos para configurar uma firewall NAT com a carga de robin redondo equilibrando usando o PF do FreeBSD em Azure para um cenário comum de servidor web. 

Se quiser aprender a oferta do FreeBSD em Azure, consulte [a introdução ao FreeBSD em Azure.](freebsd-intro-on-azure.md)

Se quiser saber mais sobre o PF, consulte o [manual FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou o [Guia do Utilizador PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
