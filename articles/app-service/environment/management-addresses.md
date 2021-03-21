---
title: Endereços de gestão
description: Encontre os endereços de gestão utilizados para controlar um Ambiente de Serviço de Aplicações. Configurou-os numa mesa de rotas para evitar problemas de encaminhamento assimétricos.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 02/11/2021
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 7810ca71ccdd8714773ffd459ef38b4bf80de02c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371622"
---
# <a name="app-service-environment-management-addresses"></a>Endereços de gestão de Ambiente de Serviço de Aplicações

O App Service Environment (ASE) é uma única implantação do Serviço de Aplicações Azure que funciona na sua Rede Virtual Azure (VNet).  Enquanto o ASE funciona no seu VNet, ainda deve estar acessível a partir de uma série de endereços IP dedicados que são utilizados pelo Serviço de Aplicações Azure para gerir o serviço.  No caso de uma ASE, o tráfego de gestão atravessa a rede controlada pelo utilizador. Se este tráfego estiver bloqueado ou desviado, o ASE será suspenso. Para mais informações sobre as dependências de networking da ASE, leia [considerações de Networking e o Ambiente de Serviço de Aplicações.][networking] Para obter informações gerais sobre o ASE, pode começar com [Introdução ao Ambiente de Serviço de Aplicações.][intro]

Todas as ASEs têm um VIP público em que o tráfego de gestão entra. O tráfego de gestão de entrada destes endereços vem dos portos 454 e 455 no VIP público do seu ASE. Este documento lista os endereços de origem do Serviço de Aplicações para tráfego de gestão para a ASE. Estes endereços também estão na Tag de Serviço IP chamada AppServiceManagement.

Os endereços abaixo indicados podem ser configurados numa tabela de rotas para evitar problemas de encaminhamento assimétricos com o tráfego de gestão. As rotas atuam no tráfego ao nível do IP e não têm consciência da direção de trânsito ou de que o tráfego faz parte de uma mensagem de resposta da TCP. Se o endereço de resposta para um pedido de TCP for diferente do endereço para o qual foi enviado, tem um problema de encaminhamento assimétrico. Para evitar problemas de encaminhamento assimétricos com o tráfego de gestão ASE, é necessário garantir que as respostas são enviadas de volta do mesmo endereço para o quais foram enviadas. Para obter mais informações sobre como configurar o seu ASE para operar num ambiente onde o tráfego de saída é enviado para o local, leia [Configure o seu ASE com túneis forçados][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista de endereços de gestão ##

| Region | Endereços |
|--------|-----------|
| Todas as regiões públicas | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.203.64, 191.236.154.88, 52.181.183.11 |
| Governo microsoft Azure | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Configurar um Grupo de Segurança de Rede

Com os Grupos de Segurança da Rede, não precisa de se preocupar com os endereços individuais ou com a manutenção da sua própria configuração. Existe uma etiqueta de serviço IP chamada AppServiceManagement que é mantida atualizada com todos os endereços. Para utilizar esta etiqueta de serviço IP no seu NSG, vá ao portal, abra o UI dos grupos de segurança da rede e selecione as regras de segurança de Entrada. Se tiver uma regra pré-existente para o tráfego de gestão de entrada, edite-a. Se este NSG não foi criado com o seu ASE, ou se é tudo novo, então selecione **Add**. Sob a gota de Origem, selecione **Tag de Serviço**.  Sob a etiqueta de serviço Source, selecione **AppServiceManagement**. Definir as gamas portuárias de origem para \* , Destino a **Qualquer**, Gamas portuárias de destino a **454-455,** Protocolo à **TCP,** e Ação a **Permitir**. Se estás a fazer a regra, então tens de definir a Prioridade. 

![criando um NSG com a etiqueta de serviço][1]

## <a name="configuring-a-route-table"></a>Configurar uma tabela de rotas

Os endereços de gestão podem ser colocados numa tabela de rotas com um próximo salto de internet para garantir que todo o tráfego de gestão de entrada é capaz de voltar pelo mesmo caminho. Estas rotas são necessárias quando configuram túneis forçados. Para criar a tabela de rotas, pode utilizar o portal, PowerShell ou Azure CLI.  Os comandos para criar uma tabela de rotas usando Azure CLI a partir de uma origem PowerShell estão abaixo. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.203.64", "191.236.154.88", "52.181.183.11"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Depois da sua tabela de rotas ser criada, tem de a definir na sua sub-rede ASE.  

## <a name="get-your-management-addresses-from-api"></a>Obtenha os seus endereços de gestão na API ##

Pode listar os endereços de gestão que correspondem ao seu ASE com a seguinte chamada API.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

A API devolve um documento JSON que inclui todos os endereços de entrada para o seu ASE. A lista de endereços inclui os endereços de gestão, o VIP utilizado pelo seu ASE e o próprio intervalo de endereços da sub-rede ASE.  

Para ligar para a API com o [braço,](https://github.com/projectkudu/ARMClient) utilize os seguintes comandos, mas substitua o ID de subscrição, o grupo de recursos e o nome ASE.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
