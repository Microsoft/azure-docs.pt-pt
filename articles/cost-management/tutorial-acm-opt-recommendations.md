---
title: Tutorial-reduzir os custos do Azure com recomendações de otimização | Microsoft Docs
description: Este tutorial ajuda a reduzir os custos do Azure quando você atua em recomendações de otimização.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935755"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: otimizar os custos de recomendações

O Azure Cost Management funciona com o Assistente do Azure para dar recomendações de otimização de custos. O Assistente do Azure ajuda-o a otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados. Este tutorial explica um exemplo em que você identifica os recursos subutilizados do Azure e, em seguida, passa a agir para reduzir os custos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Exibir recomendações de otimização de custo para exibir possíveis ineficiências de uso
> * Agir em uma recomendação para redimensionar uma máquina virtual para uma opção mais econômica
> * Verifique a ação para garantir que a máquina virtual foi redimensionada com êxito

## <a name="prerequisites"></a>Pré-requisitos
As recomendações estão disponíveis para uma variedade de escopos e tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). Tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos para ver os dados de custos. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Subscrição
- Grupo de recursos

Você deve ter máquinas virtuais ativas com pelo menos 14 dias de atividade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Exibir recomendações de otimização de custo

Para exibir as recomendações de otimização de custo para uma assinatura, abra o escopo desejado no portal do Azure e selecione **recomendações do Advisor**.

Para exibir as recomendações para um grupo de gerenciamento, abra o escopo desejado no portal do Azure e selecione **análise de custo** no menu. Use o **escopo** do Pill para alternar para um escopo diferente, como um grupo de gerenciamento. Selecione **recomendações do Advisor** no menu. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

![Recomendações do supervisor de gerenciamento de custos mostradas na portal do Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A lista de recomendações identifica as ineficiências de uso ou mostra as recomendações de compra que podem ajudá-lo a economizar dinheiro adicional. A economia de **potencial anual** total mostra o valor total que você pode salvar se você desligar ou desalocar todas as suas VMs que atendem às regras de recomendação. Se você não quiser desligá-los, considere redimensioná-los para uma SKU de VM menos dispendiosa.

A categoria de **impacto** , juntamente com a **economia potencial anual**, foi projetada para ajudar a identificar as recomendações que têm o potencial de economizar o máximo possível.

As recomendações de alto impacto incluem:
- [Comprar instâncias de máquinas virtuais reservadas para economizar dinheiro nos custos pagos conforme o uso](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Otimizar o gasto de máquinas virtuais redimensionando ou desligando instâncias subutilizadas](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Usar o armazenamento padrão para armazenar instantâneos de Managed Disks](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

As recomendações de impacto médio incluem:
- [Excluir Azure Data Factory pipelines que estão falhando](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduzir custos eliminando circuitos do ExpressRoute não provisionados](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduzir custos ao excluir ou reconfigurar gateways de rede virtual ociosas](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agir em uma recomendação

O Azure Advisor monitora o uso da sua máquina virtual por sete dias e identifica máquinas virtuais subutilizadas. Máquinas virtuais cuja utilização da CPU é de cinco% ou menos e o uso da rede é de sete MB ou menos para quatro ou mais dias são considerados máquinas virtuais de baixa utilização.

A configuração de utilização de CPU de 5% ou menos é a padrão, mas você pode ajustar as configurações. Para obter mais informações sobre como ajustar a configuração, consulte [Configurar a regra média de utilização da CPU ou a recomendação de máquina virtual de uso baixo](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Embora alguns cenários possam resultar em baixa utilização por design, muitas vezes você pode economizar dinheiro alterando o tamanho de suas máquinas virtuais para tamanhos menos caros. Sua economia real pode variar se você escolher uma ação de redimensionamento. Vamos examinar um exemplo de redimensionamento de uma máquina virtual.

Na lista de recomendações, clique no **tamanho correto ou desligando a recomendação de máquinas virtuais subutilizadas** . Na lista de candidatos à máquina virtual, escolha uma máquina virtual para redimensionar e clique na máquina virtual. Os detalhes da máquina virtual são mostrados para que você possa verificar as métricas de utilização. O valor de **economia anual potencial** é o que você pode salvar se você desligar ou remover a VM. O redimensionamento de uma VM provavelmente economizará dinheiro, mas você não economizará a quantidade total de economia anual potencial.

![Exemplo de detalhes de recomendação](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nos detalhes da VM, verifique a utilização da máquina virtual para confirmar se é um candidato de redimensionamento adequado.

![Detalhes da VM de exemplo mostrando a utilização histórica](./media/tutorial-acm-opt-recommendations/vm-details.png)

Anote o tamanho da máquina virtual atual. Depois de verificar se a máquina virtual deve ser redimensionada, feche os detalhes da VM para ver a lista de máquinas virtuais.

Na lista de candidatos a serem desligados ou redimensionados, selecione * * Redimensionar *&lt;FromVirtualMachineSKU&gt;* para *&lt;ToVirtualMachineSKU&gt;* * *.
![exemplo de recomendação com a opção de redimensionar a máquina virtual](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Em seguida, você verá uma lista de opções de redimensionamento disponíveis. Escolha aquela que fornecerá o melhor desempenho e a economia de custo para seu cenário. No exemplo a seguir, a opção escolhida redimensiona de **Standard_D8s_v3** para **Standard_D2s_v3**.

![Exemplo de uma lista de tamanhos de VM disponíveis onde você pode escolher um tamanho](./media/tutorial-acm-opt-recommendations/choose-size.png)

Depois de escolher um tamanho adequado, clique em **redimensionar** para iniciar a ação de redimensionamento.

O redimensionamento requer que uma máquina virtual em execução ativamente seja reiniciada. Se a máquina virtual estiver em um ambiente de produção, recomendamos que você execute a operação de redimensionamento após o horário comercial. O agendamento da reinicialização pode reduzir as interrupções causadas por uma indisponibilidade momentaneamente.

## <a name="verify-the-action"></a>Verificar a ação

Quando o redimensionamento da VM for concluído com êxito, uma notificação do Azure será mostrada.

![Notificação de máquina virtual redimensionada com êxito](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Exibir recomendações de otimização de custo para exibir possíveis ineficiências de uso
> * Agir em uma recomendação para redimensionar uma máquina virtual para uma opção mais econômica
> * Verifique a ação para garantir que a máquina virtual foi redimensionada com êxito

Se você ainda não leu o artigo práticas recomendadas de gerenciamento de custos, ele fornece diretrizes e princípios de alto nível a serem considerados para ajudar a gerenciar os custos.

> [!div class="nextstepaction"]
> [Práticas recomendadas de gerenciamento de custos](cost-mgt-best-practices.md)
