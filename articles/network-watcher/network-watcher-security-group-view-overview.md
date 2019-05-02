---
title: Introdução à vista de grupo de segurança no observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece uma descrição geral da capacidade de vista de segurança de observador de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: kumud
ms.openlocfilehash: ed0ad244a758b92f5ccba5785184190b030c306c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722586"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introdução à vista de grupo de segurança de rede no observador de rede do Azure

Grupos de segurança de rede são associados a um nível de sub-rede ou num nível NIC. Quando associados a um nível de sub-rede, aplica-se a todas as instâncias VM na sub-rede. Vista de grupo de segurança de rede devolve todas as configurado NSGs e regras que estão associadas a um nível NIC e sub-rede de uma máquina virtual, fornece ideias sobre a configuração. Além disso, as regras de segurança efetivas são devolvidas para cada uma das NICs numa VM. Vista de grupo de segurança de rede a utilizar, pode avaliar uma VM para vulnerabilidades de rede, tais como abrir portas. Também pode validar se o seu grupo de segurança de rede está a funcionar conforme esperado com base numa [comparação entre configurada e as regras de segurança aprovadas](network-watcher-nsg-auditing-powershell.md).

Um caso de utilização mais expandido está em conformidade de segurança e auditoria. Pode definir um conjunto prescritivo de regras de segurança como um modelo de governação de segurança na sua organização. Uma auditoria de conformidade periódica pode ser implementada de forma programática ao comparar as regras prescritivas com as regras em vigor para cada uma das VMs na sua rede.

No portal do que as regras são divididas em vigor, sub-rede, Interface de rede e padrão. Isso fornece uma exibição simples para as regras aplicadas a uma máquina virtual. Um botão de download é fornecido para transferir facilmente todas as regras de segurança, não importa o separador para um ficheiro CSV.

![Vista de grupo de segurança][1]

As regras podem ser selecionadas e é aberto um painel novo para mostrar o grupo de segurança de rede e os prefixos de origem e de destino. A partir deste painel, pode navegar diretamente para o recurso do grupo de segurança de rede.

![desagregar][2]

### <a name="next-steps"></a>Passos Seguintes

Saiba como as definições de grupo de segurança de rede de auditoria visitando [configurações de grupo de segurança de rede de auditoria com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









