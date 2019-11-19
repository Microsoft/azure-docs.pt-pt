---
title: Usando o chefe com o Azure
description: Introdução ao uso do chefe para configurar e testar sua infraestrutura do Azure
keywords: Azure, chefe, DevOps, máquinas virtuais, visão geral, automatizar
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158004"
---
# <a name="using-chef-with-azure"></a>Usando o chefe com o Azure
[Chefe](https://www.chef.io) é uma poderosa plataforma de automação que transforma a infraestrutura de máquina virtual no Azure em código. A chefe automatiza como a infraestrutura é configurada, implantada e gerenciada em toda a sua rede, independentemente do seu tamanho.

Este artigo descreve os benefícios de usar o chefe para gerenciar a infraestrutura do Azure.

## <a name="chef-extension-on-azure"></a>Extensão do chefe no Azure
Provisione uma máquina virtual com o cliente chefe em execução como um serviço em segundo plano com a [extensão chefe](https://docs.microsoft.com/azure/chef/chef-extension-portal) no portal do Azure. Depois de provisionado, essas máquinas virtuais estão prontas para serem gerenciadas por um servidor chefe.

## <a name="chef-cloud-shell"></a>Cloud Shell chefe
Use a estação de trabalho chefe diretamente no Azure Cloud Shell! Execute todos os seus utilitários chefe e inspecs diretamente de Cloud Shell. Você pode utilizar os comandos chefe de:

* [chef](https://docs.chef.io/ctl_chef.html)
* [estrelas](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [lâmina](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combine nossos utilitários de comando com as outras ferramentas disponíveis em Cloud Shell, como `git`, `az-cli`e `terraform`, e escreva sua infraestrutura e automação de conformidade do navegador.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatize a infraestrutura, os aplicativos e a conformidade com uma plataforma
Empresas exigem velocidade, velocidade e segurança para competir no mercado digital. Juntos, o chefe e a Microsoft ajudam pessoas, equipes e empresas a realizar todas essas coisas. Com uma plataforma, a chefe automatizada, agora você pode automatizar e fornecer continuamente sua infraestrutura, seus aplicativos e conformidade em todo o seu espaço da Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test Drive da chefe automatizado no Azure
Com suporte do chefe, a [solução chefe automatizada do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) permite que você crie, implante e gerencie sua infraestrutura e aplicativos de forma colaborativa. Um clique obtém acesso instantâneo a todos os recursos comerciais incluídos com o chefe Automate; obter visibilidade de ponta a ponta em toda a frota, habilitar a conformidade contínua e gerenciar todas as alterações com um fluxo de trabalho unificado.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma máquina virtual do Windows no Azure usando o chefe](/azure/virtual-machines/windows/chef-automation)
