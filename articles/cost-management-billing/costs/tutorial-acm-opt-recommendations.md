---
title: Tutorial-reduzir os custos do Azure com recomendações
description: Este tutorial ajuda-o a reduzir os custos do Azure, quando agir em recomendações de otimização.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990297"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: Otimizar os custos de recomendações

O Azure Cost Management funciona com o Assistente do Azure para dar recomendações de otimização de custos. O Assistente do Azure ajuda-o a otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados. Este tutorial explica-lhe um exemplo de onde identificar subutilizados recursos do Azure e, em seguida, tomar providências para reduzir os custos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização para ver o potencial ineficiências de utilização de custos
> * Agir sobre uma recomendação para redimensionar uma máquina virtual para uma opção mais rentável
> * Certifique-se a ação para se certificar de que a máquina virtual foi redimensionada com êxito

## <a name="prerequisites"></a>Pré-requisitos
As recomendações estão disponíveis para uma variedade de escopos e tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). Tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos para ver os dados de custos. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Subscrição
- Grupo de recursos

Tem de ter máquinas virtuais ativas com, pelo menos, de 14 dias de atividade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Ver recomendações de otimização de custos

Para exibir as recomendações de otimização de custo para uma assinatura, abra o escopo desejado no portal do Azure e selecione **recomendações do Advisor**.

Para exibir as recomendações para um grupo de gerenciamento, abra o escopo desejado no portal do Azure e selecione **análise de custo** no menu. Use o **escopo** do Pill para alternar para um escopo diferente, como um grupo de gerenciamento. Selecione **recomendações do Advisor** no menu. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

![Custos recomendações do Assistente de gestão apresentadas no portal do Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A lista de recomendações identifica ineficiências de utilização ou mostra as recomendações de compra que podem ajudar a poupar dinheiro adicional. O totalizados **potenciais poupanças anuais** mostra a quantidade total, que pode economizar se encerrar ou desalocar todas as suas VMs que cumpram as regras de recomendação. Se não quiser encerrá-las, deve considerar o redimensionamento-los para um SKU de VM mais barato.

O **impacto** categoria, juntamente com o **potenciais poupanças anuais**, foram concebidos para ajudar a identificar as recomendações que têm o potencial de guardar tanto quanto possível.

As recomendações de alto impacto incluem:
- [Comprar instâncias de máquinas virtuais reservadas para economizar dinheiro nos custos pagos conforme o uso](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Otimizar o gasto de máquinas virtuais redimensionando ou desligando instâncias subutilizadas](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Usar o armazenamento padrão para armazenar instantâneos de Managed Disks](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

As recomendações de impacto médio incluem:
- [Excluir Azure Data Factory pipelines que estão falhando](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduzir custos eliminando circuitos do ExpressRoute não provisionados](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduzir custos ao excluir ou reconfigurar gateways de rede virtual ociosas](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agir sobre uma recomendação

O Azure Advisor monitora o uso da sua máquina virtual por sete dias e identifica máquinas virtuais subutilizadas. Máquinas virtuais cuja utilização da CPU é cinco por cento ou menos e utilização de rede é de sete MB ou menos para quatro ou mais dias são considerados máquinas de virtuais de baixa utilização.

Os 5% ou menos definição de utilização da CPU é o padrão, mas pode ajustar as definições. Para obter mais informações sobre como ajustar a configuração, consulte [Configurar a regra média de utilização da CPU ou a recomendação de máquina virtual de uso baixo](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Embora alguns cenários podem resultar numa utilização reduzida por design, que, muitas vezes, pode poupar dinheiro ao alterar o tamanho das suas máquinas virtuais para tamanhos de menos dispendiosos. Suas poupanças reais podem variar se escolher uma ação de redimensionamento. Vamos examinar um exemplo de redimensionamento de uma máquina virtual.

Na lista de recomendações, clique a **dimensionar ou encerramento subutilizados máquinas virtuais** recomendação. Na lista de candidatos de máquina virtual, selecione uma máquina virtual, de redimensionar e, em seguida, clique na máquina virtual. Detalhes da máquina virtual são apresentados para que pode verificar as métricas de utilização. O **potenciais poupanças anuais** valor é o que pode poupar se desligar ou remover a VM. Redimensionar uma VM irá provavelmente poupar dinheiro, mas não vai economizar o montante total das poupanças anuais potenciais.

![Exemplo de detalhes da recomendação](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nos detalhes de VM, verifique a utilização da máquina virtual para confirmar que é um candidato de redimensionamento adequado.

![VM de exemplo que mostra utilização histórica de detalhes](./media/tutorial-acm-opt-recommendations/vm-details.png)

Tenha em atenção o tamanho da máquina virtual atual. Depois de verificar que a máquina virtual deve ser redimensionada, feche os detalhes da VM para que vê a lista de máquinas virtuais.

Na lista de candidatos a serem desligados ou redimensionados, selecione * * Redimensionar *&lt;FromVirtualMachineSKU&gt;* para *&lt;ToVirtualMachineSKU&gt;* * *.
![Recomendação de exemplo com a opção para redimensionar a máquina virtual](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Em seguida, é apresentada uma lista de opções de redimensionamento disponíveis. Escolha o método que vai dar o melhor desempenho e rentabilidade para o seu cenário. No exemplo a seguir, a opção escolhida redimensiona de **Standard_D8s_v3** para **Standard_D2s_v3**.

![Lista de exemplo de onde pode escolher um tamanho de tamanhos de VM disponíveis](./media/tutorial-acm-opt-recommendations/choose-size.png)

Depois de escolher um tamanho adequado, clique em **redimensionar** para iniciar a ação de redimensionamento.

Redimensionamento necessita de uma máquina virtual ativamente em execução para reiniciar. Se a máquina virtual está num ambiente de produção, recomendamos que execute a operação de redimensionamento após o expediente. O reinício de agendamento pode reduzir as interrupções causadas por uma indisponibilidade em instantes.

## <a name="verify-the-action"></a>Certifique-se a ação

Quando o redimensionamento de VM for concluída com êxito, é apresentada uma notificação do Azure.

![Notificação de máquina de virtual redimensionada com êxito](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização para ver o potencial ineficiências de utilização de custos
> * Agir sobre uma recomendação para redimensionar uma máquina virtual para uma opção mais rentável
> * Certifique-se a ação para se certificar de que a máquina virtual foi redimensionada com êxito

Se já não leu o artigo de gestão de custos de melhores práticas, ele fornece diretrizes de alto nível e princípios a ter em consideração para o ajudar a gerir os custos.

> [!div class="nextstepaction"]
> [Melhores práticas de gestão de custos](cost-mgt-best-practices.md)
