---
title: Problemas conhecidos do Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo aborda problemas conhecidos com o Azure Monitor para VMs, uma solução no Azure que combina integridade, descoberta de dependência de aplicativos e monitoramento de desempenho do sistema operacional da VM do Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.openlocfilehash: f6719a8c28571faceb6ebad0567d13a4edc60fe6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553773"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (versão prévia)

Este artigo aborda problemas conhecidos com o Azure Monitor para VMs, uma solução no Azure que combina integridade, descoberta de componentes de aplicativos e monitoramento de desempenho do sistema operacional da VM do Azure. 

## <a name="health"></a>Saúde 
Veja a seguir os problemas conhecidos com a versão atual do recurso de integridade:

- Se uma VM do Azure for removida ou excluída, ela será exibida no modo de exibição de lista de VMs por algum tempo. Além disso, clicar no estado de uma VM removida ou excluída abre a exibição **diagnóstico de integridade** e, em seguida, inicia um loop de carregamento. A seleção do nome da VM excluída abre um painel com uma mensagem informando que a VM foi excluída.
- Alterações de configuração, como a atualização de um limite, levam até 30 minutos, mesmo que o portal ou a API do monitor de carga de trabalho possa atualizá-los imediatamente. 
- A experiência de diagnóstico de integridade é atualizada mais rapidamente do que as outras exibições. As informações podem ser atrasadas quando você alterna entre elas. 
- Para VMs do Linux, o título da página que lista os critérios de integridade para uma única exibição de VM tem o nome de domínio inteiro da VM em vez do nome da VM definida pelo usuário. 
- Depois de desabilitar o monitoramento de uma VM usando um dos métodos com suporte e tentar implantá-lo novamente, você deve implantá-lo no mesmo espaço de trabalho. Se você escolher um espaço de trabalho diferente e tentar exibir o estado de integridade dessa VM, ele poderá mostrar um comportamento inconsistente.
- Depois de remover os componentes da solução do espaço de trabalho, você pode continuar a ver o estado de integridade de suas VMs do Azure; especificamente o desempenho e o mapa de dados quando você navega para o modo de exibição no Portal. Eventualmente, os dados deixarão de aparecer da exibição de desempenho e de mapa depois de algum tempo; no entanto, a exibição de integridade continuará a mostrar o status de integridade para suas VMs. A opção **experimentar agora** estará disponível para re-integrar de modo de exibição de desempenho e de mapa.

## <a name="next-steps"></a>Passos seguintes
Para entender os requisitos e métodos para habilitar o monitoramento de suas máquinas virtuais, examine [habilitar Azure monitor para VMs](vminsights-enable-overview.md).
