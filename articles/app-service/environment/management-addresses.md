---
title: Endereços de gestão de ambiente de serviço de aplicações - Azure
description: Apresenta uma lista de endereços de gestão utilizados para um ambiente de serviço de aplicações de comando
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045292"
---
# <a name="app-service-environment-management-addresses"></a>Endereços de gestão do ambiente de serviço de aplicações

O ambiente de serviço de aplicações (ASE) é uma implementação de inquilino único do serviço de aplicações do Azure que é executado na sua rede Virtual do Azure (VNet).  Embora o ASE é executado na sua VNet, deve ainda ser acessível a partir de um número de endereços IP dedicados, que são utilizados pelo serviço de aplicações do Azure para gerir o serviço.  No caso de um ASE, o tráfego de gestão atravessa a rede de controlada pelo utilizador. Se este tráfego é bloqueado ou misrouted, o ASE irá tornar-se suspensa. Para obter detalhes sobre as dependências de rede do ASE, leia [considerações e o ambiente de serviço de aplicações de rede][networking]. Para obter informações gerais sobre o ASE, pode começar com [introdução ao ambiente de serviço de aplicações][intro].

Todos os ASEs têm um VIP público, o tráfego de gestão entra em. O tráfego de gestão entrada nestes endereços vêm de portas 454 e 455 no VIP público do seu ASE. Este documento apresenta uma lista os endereços de origem do serviço de aplicações para o tráfego de gestão para o ASE. Estes endereços são também na marca de serviço de IP com o nome AppServiceManagement.

Os endereços indicados abaixo podem ser configurados numa tabela de rotas para evitar problemas de encaminhamento assimétricos com o tráfego de gestão. Rotas de agirem sobre o tráfego ao nível do IP e não têm reconhecimento da direção do tráfego ou que o tráfego é uma parte de uma mensagem de resposta TCP. Se o endereço de resposta para um pedido TCP é diferente do que o endereço que foi enviado, terá um problema de encaminhamento assimétrico. Para evitar problemas de encaminhamento assimétricos com o seu tráfego de gestão do ASE, terá de garantir que as respostas são enviadas do mesmo endereço que foram enviadas para. Para obter detalhes sobre como configurar o ASE para operar num ambiente em que o tráfego de saída é enviado no local, leia [configurar o ASE com túnel forçado][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista de endereços de gestão ##

| Região | Endereços |
|--------|-----------|
| Todas as regiões públicas | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Governo | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configurar um grupo de segurança de rede

Com grupos de segurança de rede, não é necessário se preocupar sobre os endereços individuais ou manter a sua própria configuração. Existe uma etiqueta de serviço IP com o nome AppServiceManagement que é mantida atualizada com todos os endereços. Para utilizar esta etiqueta de serviço IP no seu NSG, aceda ao portal, abra a IU de grupos de segurança de rede e selecionar regras de segurança de entrada. Se tiver uma regra já existente para o tráfego de entrada de gestão, editá-lo. Se este NSG não foi criado com o seu ASE, ou se se trata de todos os novos, em seguida, selecione **adicionar**. Em origem pendente, selecione **etiquetas de serviço**.  Sob a etiqueta de serviço de origem, selecione **AppServiceManagement**. Definir a origem de intervalos de portas \*, o destino para **qualquer**, intervalos de portas de destino para **454 455**, de protocolo para **TCP**e a ação para **permitir** . Se estiver fazendo a regra, em seguida, terá de definir a prioridade. 

![criar um NSG com a etiqueta de serviço][1]

## <a name="configuring-a-route-table"></a>Configurar uma tabela de rotas

Os endereços de gestão podem ser colocados numa tabela de rotas com um salto seguinte da internet para garantir que todo o tráfego de entrada de gestão é capaz de voltar a repetir o mesmo caminho. Estas rotas são necessários quando configurar o túnel forçado. Para criar a tabela de rotas, pode utilizar o portal, PowerShell ou a CLI do Azure.  São os comandos para criar uma tabela de rotas com o CLI do Azure a partir de uma linha de comandos do PowerShell abaixo. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

Depois de sua tabela de rotas é criada, tem de defini-lo na sub-rede do ASE.  

## <a name="get-your-management-addresses-from-api"></a>Obtenha os endereços de gestão de API ##

Pode listar os endereços de gestão que correspondem ao seu ASE com a seguinte chamada à API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

A API devolve um documento JSON que inclui todos os endereços de entrada para o seu ASE. A lista de endereços inclui os endereços de gestão, o VIP utilizado pelo seu ASE e o intervalo de endereços de sub-rede do ASE em si.  

Para chamar a API com o [armclient](https://github.com/projectkudu/ARMClient) utilize os seguintes comandos, mas substitua no seu ID de subscrição, o grupo de recursos e o nome do ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
