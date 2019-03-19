---
title: Introdução ao Cloud Foundry no Microsoft Azure | Documentos da Microsoft
description: Execute sistemas operacionais ou Pivotal Cloud Foundry no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 68ae01b814de08098c0ba6b5713f420cfebc3d97
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001253"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

O Cloud Foundry é uma plataforma como serviço (PaaS) de código aberto que permite criar, implementar e utilizar aplicações de 12 fatores programadas em diferentes linguagens e arquiteturas. Este documento descreve as opções que existem para executar o Cloud Foundry no Azure e como pode começar a utilizar.

## <a name="cloud-foundry-offerings"></a>Ofertas do cloud Foundry

Existem duas formas de Cloud Foundry disponível para ser executada no Azure: Cloud Foundry (OSS CF) e a Pivotal Cloud Foundry (PCF) de código-fonte aberto. CF de sistemas operacionais é um inteiramente [aberto](https://github.com/cloudfoundry) versão do Cloud Foundry geridos à Cloud Foundry Foundation. Pivotal Cloud Foundry é uma distribuição empresarial de Cloud Foundry da Pivotal Software Inc. Vamos examinar algumas das diferenças entre as duas ofertas.

### <a name="open-source-cloud-foundry"></a>Código-fonte aberto Cloud Foundry

Pode implementar OSS Cloud Foundry no Azure ao implementar primeiro um diretor BOSH e, em seguida, implementar o Cloud Foundry, utilizando o [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre como utilizar CF de sistemas operacionais, consulte a [documentação](https://docs.cloudfoundry.org/) fornecida pela Cloud Foundry Foundation.

A Microsoft fornece suporte de melhor esforço para sistemas operacionais CF através dos seguintes canais de Comunidade:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>canal de bosh-azure-cpi no [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [lista de correio cf bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas do GitHub para o [medição de CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e [Mediador de serviço](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os seus recursos do Azure, tais como as máquinas virtuais em que executou o Cloud Foundry, baseia-se no seu contrato de suporte do Azure. Suporte da Comunidade de melhor esforço só se aplica aos componentes específicos do Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Cloud Foundry da Pivotal inclui a plataforma principal do mesmo como a distribuição de sistemas operacionais, juntamente com um conjunto de ferramentas de proprietário de gestão e suporte empresarial. Para executar o PCF no Azure, tem de adquirir uma licença da Pivotal. A oferta PCF no Azure marketplace inclui uma licença de avaliação de 90 dias.

As ferramentas incluem [Pivotal do Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), uma aplicação web que simplifica a implementação e gestão de uma base de Cloud Foundry, e [Gestor de aplicações Pivotal](https://docs.pivotal.io/pivotalcf/console/), uma aplicação web para gerir os utilizadores e aplicações.

Além dos canais de suporte listados para sistemas operacionais CF acima, uma licença do PCF dá direito a contacte Pivotal para obter suporte. Microsoft e a Pivotal também tem ativado o suporte a fluxos de trabalho que permitem que entre em contato com qualquer uma das partes para obter assistência e tenha sua pergunta devidamente encaminhada dependendo de onde está o problema.

## <a name="azure-service-broker"></a>Mediador de serviço do Azure

Cloud Foundry incentiva a ["aplicação de fatores de doze"](https://12factor.net/) metodologia, que promove uma separação clara entre processos de aplicativos sem monitoração de estado e com monitorização de estado dos serviços de segurança. [Mediadores de serviço](https://docs.cloudfoundry.org/services/api.html) oferecem uma forma consistente para aprovisionar e ligar a serviços de apoio às aplicações. O [Mediador de serviço do Azure](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos principais serviços do Azure através deste canal, incluindo o armazenamento do Azure e SQL do Azure.

Se estiver a utilizar o Cloud Foundry da Pivotal, o service broker é também [disponível como um mosaico](https://docs.pivotal.io/azure-sb/installing.html) da Pivotal rede.

## <a name="related-resources"></a>Recursos relacionados

### <a name="azure-devops-services-plugin"></a>Plug-in Serviços de DevOps do Azure

Cloud Foundry é ideal para desenvolvimento de software ágil, incluindo a utilização da integração contínua (CI) e a entrega contínua (CD). Se utilizar os serviços de DevOps do Azure para gerir os seus projetos e gostaria de definir a CI/CD do pipeline direcionamento Cloud Foundry, pode utilizar o [do Azure DevOps serviços Cloud Foundry crie extensão](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). O plug-in simplifica a configurar e automatizar as implementações para o Cloud Foundry, independentemente de ser executados no Azure ou noutro ambiente.

## <a name="next-steps"></a>Passos Seguintes

- [Implementar a Pivotal Cloud Foundry no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Implementar uma aplicação para a Cloud Foundry no Azure](./cloudfoundry-deploy-your-first-app.md)
