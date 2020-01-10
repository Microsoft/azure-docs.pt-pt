---
title: O Azure Monitor para problemas conhecidos VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo aborda problemas conhecidos com o Azure Monitor para VMs, uma solução no Azure que combina integridade, descoberta de dependência de aplicativos e monitoramento de desempenho do sistema operacional da VM do Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450683"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (pré-visualização)

Este artigo aborda problemas conhecidos com o Azure Monitor para VMs, uma solução no Azure que combina integridade, descoberta de componentes de aplicativos e monitoramento de desempenho do sistema operacional da VM do Azure. 

## <a name="health"></a>Saúde 
Veja a seguir os problemas conhecidos com a versão atual do recurso de integridade:

- Se uma VM do Azure for removida ou excluída, ela será exibida no modo de exibição de lista de VMs por algum tempo. Além disso, o estado de uma VM removida ou eliminada de clicar abre o **diagnóstico de estado de funcionamento** ver e, em seguida, inicia um ciclo de carregamento. Selecionar o nome da VM eliminada é aberto um painel com uma mensagem a indicar que a VM tiver sido eliminada.
- Alterações de configuração, como atualizar um limite, demorar até 30 minutos, mesmo que o portal ou a API de Monitor da carga de trabalho pode atualizá-las imediatamente. 
- A experiência de diagnóstico de integridade é atualizada mais rapidamente do que as outras exibições. As informações podem ser atrasadas quando você alterna entre elas. 
- Para VMs do Linux, o título da página que lista os critérios de integridade para uma única exibição de VM tem o nome de domínio inteiro da VM em vez do nome da VM definida pelo usuário. 
- Depois de desabilitar o monitoramento de uma VM usando um dos métodos com suporte e tentar implantá-lo novamente, você deve implantá-lo no mesmo espaço de trabalho. Se você escolher um espaço de trabalho diferente e tentar exibir o estado de integridade dessa VM, ele poderá mostrar um comportamento inconsistente.
- Depois de remover os componentes da solução do espaço de trabalho, você pode continuar a ver o estado de integridade de suas VMs do Azure; especificamente o desempenho e o mapa de dados quando você navega para o modo de exibição no Portal. Eventualmente, os dados deixarão de aparecer da exibição de desempenho e de mapa depois de algum tempo; no entanto, a exibição de integridade continuará a mostrar o status de integridade para suas VMs. A opção **experimentar agora** estará disponível para re-integrar de modo de exibição de desempenho e de mapa.

## <a name="next-steps"></a>Passos seguintes
Para entender os requisitos e métodos para habilitar o monitoramento de suas máquinas virtuais, examine [habilitar Azure monitor para VMs](vminsights-enable-overview.md).
