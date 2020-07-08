---
title: Filtragem FQDN de Azure Firewall nas regras da rede (pré-visualização)
description: Como utilizar a filtragem FQDN nas regras de rede
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 46d1f2eae1342901a660f99891d1ac1529e8ad77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569316"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtragem FQDN nas regras da rede (pré-visualização)

> [!IMPORTANT]
> A filtragem FQDN nas regras de rede está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um nome de domínio totalmente qualificado (FQDN) representa um nome de domínio de um hospedeiro. Um nome de domínio está associado a um único ou múltiplo endereço IP. Pode permitir ou bloquear tags FQDNs e FQDN nas regras de aplicação. Utilizando configurações personalizadas de DNS e DNS proxy, também pode utilizar a filtragem FQDN nas regras de rede.

## <a name="how-it-works"></a>Como funciona

O Azure Firewall traduz o FQDN para um endereço IP(es) utilizando as suas definições de DNS e faz o processamento de regras com base no Azure DNS ou numa configuração de DNS personalizada.

Para utilizar FQDNs nas regras de rede, deve ativar o proxy DNS. Se não ativar o proxy DNS, o processamento de regras fiável está em risco. Quando está ativado, o tráfego DNS é direcionado para a Azure Firewall, onde pode configurar o seu servidor DNS personalizado. Em seguida, a firewall e os clientes usam o mesmo servidor DNS configurado. Se o proxy DNS não estiver ativado, o Azure Firewall pode produzir uma resposta diferente porque o cliente e a firewall podem usar diferentes servidores para a resolução de nomes. A filtragem FQDN nas regras de rede pode ser defeituosa ou inconsistente se o cliente e a firewall receberem diferentes respostas DNS.

## <a name="next-steps"></a>Próximos passos

[Definições de DNS de Firewall Azure Firewall](dns-settings.md)
