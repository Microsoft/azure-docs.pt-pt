---
title: Introdução com Cloud Foundry em Microsoft Azure | Microsoft Docs
description: Executar Cloud Foundry OSS ou Pivotal no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d91ad0bea7f79dd67edd4f0bb9e06a37a0f86bea
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091929"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

O Cloud Foundry é uma plataforma como serviço (PaaS) de código aberto que permite criar, implementar e utilizar aplicações de 12 fatores programadas em diferentes linguagens e arquiteturas. Este documento descreve as opções que você tem para executar o Cloud Foundry no Azure e como você pode começar.

## <a name="cloud-foundry-offerings"></a>Ofertas de Cloud Foundry

Há duas formas de Cloud Foundry disponíveis para serem executadas no Azure: Cloud Foundry de código-fonte aberto (OSS CF) e PCF (Cloud Foundry dinâmico). O CF do OSS é uma versão totalmente de [código aberto](https://github.com/cloudfoundry) do Cloud Foundry gerenciado pelo Cloud Foundry Foundation. A Cloud Foundry dinâmica é uma distribuição empresarial de Cloud Foundry da Pivotal Software Inc. Vamos examinar algumas das diferenças entre as duas ofertas.

### <a name="open-source-cloud-foundry"></a>Cloud Foundry de código-fonte aberto

Você pode implantar o OSS Cloud Foundry no Azure implantando primeiro um diretor de BOSH e, em seguida, implantando Cloud Foundry, usando as [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre como usar o CF do OSS, consulte a [documentação](https://docs.cloudfoundry.org/) fornecida pelo Cloud Foundry Foundation.

A Microsoft fornece suporte de melhor esforço para o CF OSS por meio dos seguintes canais de comunidade:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>Bosh-canal Azure-CPI na [margem de atraso Cloud Foundry](https://slack.cloudfoundry.org/)
- [lista de endereçamento do CF-Bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas do GitHub para o [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e o [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os recursos do Azure, como as máquinas virtuais em que você executa Cloud Foundry, é baseado em seu contrato de suporte do Azure. O suporte da comunidade de melhor esforço se aplica apenas aos componentes específicos do Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

A Cloud Foundry dinâmica inclui a mesma plataforma principal que a distribuição de OSS, juntamente com um conjunto de ferramentas de gerenciamento de propriedade e suporte empresarial. Para executar o PCF no Azure, você deve adquirir uma licença da Pivotal. A oferta de PCF do Azure Marketplace inclui uma licença de avaliação de 90 dias.

As ferramentas incluem o [pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), um aplicativo Web que simplifica a implantação e o gerenciamento de um Cloud Foundry Foundation e do [Pivotal apps Manager](https://docs.pivotal.io/pivotalcf/console/), um aplicativo Web para gerenciar usuários e aplicativos.

Além dos canais de suporte listados para o CF do OSS acima, uma licença PCF o autoriza a entrar em contato com a dinamização para obter suporte. A Microsoft e a Pivotal também habilitaram o suporte a fluxos de trabalho que permitem que você entre em contato com uma das partes para obter assistência e que sua consulta seja roteada adequadamente dependendo de onde está o problema.

## <a name="azure-service-broker"></a>Service Broker do Azure

Cloud Foundry incentiva a metodologia ["aplicativo de doze fatores"](https://12factor.net/) , que promove uma separação limpa de processos de aplicativos sem estado e serviços de backup com estado. Os [agentes de serviço](https://docs.cloudfoundry.org/services/api.html) oferecem uma maneira consistente de provisionar e ligar serviços de backup a aplicativos. O [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos principais serviços do Azure por meio desse canal, incluindo o armazenamento do Azure e o SQL do Azure.

Se você estiver usando a Cloud Foundry dinâmica, o Service Broker também estará [disponível como um bloco](https://docs.pivotal.io/azure-sb/installing.html) da rede dinâmica.

## <a name="related-resources"></a>Recursos relacionados

### <a name="azure-devops-services-plugin"></a>Plug-in Azure DevOps Services

Cloud Foundry é adequado para o desenvolvimento de software ágil, incluindo o uso de CI (integração contínua) e o CD (entrega contínua). Se você usar Azure DevOps Services para gerenciar seus projetos e desejar configurar um pipeline de CI/CD direcionado Cloud Foundry, você poderá usar a [extensão de Build de Azure DevOps Services Cloud Foundry](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). O plug-in torna simples configurar e automatizar implantações para Cloud Foundry, seja em execução no Azure ou em outro ambiente.

## <a name="next-steps"></a>Passos Seguintes

- [Implantar Cloud Foundry dinâmicos do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Implantar um aplicativo para Cloud Foundry no Azure](./cloudfoundry-deploy-your-first-app.md)
