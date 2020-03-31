---
title: Integração de Gateway de aplicações com pontos finais de serviço - Serviço de Aplicações Azure / Microsoft Docs
description: Descreve como o Application Gateway se integra com o Serviço de Aplicações Azure protegido com pontos finais de serviço.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980065"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integração de Gateway de aplicação com pontos finais de serviço
Existem três variações do Serviço de Aplicações que requerem uma configuração ligeiramente diferente da integração com o Portal de Aplicações Azure. As variações incluem o Serviço regular de Aplicações - também conhecido como multi-inquilino, Internal Load Balancer (ILB) App Service Environment (ASE) e ASE Externa. Este artigo irá analisar como configurá-lo com o App Service (multi-inquilino) e discutir considerações sobre iLB, e ASE Externa.

## <a name="integration-with-app-service-multi-tenant"></a>Integração com o App Service (multi-inquilino)
O App Service (multi-inquilino) tem um ponto final virado para a internet pública. Utilizando [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md) de serviço, só pode permitir o tráfego a partir de uma subrede específica dentro de uma Rede Virtual Azure e bloquear tudo o resto. No seguinte cenário, usaremos esta funcionalidade para garantir que uma instância do Serviço de Aplicações só pode receber tráfego a partir de uma instância específica do Gateway de aplicações.

![Integração de Gateway de aplicações com serviço de aplicações](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Existem duas partes nesta configuração além de criar o Serviço de Aplicações e o Gateway de Aplicações. A primeira parte é permitir pontos finais de serviço na subnet da Rede Virtual onde o Gateway de Aplicação é implementado. Os pontos finais do serviço garantirão que todo o tráfego de rede que saia da subnet para o Serviço de Aplicações será marcado com o ID da sub-rede específico. A segunda parte é definir uma restrição de acesso da aplicação web específica para garantir que apenas o tráfego marcado com este ID de sub-rede específico é permitido. Pode configurá-lo utilizando diferentes ferramentas dependendo da preferência.

## <a name="using-azure-portal"></a>Com o Portal do Azure
Com o portal Azure, siga quatro passos para fornecer e configurar a configuração. Se tiver recursos existentes, pode saltar os primeiros passos.
1. Criar um Serviço de Aplicações utilizando um dos Quickstarts na documentação do Serviço de Aplicações, por [exemplo.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Crie um Gateway de Aplicação utilizando o [portal Quickstart,](../../application-gateway/quick-create-portal.md)mas ignore a secção de alvos add end.
3. Configure o Serviço de [Aplicações como um backend no Gateway de Aplicações,](../../application-gateway/configure-web-app-portal.md)mas ignore a secção de acesso restrito.
4. Finalmente crie a restrição de [acesso utilizando pontos finais](../../app-service/app-service-ip-restrictions.md#service-endpoints)de serviço .

Agora pode aceder ao Serviço de Aplicações através do Gateway de Aplicações, mas se tentar aceder diretamente ao Serviço de Aplicações, deverá receber um erro de 403 HTTP indicando que o web site está parado.

![Integração de Gateway de aplicações com serviço de aplicações](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
O modelo de [implementação do Gestor][template-app-gateway-app-service-complete] de Recursos fornecerá um cenário completo. O cenário consiste numa instância do Serviço de Aplicações bloqueada com pontos finais de serviço e restrição de acesso para apenas receber tráfego a partir do Gateway de aplicação. O modelo inclui muitos Prenúis Inteligentes e fixos postais únicos adicionados aos nomes de recursos para que seja simples. Para os anular, terá de clonar o repo ou baixar o modelo e editá-lo. 

Para aplicar o modelo pode utilizar o botão Deploy para Azure encontrado na descrição do modelo, ou pode utilizar powerShell/CLI apropriado.

## <a name="using-azure-command-line-interface"></a>Usando interface de linha de comando Azure
A [amostra Azure CLI](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) fornecerá um Serviço de Aplicações bloqueado com pontos finais de serviço e restrição de acesso para apenas receber tráfego a partir do Gateway de aplicação. Se necessitar apenas de isolar o tráfego de um serviço de aplicações existente a partir de um Gateway de Aplicações existente, o seguinte comando é suficiente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Na configuração predefinida, o comando garantirá a configuração do ponto final do serviço na sub-rede e a restrição de acesso no Serviço de Aplicações.

## <a name="considerations-for-ilb-ase"></a>Considerações para a ILB ASE
O ILB ASE não está exposto à internet e o tráfego entre a instância e um Gateway de aplicação já está isolado na Rede Virtual. O seguinte [guia](../environment/integrate-with-application-gateway.md) configura um ILB ASE e integra-o com um Gateway de Aplicação utilizando o portal Azure. 

Se pretender garantir que apenas o tráfego a partir da subnet Application Gateway está a chegar à ASE, pode configurar um grupo de segurança de rede (NSG) que afeta todas as aplicações web na ASE. Para o NSG, é possível especificar a gama IP da sub-rede e opcionalmente as portas (80/443). Certifique-se de que não anula as [regras de NSG necessárias](../environment/network-info.md#network-security-groups) para que a ASE funcione corretamente.

Para isolar o tráfego de uma aplicação web individual, terá de utilizar restrições de acesso baseadas em IP, uma vez que os pontos finais do serviço não funcionarão para a ASE. O endereço IP deve ser o IP privado da instância De entrada de aplicações.

## <a name="considerations-for-external-ase"></a>Considerações para a ASE Externa
A ASE externa tem um equilibrador de carga virado para o público como o Serviço de Aplicações multi-inquilinos. Os pontos finais do serviço não funcionam para a ASE, e é por isso que terá de usar restrições de acesso baseadas em IP utilizando o IP público da instância de Gateway de Aplicação. Para criar uma ASE Externa utilizando o portal Azure, pode seguir este [Quickstart](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modelo de Gestor de Recursos Azure para cenário completo"

## <a name="considerations-for-kuduscm-site"></a>Considerações para o site kudu/scm
O site Scm, também conhecido como kudu, é um site de administração, que existe para cada aplicação web. Não é possível inverter o proxy do site scm e provavelmente também quer bloqueá-lo em endereços IP individuais ou uma sub-rede específica.

Se pretender utilizar as mesmas restrições de acesso que o site principal, pode herdar as definições utilizando o seguinte comando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se pretender definir restrições de acesso individuais para o site scm, pode adicionar restrições de acesso utilizando a bandeira do site --scm como mostrado abaixo.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Ambiente do Serviço de Aplicações, consulte a [documentação do Ambiente do Serviço de Aplicações.](https://docs.microsoft.com/azure/app-service/environment)

Para garantir ainda mais a sua aplicação web, as informações sobre o Firewall da Aplicação Web no Gateway de Aplicações podem ser encontradas na documentação de Firewall da [Aplicação Web do Azure](../../web-application-firewall/ag/ag-overview.md).