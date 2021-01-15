---
title: Integração de Gateway de Aplicações com pontos finais de serviço - Azure App Service / Microsoft Docs
description: Descreve como o Application Gateway se integra com o Azure App Service protegido com pontos finais de serviço.
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
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209874"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integração de Gateway de Aplicação com pontos finais de serviço
Existem três variações do Serviço de Aplicações que requerem uma configuração ligeiramente diferente da integração com o Azure Application Gateway. As variações incluem o Serviço regular de Aplicações - também conhecido como multi-inquilino, Internal Load Balancer (ILB) App Service Environment (ASE) e ASE Externo. Este artigo irá percorrer como configurá-lo com o App Service (multi-inquilino) e discutir considerações sobre iLB, e ASE Externo.

## <a name="integration-with-app-service-multi-tenant"></a>Integração com Serviço de Aplicações (multi-inquilino)
O App Service (multi-inquilino) tem um ponto final virado para a internet pública. Utilizando [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md) de serviço, pode permitir o tráfego apenas a partir de uma sub-rede específica dentro de uma Rede Virtual Azure e bloquear tudo o resto. No cenário seguinte, usaremos esta funcionalidade para garantir que uma instância do Serviço de Aplicações apenas pode receber tráfego de uma instância específica do Gateway de aplicações.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="O diagrama mostra a Internet a fluir para um Gateway de aplicações numa Rede Virtual Azure e fluindo de lá através de um ícone de firewall para instâncias de aplicações no Serviço de Aplicações.":::

Existem duas partes para esta configuração além de criar o Serviço de Aplicações e o Gateway de Aplicações. A primeira parte é permitir pontos finais de serviço na sub-rede da Rede Virtual onde o Gateway de aplicação é implantado. Os pontos finais de serviço garantirão que todo o tráfego de rede que sai da sub-rede em direção ao Serviço de Aplicações será marcado com o ID específico da sub-rede. A segunda parte é definir uma restrição de acesso da aplicação web específica para garantir que apenas o tráfego marcado com este ID específico da sub-rede é permitido. Pode configugá-lo utilizando diferentes ferramentas, dependendo da preferência.

## <a name="using-azure-portal"></a>Com o Portal do Azure
Com o portal Azure, segue quatro passos para a disposição e configuração da configuração. Se tiver recursos existentes, pode saltar os primeiros passos.
1. Criar um Serviço de Aplicações utilizando um dos Quickstarts na documentação do Serviço de Aplicações, por [exemplo.NET Core Quickstart](../quickstart-dotnetcore.md)
2. Crie um Gateway de Aplicação utilizando o [portal Quickstart,](../../application-gateway/quick-create-portal.md)mas ignore a secção de alvos de backend Add.
3. Configure o [Serviço de Aplicações como backend no Gateway de Aplicações,](../../application-gateway/configure-web-app-portal.md)mas ignore a secção de acesso Restrict.
4. Finalmente criar a restrição de [acesso utilizando pontos finais de serviço](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Agora pode aceder ao Serviço de Aplicações através do Application Gateway, mas se tentar aceder diretamente ao Serviço de Aplicações, deverá receber um erro 403 HTTP indicando que o site está parado.

![A screenshot mostra o texto de um Erro 403 - Proibido.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager
O [modelo de implementação do Gestor de Recursos][template-app-gateway-app-service-complete] irá prever um cenário completo. O cenário consiste numa instância do Serviço de Aplicações bloqueada com pontos finais de serviço e restrição de acesso para receber apenas tráfego a partir do Gateway de Aplicações. O modelo inclui muitos Smart Defaults e postfixes únicos adicionados aos nomes de recursos para que seja simples. Para os anular, terá de clonar o repo ou descarregar o modelo e editá-lo. 

Para aplicar o modelo pode utilizar o botão Implementar para Azure encontrado na descrição do modelo, ou pode utilizar o PowerShell/CLI apropriado.

## <a name="using-azure-command-line-interface"></a>Usando interface de linha de comando Azure
A [amostra Azure CLI](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) irá providenciar um Serviço de Aplicações bloqueado com pontos finais de serviço e restrição de acesso para apenas receber tráfego a partir de App Gateway. Se apenas precisar de isolar o tráfego para um Serviço de Aplicações existente a partir de um Gateway de aplicações existente, o seguinte comando é suficiente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Na configuração predefinição, o comando garantirá tanto a configuração da configuração do ponto final de serviço na sub-rede como a restrição de acesso no Serviço de Aplicações.

## <a name="considerations-for-ilb-ase"></a>Considerações para o ILB ASE
O ILB ASE não está exposto à internet e o tráfego entre o caso e um Gateway de aplicação já está isolado para a Rede Virtual. O seguinte [guia](../environment/integrate-with-application-gateway.md) configura um ILB ASE e integra-o com um Gateway de aplicação utilizando o portal Azure. 

Se quiser garantir que apenas o tráfego da sub-rede Do Gateway de Aplicação está a chegar à ASE, pode configurar um grupo de segurança da Rede (NSG) que afeta todas as aplicações web na ASE. Para o NSG, é possível especificar a gama IP da sub-rede e opcionalmente as portas (80/443). Certifique-se de que não substitui as [regras NSG necessárias](../environment/network-info.md#network-security-groups) para que a ASE funcione corretamente.

Para isolar o tráfego de uma aplicação web individual, terá de utilizar restrições de acesso baseadas em IP, uma vez que os pontos finais de serviço não funcionarão para o ASE. O endereço IP deve ser o IP privado da instância Gateway de aplicação.

## <a name="considerations-for-external-ase"></a>Considerações para asE externa
A ASE externa tem um equilibrador de carga virado para o público, como o Serviço de Aplicações multi-arrendatário. Os pontos finais de serviço não funcionam para o ASE, e é por isso que terá de usar restrições de acesso baseadas em IP usando o IP público da instância Gateway de aplicações. Para criar um ASE externo utilizando o portal Azure, pode seguir este [Quickstart](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modelo de gestor de recursos Azure para cenário completo"

## <a name="considerations-for-kuduscm-site"></a>Considerações para o site kudu/scm
O site scm, também conhecido como kudu, é um site de administração, que existe para cada aplicação web. Não é possível reverter o site scm e provavelmente também pretende bloqueá-lo em endereços IP individuais ou numa sub-rede específica.

Se pretender utilizar as mesmas restrições de acesso que o site principal, pode herdar as definições utilizando o seguinte comando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se quiser definir restrições de acesso individuais para o site scm, pode adicionar restrições de acesso usando a bandeira do site -scm como mostrado abaixo.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Ambiente do Serviço de Aplicações, consulte [a documentação do App Service Environment](/azure/app-service/environment).

Para garantir ainda mais a sua aplicação web, informações sobre firewall de aplicação web no Gateway de aplicações podem ser encontradas na [documentação Azure Web Application Firewall](../../web-application-firewall/ag/ag-overview.md).
