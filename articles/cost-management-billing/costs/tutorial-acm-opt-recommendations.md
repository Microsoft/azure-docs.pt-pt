---
title: Tutorial – Reduzir os custos do Azure com recomendações
description: Este tutorial ajuda-o a reduzir os custos do Azure quando segue recomendações de otimização.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 37253bb4c6001afe436e22597e75e2bc869fbbc8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990297"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: Otimizar os custos com base em recomendações

O Azure Cost Management funciona com o Assistente do Azure para dar recomendações de otimização de custos. O Assistente do Azure ajuda-o a otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados. Este tutorial mostra-lhe um exemplo em que identifica recursos do Azure subutilizados e toma uma ação para reduzir os custos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização de custos para identificar potenciais ineficiências de utilização
> * Seguir uma recomendação de redimensionamento de uma máquina virtual para uma opção mais rentável
> * Verificar a ação para garantir que a máquina virtual foi redimensionada com sucesso

## <a name="prerequisites"></a>Pré-requisitos
Estão disponíveis recomendações para uma variedade de âmbitos e tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md). Tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos para ver os dados de custos. Para obter mais informações sobre âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Subscrição
- Grupo de recursos

Tem de ter máquinas virtuais ativas com, pelo menos, 14 dias de atividade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Ver recomendações de otimização de custos

Para ver as recomendações de otimização de custos de uma subscrição, abra o âmbito desejado no portal do Azure e selecione **Recomendações do assistente**.

Para ver recomendações para um grupo de gestão, abra o âmbito desejado no portal do Azure e selecione **Análise de custos** no menu. Utilize o atalho **Âmbito** para mudar para um âmbito diferente, como um grupo de gestão. Selecione **Recomendações do assistente** no menu. Para obter mais informações sobre âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

![Recomendações do Assistente do Cost Management no portal do Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A lista de recomendações identifica as ineficiências de utilização ou mostra as recomendações de utilização que o podem ajudar a poupar mais dinheiro. O total de **Potenciais poupanças anuais** mostra o montante total que pode poupar caso encerre ou desaloque todas as VMs para que cumpram as regras de recomendação. Se não as quiser encerrar, deverá considerar redimensioná-las para um SKU de VM mais barato.

A categoria **Impacto** e as **Potenciais poupanças anuais** foram concebidas para ajudar a identificar as recomendações que têm o potencial para poupar o máximo possível.

As recomendações de impacto elevado incluem:
- [Comprar instâncias de máquina virtual reservadas para poupar dinheiro sobre os custos pay as you go](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Otimizar a despesa da máquina virtual ao redimensionar ou encerrar instâncias subutilizadas](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Utilizar o Armazenamento Standard para armazenar instantâneos do Managed Disks](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

As recomendações de impacto médio incluem:
- [Eliminar pipelines do Azure Data Factory com falhas](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduzir custos ao eliminar circuitos do ExpressRoute não aprovisionados](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduzir custos ao eliminar ou reconfigurar gateways de rede virtual](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Seguir uma recomendação

O Assistente do Azure monitoriza a utilização da máquina virtual durante sete dias e, em seguida, identifica as máquinas virtuais subutilizadas. As máquinas virtuais cuja utilização da CPU é de 5% ou menos e cuja utilização de rede é de 7 MB ou menos durante quatro dias ou mais são consideradas máquinas virtuais de utilização baixa.

A definição de utilização da CPU de 5% ou menos é p valor predefinido, mas poderá ajustar as definições. Para obter mais informações sobre como ajustar a definição, veja [Configurar a regra de utilização média da CPU ou a recomendação de máquina virtual de utilização baixa](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Embora alguns cenários possam resultar em pouca utilização por defeito, pode, em muitos casos, poupar dinheiro ao alterar o tamanho das máquinas virtuais para tamanhos mais baratos. As poupanças reais poderão variar se escolher uma ação de redimensionamento. Vamos mostrar-lhe um exemplo de redimensionamento de uma máquina virtual.

Na lista de recomendações, clique na recomendação **Redimensionar ou encerrar máquinas virtuais subutilizadas**. Na lista das máquinas virtuais candidatas, escolha uma máquina virtual para redimensionar e, em seguida, clique na máquina virtual. Os detalhes da máquina virtual são mostrados para que possa verificar a métrica de utilização. O valor **potenciais poupanças anuais** representará o que pode economizar se encerrar ou remover a VM. O redimensionamento de uma VM irá provavelmente poupar-lhe algum dinheiro, mas não irá poupar o valor completo das potenciais poupanças anuais.

![Exemplo de Detalhes da recomendação](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nos detalhes da VM, verifique a utilização da máquina virtual para confirmar que é uma candidata adequada para o redimensionamento.

![Detalhes da VM de exemplo a mostrar o histórico de utilização](./media/tutorial-acm-opt-recommendations/vm-details.png)

Tenha em conta o tamanho atual da máquina virtual. Depois de ter verificado que a máquina virtual deve ser redimensiona, feche os detalhes da VM para que possa ver a lista de máquinas virtuais.

Na lista de candidatas a encerrar ou a redimensionar, selecione **Redimensionar de *&lt;FromVirtualMachineSKU&gt;* para *&lt;ToVirtualMachineSKU&gt;***.
![Recomendação de exemplo com a opção para redimensionar a máquina virtual](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Em seguida, ser-lhe-á apresentada uma lista de opções de redimensionamento disponíveis. Escolha a que oferecerá o melhor desempenho e que é mais rentável para o seu cenário. No seguinte exemplo, a opção escolhida redimensiona de **Standard_D8s_v3** para **Standard_D2s_v3**.

![Exemplo de lista de tamanhos de VM disponíveis para escolher um tamanho](./media/tutorial-acm-opt-recommendations/choose-size.png)

Depois de escolher um tamanho adequado, clique em **Redimensionar** para iniciar a ação de redimensionamento.

O redimensionamento requer o reinício da máquina virtual em execução ativa. Se a máquina virtual estiver num ambiente de produção, recomendamos que execute a operação de redimensionamento fora do horário comercial. O agendamento do reinício poderá reduzir as interrupções causadas pela indisponibilidade momentânea.

## <a name="verify-the-action"></a>Verificar a ação

Quando o redimensionamento da VM for concluído com sucesso, é mostrada uma notificação do Azure.

![Notificação de máquina virtual redimensionada com sucesso](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização de custos para identificar potenciais ineficiências de utilização
> * Seguir uma recomendação de redimensionamento de uma máquina virtual para uma opção mais rentável
> * Verificar a ação para garantir que a máquina virtual foi redimensionada com sucesso

Se ainda não leu o artigo de melhores práticas do Cost Management, deve fazê-lo, pois este disponibiliza orientações e princípios de nível elevado a considerar para o ajudar a gerir os custos.

> [!div class="nextstepaction"]
> [Melhores práticas do Cost Management](cost-mgt-best-practices.md)
