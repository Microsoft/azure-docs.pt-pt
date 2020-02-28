---
title: O Azure Monitor para problemas conhecidos VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo aborda questões conhecidas com o Azure Monitor para VMs, uma solução em Azure que combina saúde, descoberta de dependência de aplicações e monitorização de desempenho do sistema operativo Azure VM.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: 711b3707d536c4858578817589670edf0f467b64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670734"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problemas conhecidos com o Azure Monitor para VMs (pré-visualização)

Este artigo aborda questões conhecidas com o Azure Monitor para VMs, uma solução em Azure que combina saúde, descoberta de componentes de aplicações e monitorização de desempenho do sistema operativo Azure VM. 

## <a name="health"></a>Estado de Funcionamento 
São conhecidos os seguintes problemas com a versão atual da funcionalidade Saúde:

- Se um VM Azure for removido ou eliminado, é apresentado na vista da lista VM durante algum tempo. Além disso, clicar no estado de um VM removido ou eliminado abre a vista Health **Diagnostics** e, em seguida, inicia um loop de carregamento. Selecionar o nome da VM eliminada é aberto um painel com uma mensagem a indicar que a VM tiver sido eliminada.
- Alterações de configuração, como atualizar um limite, demorar até 30 minutos, mesmo que o portal ou a API de Monitor da carga de trabalho pode atualizá-las imediatamente. 
- A experiência de Diagnóstico de Saúde atualiza atualiza-se mais rapidamente do que as outras vistas. A informação pode atrasar-se quando se troca entre eles. 
- Para os VMs Linux, o título da página que lista os critérios de saúde para uma única vista VM tem todo o nome de domínio do VM em vez do nome VM definido pelo utilizador. 
- Depois de desativar a monitorização de um VM utilizando um dos métodos suportados e tentar implantá-lo novamente, deverá implantá-lo no mesmo espaço de trabalho. Se escolher um espaço de trabalho diferente e tentar ver o estado de saúde para esse VM, pode mostrar um comportamento inconsistente.
- Depois de remover os componentes da solução do seu espaço de trabalho, poderá continuar a ver o estado de saúde dos seus VMs Azure; especificamente dados de desempenho e mapa quando navega para qualquer uma das vistas no portal. Os dados acabarão por deixar de aparecer da vista Performance e Mapa após algum tempo; no entanto, a visão da Saúde continuará a mostrar o estado de saúde dos seus VMs. A opção **Try now** estará disponível para reembarcar apenas a partir de vistas de Desempenho e Mapa.

## <a name="next-steps"></a>Passos seguintes
Para compreender os requisitos e métodos para permitir a monitorização das suas máquinas virtuais, reveja o [Monitor Enable Azure para VMs](vminsights-enable-overview.md).
