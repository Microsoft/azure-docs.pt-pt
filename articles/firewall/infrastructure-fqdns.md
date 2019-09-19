---
title: FQDN de infraestrutura para o Firewall do Azure
description: Saiba mais sobre os FQDNs de infraestrutura no firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130209"
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