---
title: Preferência de encaminhamento Semmetered em Azure
description: Saiba como pode configurar a preferência de encaminhamento dos seus recursos para o fornecedor de CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101680305"
---
# <a name="what-is-routing-preference-unmetered"></a>O que é a Preferência de Encaminhamento SemMeteted?

A Preferência de encaminhamento Não é disponíveis para fornecedores da Rede de Entrega de Conteúdos (CDN) com clientes hospedados nos seus conteúdos de origem em Azure. O serviço permite que os fornecedores de CDN estabeleçam uma ligação de pares direto com os routers de borda de rede globais da Microsoft em vários locais.

![Preferência de encaminhamento sem metrô](media/routing-preference-unmetered/unmetered.png)

O tráfego da sua rede que se afasta da origem em Azure destinado ao fornecedor cdn beneficia da conectividade direta.
* A conta de transferência de dados para a saída de tráfego dos seus recursos Azure que são encaminhados através destas ligações diretas é gratuita.
* A ligação direta entre o fornecedor cdn e a origem em Azure proporciona um desempenho ótimo, uma vez que não existem lúpulos no meio. Isto beneficia a carga de trabalho da CDN que frequentemente recolhe dados da origem.

## <a name="configuring-routing-preference-unmetered"></a>Preferência de encaminhamento de configuração nãometida

Para tirar partido da Preferência de Encaminhamento semmeteramento, os seus fornecedores de CDN precisam de fazer parte deste programa. Se o seu fornecedor de CDN não fizer parte do programa, contacte o seu fornecedor DE CDN.

Em seguida, configurar a preferência de encaminhamento para os seus recursos e definir o tipo de Preferência de encaminhamento para a **Internet**. Pode configurar a Preferência de Encaminhamento enquanto cria um endereço IP público e, em seguida, associar o IP público a recursos como máquinas virtuais, equilibradores de carga virados para a Internet e muito mais. [Saiba como configurar a preferência de encaminhamento para um endereço IP público utilizando o portal Azure](routing-preference-portal.md)

Também pode ativar a preferência de encaminhamento para a sua conta de armazenamento e publicar um segundo ponto final, que precisa de ser usado pelo fornecedor CDN para obter dados da origem do armazenamento. Por exemplo, a publicação de um ponto final específico da rota da Internet para a conta de armazenamento *StorageAccountA* publicará o segundo ponto final para os seus serviços de armazenamento, como mostrado abaixo:

![Preferência de encaminhamento para contas de armazenamento](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Passos seguintes

* [Configure a preferência de encaminhamento para um VM utilizando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configure a preferência de encaminhamento para um VM utilizando o CLI Azure](configure-routing-preference-virtual-machine-cli.md)
* [Configure a preferência de encaminhamento para a sua conta de armazenamento](/azure/storage/common/network-routing-preference)