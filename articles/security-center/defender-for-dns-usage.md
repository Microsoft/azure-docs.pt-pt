---
title: Como responder aos alertas do Azure Defender para os alertas do DNS
description: Conheça os passos necessários para responder aos alertas do Azure Defender para o DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754774"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Responda ao Azure Defender para alertas dns

Quando receber um alerta do Azure Defender para o DNS, recomendamos que investigue e responda ao alerta conforme descrito abaixo. O Azure Defender para DNS protege todos os recursos conectados, por isso mesmo que esteja familiarizado com a aplicação ou utilizador que desencadeou o alerta, é importante verificar a situação em torno de cada alerta.  


## <a name="step-1-contact"></a>Passo 1. Contacto

1. Contacte o titular do recurso para determinar se o comportamento era esperado ou intencional.
1. Se a atividade for esperada, desprezem o alerta.
1. Se a atividade for inesperada, trate o recurso como potencialmente comprometido e atenuante como descrito no passo seguinte.

## <a name="step-2-immediate-mitigation"></a>Passo 2. Atenuação imediata 

1. Isolar o recurso da rede para evitar o movimento lateral.
1. Esc integralmente a varredura de antimalware no recurso, seguindo quaisquer conselhos de reparação resultantes.
1. Reveja o software instalado e em execução no recurso, removendo quaisquer pacotes desconhecidos ou indesejados.
1. Reverta a máquina para um bom estado conhecido, reinstalando o sistema operativo se necessário, e restaurando o software a partir de uma fonte sem malware verificada.
1. Resolva quaisquer recomendações do Centro de Segurança Azure para a máquina, remediando problemas de segurança destacados para evitar futuras falhas.


## <a name="next-steps"></a>Passos seguintes

Esta página explicou o processo de resposta a um alerta do Azure Defender para o DNS. Para obter informações relacionadas consulte as seguintes páginas:

- [Introdução ao Azure Defender para DNS](defender-for-dns-introduction.md)
- [Alertas de Supressão do Azure Defender](alerts-suppression-rules.md)
- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)