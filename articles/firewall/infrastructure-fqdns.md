---
title: Infraestrutura FQDN para firewall Azure
description: O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168971"
---
# <a name="infrastructure-fqdns"></a>FQDNs de Infraestrutura

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. 

Os seguintes serviços estão incluídos na coleção de regras incorporada:

- Aceda ao acesso à plataforma de armazenamento Repositório de Imagem (PIR)
- Acesso ao armazenamento de estado dos discos geridos
- Diagnósticos e Registos Azure (MDS)

## <a name="overriding"></a>Sobrepução 

Você pode anular esta coleção de regras de infraestrutura incorporada, criando uma coleção de regras de aplicação que é processada por último. Será sempre processada antes da coleção de regras de infraestrutura. Qualquer item que não esteja na coleção de regras de infraestrutura é negado por predefinição.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).