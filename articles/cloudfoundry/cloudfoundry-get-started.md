---
title: Começar com Cloud Foundry no Microsoft Azure
description: Executar OSS ou Pivotal Cloud Foundry no Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d3b13dffcf251f79a04182d32f23fd40e6ed6044
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562899"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

O Cloud Foundry é uma plataforma como serviço (PaaS) de código aberto que permite criar, implementar e utilizar aplicações de 12 fatores programadas em diferentes linguagens e arquiteturas. Este documento descreve as opções que tem para executar Cloud Foundry no Azure e como pode começar.

## <a name="cloud-foundry-offerings"></a>Ofertas de Cloud Foundry

Existem duas formas de Cloud Foundry disponíveis para executar no Azure: Cloud Foundry (OSS CF) e Pivotal Cloud Foundry (PCF). O OSS CF é uma versão totalmente [aberta](https://github.com/cloudfoundry) da Cloud Foundry gerida pela Cloud Foundry Foundation. Pivotal Cloud Foundry é uma distribuição empresarial da Cloud Foundry da Pivotal Software Inc. Olhamos para algumas das diferenças entre as duas ofertas.

### <a name="open-source-cloud-foundry"></a>Fundição de nuvem de código aberto

Pode implementar a OsS Cloud Foundry no Azure, implantando primeiro um diretor BOSH e, em seguida, implantando cloud foundry, utilizando as [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre a utilização do OSS CF, consulte a [documentação](https://docs.cloudfoundry.org/) fornecida pela Cloud Foundry Foundation.

A Microsoft fornece o melhor suporte de esforço para o OSS CF através dos seguintes canais comunitários:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>canal bosh-azure-cpi em [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [lista de correio cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas do GitHub para o [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e [corretor de serviços](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os seus recursos Azure, como as máquinas virtuais onde gere a Cloud Foundry, baseia-se no seu acordo de suporte Azure. O melhor suporte comunitário aplica-se apenas aos componentes específicos da Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Fundamental Cloud Foundry

A Pivotal Cloud Foundry inclui a mesma plataforma central que a distribuição do OSS, juntamente com um conjunto de ferramentas de gestão proprietária e suporte empresarial. Para executar o PCF na Azure, tem de adquirir uma licença da Pivotal. A oferta de PCF do mercado Azure inclui uma licença de teste de 90 dias.

As ferramentas incluem [o Pivotal Operations Manager,](https://docs.pivotal.io/pivotalcf/customizing/)uma aplicação web que simplifica a implementação e gestão de uma fundação Cloud Foundry, e [o Pivotal Apps Manager,](https://docs.pivotal.io/pivotalcf/console/)uma aplicação web para gestão de utilizadores e aplicações.

Além dos canais de suporte listados para o OSS CF acima, uma licença pcf dá-lhe o direito de contactar a Pivotal para obter apoio. A Microsoft e a Pivotal também ativaram fluxos de trabalho de suporte que lhe permitem contactar qualquer uma das partes para assistência e ter o seu inquérito encaminhado adequadamente dependendo do local onde o problema está.

## <a name="azure-service-broker"></a>Corretor de Serviços Azure

A Cloud Foundry incentiva a metodologia ["app de doze fatores",](https://12factor.net/) que promove uma separação limpa dos processos de aplicação apátrida e dos serviços de apoio estatal. [Os corretores de serviços](https://docs.cloudfoundry.org/services/api.html) oferecem uma forma consistente de prestação e ligam os serviços de apoio às aplicações. O corretor de [serviços Azure](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos principais serviços Azure através deste canal, incluindo o armazenamento Azure e Azure SQL.

Se estiver a utilizar o Pivotal Cloud Foundry, o corretor de serviços também está [disponível como azulejo](https://docs.pivotal.io/azure-sb/installing.html) da Rede Pivotal.

## <a name="related-resources"></a>Recursos relacionados

### <a name="azure-devops-services-plugin"></a>Plugin de serviços Azure DevOps

O Cloud Foundry é adequado para o desenvolvimento ágil de software, incluindo o uso de integração contínua (CI) e entrega contínua (CD). Se utilizar o Azure DevOps Services para gerir os seus projetos e pretender configurar um oleoduto CI/CD direcionado para cloud foundry, pode utilizar a [extensão de construção de cloud foundry de serviços Azure DevOps.](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension) O plugin torna simples configurar e automatizar implementações para cloud foundry, quer esteja a funcionar em Azure ou noutro ambiente.

## <a name="next-steps"></a>Passos seguintes

- [Implementar a Configuração da Nuvem Pivotal do Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Implementar uma aplicação para Cloud Foundry em Azure](./cloudfoundry-deploy-your-first-app.md)
