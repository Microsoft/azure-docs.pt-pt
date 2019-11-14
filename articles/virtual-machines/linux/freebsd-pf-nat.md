---
title: Use o filtro de pacote do FreeBSD para criar um firewall no Azure
description: Saiba como implantar um firewall NAT usando o PF do FreeBSD no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 5daceeb2c8f2497288c7891dbe3fb3e0771b2ed5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036105"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como usar o filtro de pacotes do FreeBSD para criar um firewall seguro no Azure
Este artigo apresenta como implantar um firewall NAT usando o filtro de empacotador do FreeBSD por meio do modelo de Azure Resource Manager para o cenário de servidor Web comum.

## <a name="what-is-pf"></a>O que é o PF?
O PF (filtro de pacote, também de PF gravado) é um filtro de pacote com estado licenciado do BSD, uma parte central do software para firewall. O PF já evoluiu rapidamente e agora tem várias vantagens em relação a outros firewalls disponíveis. A NAT (conversão de endereços de rede) está em PF desde o primeiro dia, o Agendador de pacotes e o gerenciamento de filas ativas foram integrados ao PF, integrando o ALTQ e tornando-o configurável por meio da configuração do PF. Recursos como pfsync e CARP para failover e redundância, authpf para autenticação de sessão e proxy de FTP para facilitar o Firewall do protocolo FTP difícil, também estenderam o PF. Em suma, o PF é um firewall avançado e rico em recursos. 

## <a name="get-started"></a>Introdução
Se você estiver interessado em configurar um firewall seguro na nuvem para seus servidores Web, vamos começar. Você também pode aplicar os scripts usados neste modelo de Azure Resource Manager para configurar sua topologia de rede.
O modelo de Azure Resource Manager configurar uma máquina virtual FreeBSD que executa o NAT/Redirection usando PF e duas máquinas virtuais FreeBSD com o servidor Web Nginx instalado e configurado. Além de executar o NAT para o tráfego de saída dos dois servidores Web, a máquina virtual de NAT/redirecionamento intercepta as solicitações HTTP e redireciona-as para os dois servidores Web no modo Round Robin. A VNet usa o espaço de endereço IP não roteável privado 10.0.0.2/24 e você pode modificar os parâmetros do modelo. O modelo de Azure Resource Manager também define uma tabela de rotas para toda a VNet, que é uma coleção de rotas individuais usadas para substituir rotas padrão do Azure com base no endereço IP de destino. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implantar por meio do CLI do Azure
Você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index). Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um nome de grupo de recursos `myResourceGroup` no `West US` local.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implante o modelo [PF-FreeBSD-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) com [AZ Group Deployment Create](/cli/azure/group/deployment). Baixe [azuredeploy. Parameters. JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) no mesmo caminho e defina seus próprios valores de recurso, como `adminPassword`, `networkPrefix`e `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Após cerca de cinco minutos, você receberá as informações de `"provisioningState": "Succeeded"`. Em seguida, você pode usar SSH para a VM de front-end (NAT) ou acessar o servidor Web Nginx em um navegador usando o endereço IP público ou FQDN da VM de front-end (NAT). O exemplo a seguir lista o endereço IP público e FQDN atribuído à VM de front-end (NAT) no grupo de recursos `myResourceGroup`. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passos seguintes
Deseja configurar seu próprio NAT no Azure? Software livre, gratuito, mas eficiente? Em seguida, o PF é uma boa opção. Usando o modelo [PF-FreeBSD-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), você só precisa de cinco minutos para configurar um firewall NAT com balanceamento de carga Round Robin usando o PF do FreeBSD no Azure para um cenário de servidor Web comum. 

Se você quiser aprender a oferta do FreeBSD no Azure, consulte [introdução ao FreeBSD no Azure](freebsd-intro-on-azure.md).

Se você quiser saber mais sobre o PF, consulte o [manual do FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou o [Guia do usuário do PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
