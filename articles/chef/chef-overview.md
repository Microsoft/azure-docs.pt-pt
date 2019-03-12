---
title: Utilizar o Chef com o Azure
description: Introdução à utilização do Chef para configurar e testar a sua infraestrutura do Azure
ms.service: virtual-machines-linux
keywords: Azure, chef, devops, máquinas virtuais, visão geral, automatizar
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 5c4e20177c1b334a34f7ce9328dfaa7bd8c66d3d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545740"
---
# <a name="using-chef-with-azure"></a>Utilizar o Chef com o Azure
[Chef](https://www.chef.io) é uma plataforma de automação poderoso que transforma a infraestrutura de máquina virtual no Azure em código. Chef automatiza como infraestrutura é configurada, implementada e gerenciada na sua rede, independentemente do seu tamanho.

Este artigo descreve as vantagens de utilizar o Chef para gerir a infraestrutura do Azure.

## <a name="chef-extension-on-azure"></a>Extensão do chef no Azure
Aprovisionar uma máquina virtual com o cliente do Chef em execução como um serviço em segundo plano com o [Chef extensão](https://docs.microsoft.com/azure/chef/chef-extension-portal) no Portal do Azure. Depois de aprovisionada, estas máquinas virtuais está prontas para ser gerido por um servidor de Chef.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Estação de trabalho de utilização Chef diretamente no Azure Cloud Shell! Execute todos os seus utilitários do Chef e da InSpec certo do Cloud Shell. Pode utilizar os comandos de Chef de:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combinar nosso utilitários de comando com as outras ferramentas disponíveis no Cloud Shell, tais como `git`, `az-cli`, e `terraform`e escrever a automação de infraestrutura e a compatibilidade do navegador.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatize a infraestrutura, aplicações e conformidade com uma plataforma
As empresas exigem a velocidade, a velocidade e a segurança para competir no mercado digital. Em conjunto Chef e a Microsoft ajudam a indivíduos, equipes e empresas realizar todas essas coisas. Com uma plataforma, Chef Automate, pode automatizar e fornecer continuamente sua infraestrutura, aplicativos e compatibilidade entre o seu património de Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test drive do Chef Automate no Azure
Suportado pelo Chef, o [solução Chef automatizar o Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) permite-lhe criar, implementar e gerir a sua infraestrutura e aplicações de forma colaborativa. Um único clique permite-lhe acesso instantâneo a todas as funcionalidades comerciais incluídas no Chef Automate; ganhar visibilidade de ponta a ponta em toda a sua frota, permite a conformidade contínua e gere todas as alterações com um fluxo de trabalho unificado.

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma máquina virtual do Windows no Azure utilizando o Chef](/azure/virtual-machines/windows/chef-automation)
