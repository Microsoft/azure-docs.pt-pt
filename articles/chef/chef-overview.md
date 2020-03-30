---
title: Usando chef com Azure
description: Introdução ao uso do Chef para configurar e testar a sua infraestrutura Azure
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatização
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586347"
---
# <a name="using-chef-with-azure"></a>Usando chef com Azure
[Chef](https://www.chef.io) é uma poderosa plataforma de automação que transforma a infraestrutura de máquinas virtuais em Azure em código. Chef automatiza como a infraestrutura é configurada, implantada e gerida em toda a sua rede, independentemente do seu tamanho.

Este artigo descreve os benefícios de usar o Chef para gerir a infraestrutura azure.

## <a name="chef-extension-on-azure"></a>Extensão do Chef em Azure
Provisão de uma máquina virtual com Chef Client correndo como um serviço de fundo com a [Extensão do Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) no [portal Azure.](https://go.microsoft.com/fwlink/p/?LinkID=525040) Uma vez aprovisionados, estas máquinas virtuais estão prontas para serem geridas por um servidor chef.

## <a name="chef-cloud-shell"></a>Concha de nuvem de chef
Use chef workstation diretamente em Azure Cloud Shell! Gere todas as suas utilidades chef e InSpec direto da Cloud Shell. Você pode usar os comandos do Chef de:

* [chef](https://docs.chef.io/ctl_chef.html)
* [cozinha](https://docs.chef.io/ctl_kitchen.html)
* [inspeção](https://www.inspec.io/docs/reference/cli/)
* [faca](https://docs.chef.io/knife.html)
* [estilo cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combine os nossos utilitários de comando com `git`as `az-cli`outras ferramentas disponíveis na Cloud Shell, tais como , e `terraform`escreva a sua infraestrutura e automatização de conformidade a partir do navegador.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automate infraestrutura, apps e conformidade com uma plataforma
As empresas exigem velocidade, velocidade e segurança para competir no mercado digital. Juntos Chef e Microsoft ajudam indivíduos, equipas e empresas a realizar todas estas coisas. Com uma plataforma, o Chef Automate, agora pode automatizar e entregar continuamente a sua infraestrutura, aplicações e conformidade em toda a sua propriedade da Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test drive Chef Automate em Azure
Apoiada pelo Chef, a [solução Chef Automate Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) permite-lhe construir, implementar e gerir a sua infraestrutura e aplicações colaborativamente. Um clique dá-lhe acesso instantâneo a todas as funcionalidades comerciais incluídas no Chef Automate; ganhar visibilidade de ponta a ponta em toda a sua frota, permitir o cumprimento contínuo, e gerir todas as mudanças com um fluxo de trabalho unificado.

## <a name="next-steps"></a>Passos seguintes

* [Crie uma máquina virtual Windows em Azure usando chef](chef-automation.md)
