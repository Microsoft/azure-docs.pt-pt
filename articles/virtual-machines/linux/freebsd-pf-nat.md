---
title: Utilize o filtro de pacote saque do FreeBSD para criar uma firewall em Azure
description: Aprenda a implementar uma firewall NAT utilizando o PF do FreeBSD em Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945099"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como utilizar o filtro de pacotes do FreeBSD para criar uma firewall segura em Azure
Este artigo introduz como implementar uma firewall NAT usando o filtro Packer do FreeBSD através do modelo de Gestor de Recursos Azure para o cenário comum do servidor web.

## <a name="what-is-pf"></a>O que é PF?
PF (Packet Filter, também escrito pf) é um filtro de pacote salutar licenciado da BSD, uma peça central de software para firewalling. Desde então, a PF evoluiu rapidamente e agora tem várias vantagens sobre outras firewalls disponíveis. A Tradução de Endereços de Rede (NAT) está em PF desde o primeiro dia, depois o programador de pacotes e a gestão ativa da fila foram integrados em PF, integrando o ALTQ e tornando-o configurável através da configuração de PF. Características como pfsync e CARP para falha e redundância, authpf para autenticação de sessão, e ftp-proxy para facilitar a firewalling do difícil protocolo FTP, também alargaram o PF. Em suma, o PF é uma firewall poderosa e rica em recursos. 

## <a name="get-started"></a>Introdução
Se está interessado em configurar uma firewall segura na nuvem para os seus servidores web, então vamos começar. Também pode aplicar os scripts utilizados neste modelo do Gestor de Recursos Azure para configurar a sua topologia de networking.
O modelo do Gestor de Recursos Azure criou uma máquina virtual FreeBSD que executa NAT /redirection utilizando PF e duas máquinas virtuais FreeBSD com o servidor web Nginx instalado e configurado. Além de executar NAT para os dois servidores web egress tráfego, a máquina virtual NAT/redirection interceta pedidos HTTP e redirecioná-los para os dois servidores web de forma redonda. O VNet utiliza o espaço de endereço IP não resaída privado 10.0.0.2/24 e pode modificar os parâmetros do modelo. O modelo do Gestor de Recursos Azure também define uma tabela de rotas para todo o VNet, que é uma coleção de rotas individuais usadas para sobrepor as rotas padrão do Azure com base no endereço IP de destino. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implante através do Azure CLI
Precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index). Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria `myResourceGroup` um `West US` nome de grupo de recursos no local.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implemente o modelo [pf-freebsd-configuração](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) com [a criação](/cli/azure/group/deployment)de implementação do grupo Az . Baixe [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) no mesmo caminho e defina `adminPassword`os `networkPrefix`seus `domainNamePrefix`próprios valores de recursos, tais como , e . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Após cerca de cinco minutos, `"provisioningState": "Succeeded"`obterá a informação de . Em seguida, pode aceder ao VM frontend (NAT) ou aceder ao servidor web nginx num browser utilizando o endereço IP público ou FQDN do VM frontend (NAT). O exemplo seguinte lista fQDN e endereço IP público que atribuiu ao `myResourceGroup` VM frontend (NAT) no grupo de recursos. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passos seguintes
Quer montar o seu próprio NAT em Azure? Open Source, livre, mas poderoso? Então pf é uma boa escolha. Ao utilizar o modelo de [configuração de pf-freebsd,](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)só precisa de cinco minutos para configurar uma firewall NAT com um equilíbrio de carga de robin redondo utilizando o PF do FreeBSD em Azure para um cenário comum de servidor web. 

Se quiser aprender a oferta de FreeBSD em Azure, consulte a [introdução ao FreeBSD no Azure](freebsd-intro-on-azure.md).

Se quiser saber mais sobre PF, consulte o [manual FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou [o Guia do Utilizador de PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
