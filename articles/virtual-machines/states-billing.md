---
title: Estados e faturação de Máquinas Virtuais Azure
description: Visão geral de vários estados que um VM pode introduzir e quando um utilizador é faturado.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596302"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Estados e faturação de Máquinas Virtuais Azure

As Máquinas Virtuais Azure (VMs) passam por diferentes estados que podem ser categorizados em estados *de fornecimento* e *energia.* O objetivo deste artigo é descrever estes estados e destacar especificamente quando os clientes são cobrados, por exemplo, o uso. 

## <a name="get-states-using-instance-view"></a>Obtenha estados usando a vista de exemplo

A api de opinião de exemplo fornece informações sobre o estado de execução da VM. Para mais informações, consulte as [Máquinas Virtuais - Exemplo Ver](/rest/api/compute/virtualmachines/instanceview) documentação da API.

O explorador de Recursos Azure fornece uma UI simples para visualizar o estado de execução VM: [Resource Explorer](https://resources.azure.com/).

Os estados de provisionamento são visíveis nas propriedades de VM e na visualização de instâncias. Os estados de energia estão disponíveis, por exemplo, à vista de VM.

Para recuperar o estado de potência de todos os VMs na sua subscrição, utilize as [Máquinas Virtuais - Liste Todas as API](/rest/api/compute/virtualmachines/listall) com o status de **parâmetros Definidos** para *verdadeiros*.

> [!NOTE]
> [Máquinas Virtuais - Liste todas as API](/rest/api/compute/virtualmachines/listall) com o status de **parâmetros Uma vez** definida para verdadeira irá recuperar os estados de energia de todos os VMs numa subscrição. No entanto, em algumas situações raras, o estado de energia pode não estar disponível devido a problemas intermitentes no processo de recuperação. Nestas situações, recomendamos que volte a tentar utilizando a mesma API ou utilizando o [Azure Resource Health](../service-health/resource-health-overview.md) ou [o Azure Resource Graph](..//governance/resource-graph/overview.md) para verificar o estado de potência dos seus VMs.
 
## <a name="power-states-and-billing"></a>Estados de poder e faturação

O estado de poder representa o último estado conhecido do VM.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="A imagem mostra o diagrama dos estados de energia que um VM pode passar. ":::

A tabela seguinte fornece uma descrição de cada estado de instância e indica se é faturada, por exemplo, a utilização ou não.

| Estado de poder | Description | Faturação |  
|---|---|---|
| A iniciar| A Máquina Virtual está a ligar. |Não cobrado* | 
| Em Execução | A Máquina Virtual está completamente de pé. Este é o estado de trabalho padrão. | Faturado | 
| A parar | Este é um estado de transição entre correr e parar. | Faturado| 
|Parada | A Máquina Virtual foi desligada a partir do so do hóspede ou utilizando APIs PowerOff. Neste estado, a máquina virtual ainda está a alugar o hardware subjacente. Este estado também é referido como *Stop (Atribuído)*. | Faturado | 
| A desalocar | Este é o estado de transição entre correr e negociar. | Não cobrado* | 
| Desalocada | A Máquina Virtual lançou o aluguer do hardware subjacente e está completamente desligado. Este estado também é referido como *Stop (Deallocated)*. | Não cobrado* | 

&#42; Alguns recursos Azure, como [Discos](https://azure.microsoft.com/pricing/details/managed-disks) e [Networking](https://azure.microsoft.com/pricing/details/bandwidth/) continuarão a incorrer em encargos.


## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o estado de uma operação de avião de controlo iniciada pelo utilizador no VM. Estes estados estão separados do estado de energia de um VM.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="A imagem mostra os estados de provisionamento que um VM pode passar.":::

| Estado de provisionamento | Description | Estado de poder | Faturação | 
|---|---|---|---|
| Criar | Criação de máquinas virtuais. | A iniciar | Não cobrado* | 
| Atualizar | Atualiza o modelo para uma máquina virtual existente. Algumas alterações não-modelo a uma máquina virtual, como iniciar e reiniciar, estão no estado de atualização. | Em Execução | Faturado | 
| Eliminar | Eliminação de máquinas virtuais. | A desalocar | Não cobrado* |
| Desalocar | A máquina virtual é totalmente parada e removida do hospedeiro subjacente. A oferta de uma máquina virtual é considerada uma atualização e apresentará estados de provisionamento semelhantes à atualização. | A desalocar | Não cobrado* | 

&#42; Alguns recursos Azure, como [Discos](https://azure.microsoft.com/pricing/details/managed-disks) e [Networking](https://azure.microsoft.com/pricing/details/bandwidth/) continuarão a incorrer em encargos.

## <a name="os-provisioning-states"></a>Estados de provisão de SO
Os estados de provisão só se aplicam a máquinas virtuais criadas com uma imagem de SO. Imagens especializadas não vão exibir estes estados. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="A imagem mostra que o fornecimento de SO indica que um VM pode passar.":::

| Estado de provisão de SO | Description | Estado de poder | Faturação | 
|---|---|---|---|
| OSProvisioningInProgress | O VM está em execução e a instalação do So convidado está em andamento. | Em Execução | Faturado | 
| OSProvisioningComplete | Este é um estado de curta duração. A máquina virtual transita rapidamente deste estado para o **Sucesso.** Se as extensões ainda estiverem a ser instaladas, continuará a ver este estado até estarem completas. | Em Execução | Faturado | 
| Com êxito | As ações iniciadas pelo utilizador foram concluídas. | Em Execução | Faturado | 
| Com falhas | Representa uma operação falhada. Consulte o código de erro para obter mais informações e possíveis soluções. | Em Execução  | Faturado | 


## <a name="next-steps"></a>Passos seguintes
- Reveja a [documentação de Gestão de Custos e Faturação da Azure](../cost-management-billing/index.yml)
- Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para planear as suas implementações.
- Saiba mais sobre a monitorização do seu VM, consulte [as máquinas virtuais Monitor em Azure.](../azure-monitor/vm/monitor-vm-azure.md)