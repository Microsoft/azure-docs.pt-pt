---
title: FQDN de infraestrutura para o Firewall do Azure
description: O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168971"
---
# <a name="infrastructure-fqdns"></a>FQDNs de Infraestrutura

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. 

Os seguintes serviços estão incluídos na coleção de regras internas:

- Acesso de computação ao repositório de imagens da plataforma de armazenamento (PIR)
- Acesso ao armazenamento de status de discos gerenciados
- Diagnóstico do Azure e registro em log (MDS)

## <a name="overriding"></a>Sobrecarga 

Você pode substituir essa coleção de regras de infraestrutura interna criando uma coleção de regras negar todos os aplicativos que é processada por último. Será sempre processada antes da coleção de regras de infraestrutura. Qualquer item que não esteja na coleção de regras de infraestrutura é negado por predefinição.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).