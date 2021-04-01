---
title: Apache Kafka na visão geral da Nuvem Confluente - Soluções parceiras Azure
description: Saiba como usar Apache Kafka na Nuvem Confluente no Mercado Azure.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253530"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>O que é Apache Kafka para a Nuvem Confluente?

Apache Kafka for Confluent Cloud é uma oferta do Azure Marketplace que fornece a Apache Kafka como um serviço. É totalmente gerido para que possa concentrar-se em construir as suas aplicações em vez de gerir os clusters.

Para reduzir o fardo da gestão de plataformas cruzadas, a Microsoft associou-se à Confluent Cloud para construir uma camada integrada de avisionamento de Azure a Confluent Cloud. Proporciona uma experiência consolidada para a utilização da Nuvem Confluente no Azure. Pode integrar e gerir facilmente a Nuvem Confluente com as suas aplicações Azure.

Anteriormente, teve de comprar a oferta da Nuvem Confluente no Marketplace e configurar separadamente a conta em Confluent Cloud. Para gerir configurações e recursos, teve de navegar entre os portais para Azure e Cloud Confluente.

Agora, você disponibiliza os recursos da Cloud Confluent através de um fornecedor de recursos chamado **Microsoft.Confluent**. Você cria e gere recursos da organização Confluent Cloud através do [portal Azure](https://portal.azure.com/) [CLI,](/cli/azure/)ou [Azure SDKs](/azure/#languages-and-tools). A Confluent Cloud detém e executa o software como uma aplicação de serviço (SaaS), incluindo os ambientes, clusters, tópicos, chaves API e conectores geridos.

## <a name="capabilities"></a>Capacidades

A profunda integração entre a Nuvem Confluente e o Azure permite as seguintes capacidades:

- Disponibilização de um novo recurso de organização Confluent Cloud a partir do portal Azure com infraestruturas totalmente geridas.
- Dinamize um único sign-on (SSO) de Azure a Confluent Cloud com Azure Ative Directory (Azure AD). Não é necessária nenhuma autenticação separada do portal Cloud Confluent.
- Obtenha faturação unificada do consumo de Nuvem Confluente através da faturação por subscrição da Azure.
- Gerencie os recursos da Cloud Confluent a partir do portal Azure e rastree-os na página **de Todos os recursos** com os seus outros recursos Azure.

## <a name="confluent-organization"></a>Organização confluente

Uma organização Confluente é um recurso que fornece o mapeamento entre os recursos Azure e Confluent Cloud. É o recurso principal para outros recursos da Nuvem Confluente.

Cada subscrição do Azure pode conter vários planos Confluent. Cada plano Confluente é mapeado para uma conta de utilizador e organização no portal Confluent. Dentro de cada organização Confluente, você pode criar vários ambientes, clusters, tópicos e conectores.

Ao providenciar um recurso Confluent Cloud em Azure, obtém-se um ID de organização Confluente, ambiente padrão e conta de utilizador. Para mais informações, consulte [QuickStart: Começar com a Nuvem Confluente em Azure.](create.md)

Para faturação, cada oferta de Nuvem Confluente comprada nos mapas do Marketplace a uma organização confluente única.

## <a name="single-sign-on"></a>Início de sessão único

Quando iniciar seduzição no portal Azure, as suas credenciais também são usadas para iniciar súbência no portal Confluent Cloud SaaS. A experiência utiliza [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) e [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) para fornecer uma forma segura e conveniente de iniciar sedutar.

Para obter mais informações, consulte ['S'S.'](manage.md#single-sign-on)

## <a name="billing"></a>Faturação

Existem duas opções de faturação disponíveis: plano mensal de pagamento e plano de compromisso.

- Com o **plano mensal pay-as-you-go,** você recebe os custos de consumo da Nuvem Confluente na sua conta mensal Azure.
- Com um plano de **compromisso,** você se inscreve por um valor mínimo de gasto e obtém um desconto no seu uso comprometido de Confluent Cloud.

Você decide qual a opção de faturação a usar quando cria o serviço.

## <a name="confluent-links"></a>Ligações confluentes

Para obter ajuda adicional na utilização de Apache Kafka para Nuvem Confluente, consulte as seguintes ligações ao [site Confluent.](https://docs.confluent.io/home/overview.html)

Para aprender sobre opções de faturação, consulte:

* [Azure Marketplace com Pay As You Go](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Mercado Azure com Compromissos](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Para conhecer a gestão das soluções, consulte:

* [Criar um cluster na nuvem confluente](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Ambientes de nuvem confluente](https://docs.confluent.io/current/cloud/using/environments.html)
* [Básicos de nuvem confluente](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Para apoio e termos, consulte:

* [Apoio confluente](https://support.confluent.io)
* [Termos de Serviço](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Passos seguintes

Para criar um exemplo de Apache Kafka para Nuvem Confluente, consulte [QuickStart: Começar com a Nuvem Confluente no Azure](create.md).
