---
title: Infraestrutura FQDN para Firewall Azure
description: O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168971"
---
# <a name="infrastructure-fqdns"></a>FQDNs de Infraestrutura

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. 

Os seguintes serviços estão incluídos na coleção de regras incorporada:

- Acesso computacional ao repositório de imagem da plataforma de armazenamento (PIR)
- Acesso ao estado dos discos geridos
- Diagnósticos e Exploração Madeureiro (MDS)

## <a name="overriding"></a>Primordial 

Você pode sobrepor-se a esta coleção de regras de infraestrutura incorporada, criando uma negação de toda a coleção de regras de aplicação que é processada por último. Será sempre processada antes da coleção de regras de infraestrutura. Qualquer item que não esteja na coleção de regras de infraestrutura é negado por predefinição.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).