---
title: Tutorial - reduzir os custos do Azure com recomendações de otimização | Documentos da Microsoft
description: Este tutorial ajuda-o a reduzir os custos do Azure, quando agir em recomendações de otimização.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: bb18a7338eb688ab427725933b9fc3f73b44b502
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992056"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: Otimizar os custos de recomendações

O Azure Cost Management funciona com o Assistente do Azure para fornecer recomendações de otimização de custos. O Assistente do Azure ajuda a otimizar e melhorar a eficiência ao identificar recursos subutilizados e inativos. Este tutorial explica-lhe um exemplo de onde identificar subutilizados recursos do Azure e, em seguida, tomar providências para reduzir os custos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização para ver o potencial ineficiências de utilização de custos
> * Agir sobre uma recomendação para redimensionar uma máquina virtual para uma opção mais rentável
> * Certifique-se a ação para se certificar de que a máquina virtual foi redimensionada com êxito

## <a name="prerequisites"></a>Pré-requisitos
As recomendações estão disponíveis para todos os clientes do [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos para ver os dados de custos.

- Subscrição
- Grupo de recursos

Tem de ter máquinas virtuais ativas com, pelo menos, de 14 dias de atividade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Ver recomendações de otimização de custos

No portal do Azure, clique em **Cost Management + Faturação** na lista de serviços. Em seguida, na lista, no **Cost Management**, selecione **recomendações do assistente**. Recomendações de custos do assistente são apresentadas.

![Recomendações do assistente](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A lista de recomendações identifica ineficiências de utilização ou mostra as recomendações de compra que podem ajudar a poupar dinheiro adicional. O totalizados **potenciais poupanças anuais** mostra a quantidade total, que pode economizar se encerrar ou desalocar todas as suas VMs que cumpram as regras de recomendação. Se não quiser encerrá-las, deve considerar o redimensionamento-los para um SKU de VM mais barato.

O **impacto** categoria, juntamente com o **potenciais poupanças anuais**, foram concebidos para ajudar a identificar as recomendações que têm o potencial de guardar tanto quanto possível. As recomendações de alto impacto [comprar reservado instâncias de máquina virtual para poupar dinheiro sobre os custos de pay as you go](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e [máquina de virtual de otimizar gastar ao redimensionamento ou encerrar as instâncias subutilizadas](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances). Recomendações de impacto intermédio [reduzir os custos, eliminando o circuito do ExpressRoute não aprovisionado](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits) e [reduzir os custos, eliminar ou reconfigurar gateways de rede virtual inativo](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways).

## <a name="act-on-a-recommendation"></a>Agir sobre uma recomendação

O Assistente do Azure monitoriza a utilização da máquina virtual durante 14 dias e, em seguida, identifica as máquinas virtuais subutilizadas. Máquinas virtuais cuja utilização da CPU é cinco por cento ou menos e utilização de rede é de sete MB ou menos para quatro ou mais dias são considerados máquinas de virtuais de baixa utilização.

Os 5% ou menos definição de utilização da CPU é o padrão, mas pode ajustar as definições. Para obter mais informações sobre como ajustar a definição, consulte a [configurar a regra de utilização de CPU média](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation) artigo [para a recomendação de máquina virtual de baixa utilização](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation).

Embora alguns cenários podem resultar numa utilização reduzida por design, que, muitas vezes, pode poupar dinheiro ao alterar o tamanho das suas máquinas virtuais para tamanhos de menos dispendiosos. Suas poupanças reais podem variar se escolher uma ação de redimensionamento. Vamos examinar um exemplo de redimensionamento de uma máquina virtual.

Na lista de recomendações, clique a **dimensionar ou encerramento subutilizados máquinas virtuais** recomendação. Na lista de candidatos de máquina virtual, selecione uma máquina virtual, de redimensionar e, em seguida, clique na máquina virtual. Detalhes da máquina virtual são apresentados para que pode verificar as métricas de utilização. O **potenciais poupanças anuais** valor é o que pode poupar se desligar ou remover a VM. Redimensionar uma VM irá provavelmente poupar dinheiro, mas não vai economizar o montante total das poupanças anuais potenciais.

![Detalhes da recomendação](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nos detalhes de VM, verifique a utilização da máquina virtual para confirmar que é um candidato de redimensionamento adequado.

![Detalhes da VM](./media/tutorial-acm-opt-recommendations/vm-details.png)

Tenha em atenção o tamanho da máquina virtual atual. Depois de verificar que a máquina virtual deve ser redimensionada, feche os detalhes da VM para que vê a lista de máquinas virtuais.

Na lista de candidatos para encerrar ou redimensionar, selecione **redimensionar a máquina virtual**.
![Redimensionar a máquina virtual](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Em seguida, é apresentada uma lista de opções de redimensionamento disponíveis. Escolha o método que vai dar o melhor desempenho e rentabilidade para o seu cenário. No exemplo a seguir, é redimensionado escolhido a opção de um **DS14\_V2** para um **DS13\_V2**. Seguir a recomendação economiza us $551.30/ mês ou US $6,615.60/ ano.

![Escolher um tamanho](./media/tutorial-acm-opt-recommendations/choose-size.png)

Depois de escolher um tamanho adequado, clique em **selecione** para iniciar a ação de redimensionamento.

Redimensionamento necessita de uma máquina virtual ativamente em execução para reiniciar. Se a máquina virtual está num ambiente de produção, recomendamos que execute a operação de redimensionamento após o expediente. O reinício de agendamento pode reduzir as interrupções causadas por uma indisponibilidade em instantes.

## <a name="verify-the-action"></a>Certifique-se a ação

Quando o redimensionamento de VM for concluída com êxito, é apresentada uma notificação do Azure.

![Notificação redimensionada](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver recomendações de otimização para ver o potencial ineficiências de utilização de custos
> * Agir sobre uma recomendação para redimensionar uma máquina virtual para uma opção mais rentável
> * Certifique-se a ação para se certificar de que a máquina virtual foi redimensionada com êxito

Se já não leu o artigo de gestão de custos de melhores práticas, ele fornece diretrizes de alto nível e princípios a ter em consideração para o ajudar a gerir os custos.

> [!div class="nextstepaction"]
> [Melhores práticas de gestão de custos](cost-mgt-best-practices.md)
