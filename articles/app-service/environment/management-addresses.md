---
title: Endereços de gestão
description: Encontre os endereços de gestão utilizados para controlar um Ambiente de Serviço de Aplicações. Configurá-los numa tabela de rotas para evitar problemas de encaminhamento assimétricos.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687117"
---
# <a name="app-service-environment-management-addresses"></a>Endereços de gestão do ambiente do serviço de aplicações

O App Service Environment (ASE) é uma implantação única do Serviço de Aplicações Azure que funciona na sua Rede Virtual Azure (VNet).  Enquanto a ASE funciona no seu VNet, deve ainda estar acessível a partir de uma série de endereços IP dedicados que são usados pelo Serviço de Aplicações Azure para gerir o serviço.  No caso de uma ASE, o tráfego de gestão atravessa a rede controlada pelo utilizador. Se este tráfego estiver bloqueado ou desviado, a ASE será suspensa. Para mais detalhes sobre as dependências de rede da ASE, leia [considerações de Networking e o Ambiente][networking]do Serviço de Aplicações. Para obter informações gerais sobre a ASE, pode começar com [a Introdução ao Ambiente do Serviço de Aplicações.][intro]

Todas as ASEs têm um VIP público em que o tráfego de gestão entra. O tráfego de gestão de entrada destes endereços vem dos portos 454 e 455 no VIP público da sua ASE. Este documento lista os endereços de origem do Serviço de Aplicações para o tráfego de gestão para a ASE. Estes endereços também estão na etiqueta de serviço IP chamada AppServiceManagement.

Os endereços a seguir indicados podem ser configurados numa tabela de rotas para evitar problemas de encaminhamento assimétricos com o tráfego de gestão. As rotas atuam no tráfego ao nível do PI e não têm consciência da direção de trânsito ou que o tráfego faz parte de uma mensagem de resposta da TCP. Se o endereço de resposta para um pedido de TCP for diferente do endereço para o endereço a que foi enviado, tem um problema de encaminhamento assimétrico. Para evitar problemas de encaminhamento assimétricos com o tráfego de gestão da ASE, é necessário garantir que as respostas são enviadas do mesmo endereço para o que foram enviadas. Para obter mais detalhes sobre como configurar a Sua ASE para operar num ambiente onde o tráfego de saída é enviado no local, leia [Configure a Sua ASE com túneis forçados][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista de endereços de gestão ##

| Região | Endereços |
|--------|-----------|
| Todas as regiões públicas | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Governo Microsoft Azure | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configurar um Grupo de Segurança de Rede

Com os Grupos de Segurança da Rede, não precisa de se preocupar com os endereços individuais ou manter a sua própria configuração. Existe uma etiqueta de serviço IP chamada AppServiceManagement que é mantida atualizada com todos os endereços. Para utilizar esta etiqueta de serviço IP no seu NSG, vá ao portal, abra o UI dos Grupos de Segurança da Rede e selecione regras de segurança de entrada. Se tiver uma regra pré-existente para o tráfego de gestão de entrada, edite-a. Se este NSG não foi criado com a sua ASE, ou se é tudo novo, então selecione **Adicionar**. Sob a queda da Fonte, selecione **Etiqueta de Serviço**.  Sob a etiqueta de serviço Fonte, selecione **AppServiceManagement**. Delineie as \*gamas de porta de origem para , Destino para **Qualquer**, Destino da porta varia para **454-455**, Protocolo para **TCP,** e Ação a **Permitir**. Se estás a fazer a regra, então tens de definir a Prioridade. 

![criando um NSG com a etiqueta de serviço][1]

## <a name="configuring-a-route-table"></a>Configurar uma tabela de rotas

Os endereços de gestão podem ser colocados numa tabela de rotas com um próximo salto de internet para garantir que todo o tráfego de gestão de entrada seja capaz de voltar pelo mesmo caminho. Estas rotas são necessárias para configurar túneis forçados. Para criar a tabela de rotas, pode utilizar o portal, PowerShell ou Azure CLI.  Os comandos para criar uma tabela de rota utilizando o Azure CLI a partir de um pedido PowerShell estão abaixo. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Depois de criada a sua tabela de rotas, tem de a colocar na sua subnet ASE.  

## <a name="get-your-management-addresses-from-api"></a>Obtenha os seus endereços de gestão da API ##

Pode listar os endereços de gestão que correspondem à sua ASE com a seguinte chamada API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

A API devolve um documento JSON que inclui todos os endereços de entrada para a sua ASE. A lista de endereços inclui os endereços de gestão, o VIP utilizado pela sua Gama de Endereços ASE e a própria gama de endereços da sub-rede ASE.  

Para ligar para a API com o [armclient](https://github.com/projectkudu/ARMClient) use os seguintes comandos, mas substitua o seu ID de subscrição, grupo de recursos e nome ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
