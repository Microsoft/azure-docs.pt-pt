---
title: Descrição geral das aplicações geridas do Azure | Microsoft Docs
description: Descreve os conceitos das aplicações geridas do Azure
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: da6c9bb6cbd94ab5078641f25e42ad2203ff7a53
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402359"
---
# <a name="azure-managed-applications-overview"></a>Descrição geral das aplicações geridas do Azure

As aplicações geridas do Azure permitem-lhe oferecer soluções na cloud que os consumidores podem implementar e operar facilmente. Tem de implementar a infraestrutura e disponibilizar suporte contínuo. Para disponibilizar uma aplicação gerida para todos os clientes, publique-a no Azure Marketplace. Para disponibilizá-la apenas aos utilizadores da sua organização, publique-a num catálogo interno. 

As aplicações geridas são semelhantes aos modelos de soluções no Marketplace, com uma diferença essencial. Numa aplicação gerida, os recursos são implementados num grupo de recursos que é gerido pelo editor da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do editor tem acesso ao grupo de recursos. Como editor, tem de especificar o custo do suporte contínuo da solução.

## <a name="advantages-of-managed-applications"></a>Vantagens das aplicações geridas

As aplicações geridas reduzem barreiras aos consumidores que utilizam as suas soluções. Aqueles não precisam de conhecimentos técnicos sobre a infraestrutura na cloud para utilizar a sua solução. Os consumidores têm acesso limitado aos recursos críticos, por isso, não se preocupe se cometer um erro quando os gerir. 

As aplicações geridas permitem-lhe estabelecer uma relação contínua com os seus consumidores. Os termos para gerir a aplicação são definidos por si e todos os custos são processados através da faturação do Azure.

Embora os clientes implementem essas aplicações geridas nas respetivas subscrições, não têm de manter, atualizar ou repará-las. Pode confirmar que todos os clientes estão a utilizar versões aprovadas. Os clientes não têm de desenvolver conhecimentos no domínio de aplicações específicas para geri-las. Adquirem automaticamente as atualizações das aplicações sem terem de se preocupar com a resolução e o diagnóstico de problemas com aquelas. 

Para as equipas de TI, as aplicações geridas permitem-lhe oferecer soluções pré-aprovadas aos utilizadores da organização. Sabe que essas soluções estão em conformidade com os padrões da organização.

Aplicações geridas atualmente não suportam [geridos identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="types-of-managed-applications"></a>Tipos de aplicações geridas

Pode publicar a sua aplicação gerida externa ou internamente.

![Publicar interna ou externamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catálogo de serviços

O catálogo de serviços é um catálogo interno de soluções aprovadas para os utilizadores de uma organização. Utilize o catálogo para cumprir as normas da organização, ao mesmo tempo que disponibiliza soluções para a organização. Os funcionários utilizam o catálogo para localizar facilmente aplicações que são recomendadas e aprovadas pelos departamentos de TI. Veem as aplicações geridas que as outras pessoas da organização partilham com eles.

Para obter informações sobre como publicar uma aplicação gerida do Catálogo de Serviços, veja [Criar uma aplicação do Catálogo de Serviços](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Os fornecedores que pretendam faturar os serviços que oferecem podem disponibilizar uma aplicação gerida através do Azure Marketplace. Depois de o fornecedor publicar uma aplicação, a mesma fica disponível para os utilizadores externos à organização. Com esta abordagem, os fornecedores de serviços geridos (MSP), os fornecedores de software independentes (ISVs) e os integradores de sistemas (SIs) podem oferecer as respetivas soluções a todos os clientes do Azure.

Para obter informações sobre a publicação de aplicações geridas no Marketplace, veja [Criar aplicação do Marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupos de recursos para aplicações geridas

Normalmente, os recursos das aplicações geridas residirem em dois grupos de recursos. O consumidor gere um grupo de recursos e o editor gere o outro. Ao definir a aplicação gerida, o editor especifica os níveis de acesso. A restrição do acesso das [operações de dados](../role-based-access-control/role-definitions.md) não é atualmente suportada para todos os fornecedores de dados no Azure.

A imagem seguinte mostra um cenário em que o editor pede a função de proprietário para o grupo de recursos gerido. O editor aplicou um bloqueio de só de leitura neste grupo de recursos para o consumidor. As identidades do editor que têm acesso ao grupo de recursos gerido estão isentas do bloqueio.

![Acesso ao grupo de recursos](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupo de recursos da aplicação

Este grupo de recursos contém a instância da aplicação gerida. Só pode conter um recurso. O tipo de recurso da aplicação gerida é **Microsoft.Solutions/applications**.

O consumidor tem acesso total ao grupo de recursos e utiliza-o para gerir o ciclo de vida da aplicação gerida.

### <a name="managed-resource-group"></a>Grupo de recursos gerido

Este grupo de recursos contém todos os recursos de que a aplicação gerida precisa. Por exemplo, este grupo de recursos contém as máquinas virtuais, as contas de armazenamento e as redes virtuais da solução. O consumidor tem acesso limitado a este grupo de recursos porque não gere os recursos individuais da aplicação gerida. O acesso do editor a este grupo de recursos corresponde à função especificada na definição da aplicação gerida. Por exemplo, o editor pode pedir a função Proprietário ou Contribuidor para este grupo de recursos.

Quando o consumidor elimina a aplicação gerida, o grupo de recursos gerido também é eliminado.

## <a name="azure-policy"></a>Azure Policy

Pode aplicar uma [Azure Policy](../governance/policy/overview.md) à sua aplicação gerida. Aplique políticas para garantir que as instâncias implementadas da sua aplicação gerida cumprem os requisitos de dados e segurança. Se a sua aplicação interagir com dados confidenciais, confirme que avaliou a forma como estes devem ser protegidos. Por exemplo, se a sua aplicação interagir com dados do Office 365, aplique uma política para garantir que a encriptação de dados está ativada.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os benefícios da utilização de aplicações geridas. Aceda ao artigo seguinte para criar uma definição da aplicação gerida.

> [!div class="nextstepaction"]
> [Quickstart: Publicar um Azure definição da aplicação gerida](publish-managed-app-definition-quickstart.md)
