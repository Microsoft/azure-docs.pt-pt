---
title: Começando com cloud foundry no Microsoft Azure
description: Executar o OSS ou a Funddição de Nuvem Pivotal no Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 8645afac5734cdbffb8df6f341529e688ca526d7
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891631"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

O Cloud Foundry é uma plataforma como serviço (PaaS) de código aberto que permite criar, implementar e utilizar aplicações de 12 fatores programadas em diferentes linguagens e arquiteturas. Este documento descreve as opções que tem para executar cloud foundry no Azure e como pode começar.

## <a name="cloud-foundry-offerings"></a>Ofertas de Foundry cloud

Existem duas formas de Cloud Foundry disponíveis para funcionar em Azure: cloud foundry de código aberto (OSS CF) e Fundição de Nuvem Pivotal (PCF). OSS CF é uma versão inteiramente [aberta](https://github.com/cloudfoundry) da Cloud Foundry gerida pela Cloud Foundry Foundation. Pivotal Cloud Foundry é uma distribuição empresarial da Cloud Foundry da Pivotal Software Inc. Olhamos para algumas das diferenças entre as duas ofertas.

### <a name="open-source-cloud-foundry"></a>Fundição de nuvem de código aberto

Pode implantar a OSS Cloud Foundry no Azure, implantando primeiro um diretor BOSH e, em seguida, implantando a Cloud Foundry, utilizando as [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre a utilização do OSS CF, consulte a [documentação](https://docs.cloudfoundry.org/) fornecida pela Cloud Foundry Foundation.

A Microsoft fornece o melhor suporte para o OSS CF através dos seguintes canais comunitários:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>canal bosh-azure-cpi na [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [lista de correspondência cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas gitHub para o [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e corretor de [serviços](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os seus recursos Azure, como as máquinas virtuais onde gere a Cloud Foundry, baseia-se no seu acordo de suporte Azure. O apoio comunitário de melhor esforço aplica-se apenas aos componentes específicos da Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Fundaria de Nuvem Pivotal

A Pivotal Cloud Foundry inclui a mesma plataforma central que a distribuição do OSS, juntamente com um conjunto de ferramentas de gestão proprietária e suporte empresarial. Para executar pcf no Azure, você deve adquirir uma licença da Pivotal. A oferta de PCF do mercado Azure inclui uma licença experimental de 90 dias.

As ferramentas incluem [o Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), uma aplicação web que simplifica a implementação e gestão de uma fundação Cloud Foundry, e [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/), uma aplicação web para gestão de utilizadores e aplicações.

Além dos canais de suporte listados para o OSS CF acima, uma licença PCF dá-lhe direito a contactar a Pivotal para obter suporte. A Microsoft e a Pivotal também permitiram suporte saem que lhe permitem contactar qualquer uma das partes para obter assistência e ter o seu inquérito encaminhado adequadamente dependendo do local onde o problema está.

## <a name="azure-service-broker"></a>Corretor de Serviços Azure

A Cloud Foundry encoraja a metodologia ["app de doze fatores",](https://12factor.net/) que promove uma separação limpa dos processos de aplicação apátridas e dos serviços de apoio apátridas. [Os corretores](https://docs.cloudfoundry.org/services/api.html) de serviços oferecem uma forma consistente de fornecer e ligar serviços de apoio a aplicações. O corretor de [serviços Azure](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos principais serviços Azure através deste canal, incluindo o armazenamento Azure e o Azure SQL.

Se estiver a utilizar a Pivotal Cloud Foundry, o corretor de serviços também está [disponível como azulejo](https://docs.pivotal.io/azure-sb/installing.html) da Rede Pivotal.

## <a name="related-resources"></a>Recursos relacionados

### <a name="azure-devops-services-plugin"></a>Plugin azure DevOps Services

A Cloud Foundry é adequada ao desenvolvimento ágil do software, incluindo o uso de integração contínua (CI) e entrega contínua (CD). Se utilizar os Serviços Azure DevOps para gerir os seus projetos e quiser configurar um pipeline CI/CD direcionado para a Cloud Foundry, pode utilizar a extensão de construção de cloud foundry de [serviços Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). O plugin torna simples configurar e automatizar implementações para cloud foundry, seja em execução em Azure ou em outro ambiente.

## <a name="next-steps"></a>Passos seguintes

- [Implementar a fundação de nuvem fundamental do Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Implementar uma aplicação para Cloud Foundry em Azure](./cloudfoundry-deploy-your-first-app.md)
