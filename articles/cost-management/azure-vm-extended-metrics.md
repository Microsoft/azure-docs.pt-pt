---
title: Adicionar métricas expandidas para máquinas virtuais do Azure | Documentos da Microsoft
description: Este artigo ajuda-o a ativar e configurar métricas de diagnóstico expandida para as VMs do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/12/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b7e4665dc3579f357ce1e28bf34be35c931736bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647674"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Adicionar métricas expandidas para máquinas virtuais do Azure

Gestão de custos utiliza dados de métrica do Azure das VMs do Azure para mostrar a que informações detalhadas sobre seus recursos. Dados de métrica, também chamados de contadores de desempenho, são utilizados pela gestão de custos para gerar relatórios. No entanto, a gestão de custos não automaticamente reunir todos os dados de métrica do Azure de VMs de convidado, tem de ativar a recolha de métrica. Este artigo ajuda-o a ativar e configurar métricas de diagnóstico adicionais para as VMs do Azure.

Depois de ativar a recolha de métrica, pode:

- Sabe quando as suas VMs são atingir seus memória, disco e limites de CPU.
- Detete tendências de utilização e anomalias.
- Controle os custos de dimensionamento de acordo com a utilização.
- Obter o custo Efetivo dimensionamento recomendações de Otimização da gestão de custos.

Por exemplo, pode querer monitorizar a % de CPU e memória % das suas VMs do Azure. As métricas de VM do Azure correspondem aos _[anfitrião] percentagem de CPU_ e _percentagem de memória [convidada]_.

> [!NOTE]
> Recolha de dados de métrica expandida só é suportada com a monitorização ao nível do convidado do Azure. Gestão de custos não é compatível com a extensão de VM do Log Analytics.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Certifique-se de que as métricas estão ativadas em VMs

1. Inicie sessão no portal do Azure em http://portal.azure.com.
2. Sob **máquinas virtuais**, selecione uma VM e, em **monitorização**, selecione **métricas**. É apresentada uma lista de métricas disponíveis.
3. Selecione algumas métricas e um gráfico Exibe dados para eles.  
    ![Métrica de exemplo – percentagem de CPU do anfitrião](./media/azure-vm-extended-metrics/metric01.png)

No exemplo anterior, um conjunto limitado de métricas standard estão disponíveis para os anfitriões, mas não são métricas de memória. Métricas de memória fazem parte de métricas expandidas. Tem de efetuar alguns passos adicionais para ativar as métricas expandidas. As seguintes informações orientam-o através da ativação-los.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Ativar métricas expandidas no portal do Azure

Métricas padrão são métricas de computador do anfitrião. O _percentagem de CPU [anfitrião]_ métrica é um exemplo. Também existem métricas básicas para máquinas virtuais convidadas e também são chamados métricas expandidas. Exemplos de métricas expandidas _percentagem de memória [convidada]_ e _[convidado] memória disponível_.

Ativar as métricas expandidas é simples. Para cada VM, ative a monitorização de ao nível do convidado. Quando ativa a monitorização ao nível do convidado, o agente de diagnóstico do Azure está instalado na VM. O seguinte processo é o mesmo para as VMs clássicas e regulares e o mesmo para Windows e VMs do Linux.

Tenha em atenção que o Azure e Linux monitorização ao nível do convidado precisam de uma conta de armazenamento. Quando ativa a monitorização ao nível do convidado, se não escolher uma conta de armazenamento existente, em seguida, é criada uma para.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Ativar a monitorização ao nível do convidado VMs existentes

1. Na **máquinas virtuais**, ver a lista das suas VMs e, em seguida, selecione uma VM.
2. Sob **monitorização**, selecione **métricas**.
3. Clique em **das definições de diagnóstico**.
4. Na página de definições de diagnóstico, clique em **ativar a monitorização ao nível do convidado**.  
    ![Ativar a monitorização ao nível do convidado](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Após alguns minutos, o agente de diagnóstico do Azure está instalado na VM. Atualize a página e a lista de métricas disponíveis é atualizada com as métricas de convidado.  
    ![Métricas expandidas](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Ativar a monitorização ao nível do convidado novas VMs

Ao criar novas VMs, certifique-se de que seleciona **diagnóstico do SO convidado**.

![Ativar diagnósticos de SO convidado](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Credenciais de Gestor de recursos

Depois de ativar métricas expandidas, certifique-se de que a gestão de custos tem acesso à sua [credenciais do Gestor de recursos](activate-subs-accounts.md). As suas credenciais são necessárias para a gestão de custos recolher e apresentar dados de desempenho para as suas VMs. Eles também são usados para criar recomendações de otimização de custos. Gestão de custos tem, pelo menos, três dias de dados de desempenho de uma instância para determinar se é um candidato de uma recomendação de downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Ativar as métricas VM com um script

Pode ativar métricas da VM com scripts do PowerShell do Azure. Quando tiver muitas VMs que pretende ativar as métricas em, pode usar um script para automatizar o processo. Scripts de exemplo estão disponíveis no GitHub em [ativar o diagnóstico do Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Ver métricas de desempenho do Azure

Para ver métricas de desempenho em suas instâncias do Azure no portal do Cloudyn, navegue para **ativos** > **computação** > **instância Explorer**. Na lista de instâncias de VM, expanda uma instância e, em seguida, expanda um recurso para ver os detalhes.

![Explorador de instância](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Passos Seguintes

- Se ainda não ativou a acesso à API do Azure Resource Manager para as suas contas, avance para o [contas e subscrições de ativar o Azure](activate-subs-accounts.md).
