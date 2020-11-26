---
title: Troubleshoot Azure Monitor para saúde de hóspedes VMs (pré-visualização)
description: Descreve os passos de resolução de problemas que pode tomar quando tiver problemas com o Azure Monitor para a saúde dos VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 2aa83a861697882127c75eb696807f604c3061f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184557"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Troubleshoot Azure Monitor para saúde de hóspedes VMs (pré-visualização)
Este artigo descreve medidas de resolução de problemas que pode tomar quando tem problemas com o Azure Monitor para a saúde dos VMs.

## <a name="error-message-that-no-data-is-available"></a>Mensagem de erro de que não há dados disponíveis 

![Sem dados](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Verifique se a máquina virtual cumpre os requisitos de configuração

1. Verifique se a máquina virtual é uma máquina virtual Azure. Azure Arc para servidores não está suportada atualmente.
2. Verifique se a máquina virtual está a executar um [sistema operativo suportado](vminsights-health-enable.md?current-limitations.md).
3. Verifique se a máquina virtual está instalada numa [região suportada.](vminsights-health-enable.md?current-limitations.md)
4. Verifique se o espaço de trabalho Log Analytics está instalado numa [região suportada.](vminsights-health-enable.md?current-limitations.md)

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Verifique se o VM está devidamente a bordo
Verifique se a extensão do agente Azure Monitor e o agente de Saúde VM convidado estão a ser a provisionados com sucesso na máquina virtual. Selecione **Extensões** do menu da máquina virtual no portal Azure. Se os dois agentes estão listados, então []() ver.

![Extensões de VM](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Verifique se a identidade atribuída ao sistema está ativada na máquina virtual
Verifique se a identidade atribuída ao sistema está ativada na máquina virtual. Selecione **identidade** do menu da máquina virtual no portal Azure. Se os dois agentes estão listados, então []() ver.

![Identidade atribuída ao sistema](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verificar regra de recolha de dados
Verifique se a regra de recolha de dados especificando a extensão de saúde como fonte de dados está associada à máquina virtual.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Mensagem de erro por mau pedido devido a permissões insuficientes
Este erro indica que o fornecedor de recursos **Microsoft.WorkloadMonitor** não estava registado na subscrição. Consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) para obter informações sobre o registo deste fornecedor de recursos. 

![Mau pedido](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma visão geral da funcionalidade de saúde do Azure Monitor para VMs](vminsights-health-overview.md)