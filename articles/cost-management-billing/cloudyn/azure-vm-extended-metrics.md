---
title: Adicionar métricas expandidas para máquinas virtuais do Azure
description: Este artigo ajuda-o a ativar e a configurar métricas de diagnóstico expandidas para as VMs do Azure.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 14ea98ecc4d9682353038088a124802d60a5dd5d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131450"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Adicionar métricas expandidas para máquinas virtuais do Azure

A Cloudyn utiliza os dados de métricas do Azure das suas VMs do Azure para lhe mostrar informações detalhadas sobre os recursos. Os dados de métricas, também denominados contadores de desempenho, são utilizados pela Cloudyn para gerar relatórios. No entanto, a Cloudyn não recolhe automaticamente todos os dados de métricas do Azure de VMs de convidado. Para isso, tem de ativar a recolha de métricas. Este artigo ajuda-o a ativar e a configurar métricas de diagnóstico adicionais para as VMs do Azure.

Depois de ativar a recolha de métricas, pode:

- Saber quando as VMs estão a atingir os limites de memória, disco e CPU.
- Detetar tendências de utilização e anomalias.
- Controlar os custos através de dimensionamento de acordo com a utilização.
- Obter recomendações de otimização de dimensionamento rentável da Cloudyn.

Por exemplo, pode querer monitorizar a % da CPU e de Memória das VMs do Azure. As métricas das VMs do Azure correspondem à _Percentagem de CPU_ e _Memória\\% de Bytes Consolidados em Utilização_.

> [!NOTE]
> A recolha de dados de métricas expandidas é suportada apenas com a monitorização ao nível do convidado do Azure. A Cloudyn não é compatível com o [agente do Log Analytics](../../azure-monitor/platform/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Determinar se as métricas expandidas estão ativadas

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Em **Máquinas virtuais**, selecione uma VM e, em **Monitorização**, selecione **Métricas**. É apresentada uma lista de métricas disponíveis.
3. Selecione algumas métricas para ver um gráfico com os dados correspondentes.  
    ![Métrica de exemplo – Percentagem de CPU do anfitrião](./media/azure-vm-extended-metrics/metric01.png)

No exemplo anterior, está disponível um conjunto limitado de métricas padrão para os anfitriões, mas as métricas de memória não são apresentadas. As métricas de memória fazem parte das métricas expandidas. Neste caso, as métricas expandidas não estão ativadas para a VM. Tem de realizar alguns passos adicionais para ativar as métricas expandidas. As seguintes informações explicam como as pode ativar.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Ativar as métricas expandidas no portal do Azure

As métricas padrão são métricas do computador anfitrião. A métrica _Percentagem de CPU_ é um exemplo. Existem igualmente métricas básicas para VMs de convidado, também denominadas métricas expandidas. Os exemplos de métricas expandidas incluem _\Memória\\% de Bytes Consolidados em Utilização_ e _\Memória\Bytes Disponíveis_.

A ativação das métricas expandidas é simples. Ative a monitorização ao nível do convidado para cada VM. Quando ativar a monitorização ao nível do convidado, o agente de diagnóstico do Azure é instalado na VM. Por predefinição, é adicionado um conjunto básico de métricas expandidas. O processo seguinte é o mesmo para as VMs clássicas e regulares, bem como para as VMs do Windows e do Linux.

Não se esqueça de que tanto a monitorização ao nível do convidado do Windows como do Linux requer uma conta de armazenamento. Quando ativar a monitorização ao nível do convidado, se não escolher uma conta de armazenamento existente, será criada uma automaticamente.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Ativar a monitorização ao nível do convidado em VMs existentes

1. Em **Máquinas Virtuais**, veja a lista das suas VMs e, em seguida, selecione uma.
2. Em **Monitorização**, selecione **Definições de diagnóstico**.
3. Na página Definições de diagnóstico, clique em **Ativar monitorização ao nível do convidado**.  
    ![Ativar monitorização ao nível do convidado na página Descrição geral](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Após alguns minutos, o agente de diagnóstico do Azure é instalado na VM. É adicionado um conjunto básico de métricas. Atualize a página. Os contadores de desempenho adicionados aparecem no separador Descrição geral.
5. Em Monitorização, selecione **Métricas**.
6. No gráfico de métricas em **Espaço de Nomes da Métrica**, selecione **Convidado (Clássico)** .
7. Na lista Métricas, pode ver todos os contadores de desempenho disponíveis para a VM de convidado.  
    ![lista de métricas expandidas de exemplo](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Ativar a monitorização ao nível do convidado em VMs novas

Quando criar VMs novas, no separador Gestão, selecione **Ativado** para **Diagnóstico do SO convidado**.

![Definir Diagnóstico do SO convidado como Ativado](./media/azure-vm-extended-metrics/new-enable-diag.png)

Para obter mais informações sobre como ativar as métricas expandidas para máquinas virtuais do Azure, veja [Compreender e utilizar o agente Linux do Azure](../../virtual-machines/extensions/agent-linux.md) e [Descrição geral do Agente de Máquina Virtual do Azure](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Credenciais do Resource Manager

Depois de ativar as métricas expandidas, verifique se a Cloudyn tem acesso às [credenciais do Resource Manager](./activate-subs-accounts.md). As credenciais são necessárias para que a Cloudyn consiga recolher e apresentar os dados de desempenho das VMs. São também utilizados para criar recomendações de otimização de custos. A Cloudyn precisa de, pelo menos, três dias de dados de desempenho de uma instância para determinar se é candidata a uma recomendação de redução.

## <a name="enable-vm-metrics-with-a-script"></a>Ativar as métricas das VMs com um script

Pode ativar as métricas das VMs com scripts do Azure PowerShell. Quando tiver muitas VMs nas quais quer ativar as métricas, pode utilizar um script para automatizar o processo. Os scripts de exemplo estão no GitHub em [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics) (Ativar os Diagnósticos do Azure).

## <a name="view-azure-performance-metrics"></a>Ver as métricas de desempenho do Azure

Para ver as métricas de desempenho nas Instâncias do Azure no portal da Cloudyn, navegue para **Recursos** > **Computação** >  **Explorador**. Na lista de instâncias de VM, expanda uma instância e, em seguida, expanda um recurso para ver os detalhes.

![informações de exemplo mostradas no Explorador de Instâncias](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Passos seguintes

- Se ainda não ativou a acesso à API do Azure Resource Manager para as suas contas, avance para [Ativar subscrições e contas do Azure](./activate-subs-accounts.md).