---
title: Utilize o filtro de pacotes do FreeBSD para criar uma firewall no Azure | Documentos da Microsoft
description: Saiba como implementar uma firewall NAT através do FreeBSD PF no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 8cfa1696a18925e9e9e8b96299f1255875e85aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542990"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como utilizar o filtro de pacotes do FreeBSD para criar uma firewall segura no Azure
Este artigo apresenta como implementar uma firewall NAT através Packer filtro do FreeBSD por meio do modelo Azure Resource Manager para o cenário de servidor web comuns.

## <a name="what-is-pf"></a>O que é o PF?
PF (filtro de pacotes, também escrito pf) é um filtro de estado do pacote licenciada BSD, uma parte central do software de firewall. PF uma vez que evoluiu rapidamente e agora tem algumas vantagens em relação a outras firewalls disponíveis. Tradução de endereços de rede (NAT) está em PF, desde um dia, em seguida, Agendador de pacotes e gerenciamento de fila de Active Directory foram integradas no PF, ao integrar o ALTQ e tornando configurável através da configuração do PF. Recursos como pfsync e CARP para ativação pós-falha e redundância, authpf para autenticação de sessão e do proxy de ftp para facilitar a Firewall do protocolo FTP difícil, também estenderam PF. Em resumo, PF é um firewall de potente e rica em recursos. 

## <a name="get-started"></a>Introdução
Se estiver interessado na configuração de um firewall seguras na cloud para os servidores web, em seguida, vamos começar. Também pode aplicar os scripts utilizados neste modelo Azure Resource Manager para configurar a sua topologia de rede.
O modelo Azure Resource Manager, configure uma máquina virtual de FreeBSD que realiza /redirection NAT, utilizar o PF e duas máquinas de virtuais de FreeBSD com o servidor de web de Nginx instalado e configurado. Além de executar o NAT para o tráfego de saída de servidores web de dois, a máquina de virtual NAT/redirecionamento intercepta as solicitações HTTP e redirecioná-los para os servidores web de dois em rodízio. A VNet utiliza a privada não encaminháveis internos IP endereço espaço 10.0.0.2/24 e pode modificar os parâmetros do modelo. O modelo Azure Resource Manager também define uma tabela de rotas para a VNet inteira, o que é uma coleção de rotas individuais utilizada para substituir as rotas predefinidas do Azure com base no endereço IP de destino. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementar através da CLI do Azure
Tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index). Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um nome de grupo de recursos `myResourceGroup` no `West US` localização.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implementar o modelo [configuração do freebsd pf](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) com [criar a implementação do grupo az](/cli/azure/group/deployment). Baixe [azuredeploy](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) sob o mesmo caminho e definir seus próprios valores de recursos, tais como `adminPassword`, `networkPrefix`, e `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Depois de cerca de cinco minutos, irá obter as informações de `"provisioningState": "Succeeded"`. Em seguida, pode ssh para o front-end VM (NAT) ou o servidor de web de Nginx num browser utilizando o endereço IP ou FQDN do front-end VM (NAT) de acesso. O exemplo seguinte lista o FQDN e o endereço IP público que atribuído para o VM (NAT) de front-end no `myResourceGroup` grupo de recursos. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passos Seguintes
Pretende configurar a sua própria NAT no Azure? Código-fonte aberto, gratuita, mas poderosas? Em seguida, PF é uma boa opção. Com o modelo [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), só precisa de cinco minutos para configurar uma firewall NAT com usando FreeBSD de balanceamento de carga round-robin do PF no Azure para o cenário de servidor web comuns. 

Se quiser saber a oferta do FreeBSD no Azure, consulte [introdução ao FreeBSD no Azure](freebsd-intro-on-azure.md).

Se quiser saber mais sobre o PF, consulte [FreeBSD handbook](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou [Guia do usuário do-PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
