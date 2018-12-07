---
title: Tutorial – Rever a utilização e os custos através da Cloudyn no Azure | Microsoft Docs
description: Neste tutorial, reveja a utilização e os custos para ver tendências, detetar ineficiências e criar alertas.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: benshy
ms.openlocfilehash: a7212f64a550b3567affb1f6369ab5655dabafb1
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001676"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Tutorial: rever a utilização e os custos

A Cloudyn mostra-lhe a utilização e os custos, de modo a que possa ver tendências, detetar ineficiências e criar alertas. Todos os dados de utilização e de custos são apresentados nos relatórios e nos dashboards da Cloudyn. Os exemplos neste tutorial orientam-no ao longo da revisão da utilização e dos custos através dos dashboards e dos relatórios.

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, veja [Azure Cost Management](overview-cost-mgt.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver as tendências de utilização e de custos
> * Detetar ineficiências na utilização
> * Criar alertas para despesas ou gastos excessivos invulgares
> * Exportar dados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma conta do Azure.
- Tem de ter um registo de avaliação ou uma subscrição paga da Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Abrir o portal da Cloudyn

A utilização e os custos são revistos no portal da Cloudyn. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para https://azure.cloudyn.com e inicie sessão.

## <a name="track-usage-and-cost-trends"></a>Ver as tendências de utilização e de custos

O dinheiro efetivamente gasto para utilização e custos é controlado com os relatórios Ao Longo do Tempo, de modo a identificar tendências. Para começar a procurar tendências, utilize o relatório Custo Efetivo ao Longo do Tempo. No canto superior esquerdo do portal, clique em **Custos** > **Análise de Custos** > **Custo Efetivo ao Longo do Tempo**. Da primeira vez que abrir o relatório, não estão aplicados grupos ou filtros ao mesmo.

Segue-se um exemplo de relatório:

![relatório de exemplo](./media/tutorial-review-usage/actual-cost01.png)

O relatório mostra todos os gastos ao longo dos últimos 30 dias. Para ver os gastos apenas para serviços do Azure, aplique o grupo Serviços e filtre por todos os serviços do Azure. A imagem seguinte mostra os serviços filtrados.

![serviços filtrados](./media/tutorial-review-usage/actual-cost02.png)

No exemplo anterior, foi gasto menos dinheiro a partir de 29-10-2018. No entanto, haver demasiadas colunas pode ocultar uma tendência óbvia. Pode alterar a vista do relatório para uma linha ou área do gráfico para ver os dados apresentados noutras vistas. A imagem seguinte mostra a tendência com mais clareza.

![tendência no relatório](./media/tutorial-review-usage/actual-cost03.png)

Continuando com o exemplo, pode ver que o custo de VMs do Azure diminuiu. Os custos para outros serviços do Azure também começou a diminuir nesse dia. Então, o que é que originou a redução nas despesas? Neste exemplo, um projeto grande de trabalho foi concluído, pelo que o consumo de muitos serviços do Azure também diminuíram.

Para ver um tutorial em vídeo sobre como pode monitorizar as tendências de utilização e de custos, veja [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analisar os dados de faturação na cloud vs. tempo com a Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Detetar ineficiências na utilização

Os relatórios de otimizador melhoram a eficiência, otimizam a utilização e identificam formas de poupar dinheiro nos seus recursos da cloud. São especialmente úteis para recomendações de dimensionamento económico, com o objetivo de ajudar a reduzir o número de VMs inativas ou dispendiosas.

Um problema comum que afeta as organizações quando movem inicialmente os recursos para a cloud é a estratégia de virtualização. Muitas vezes, utilizam uma abordagem semelhante à que utilizaram para criar as máquinas virtuais para o ambiente de virtualização no local. E partem do princípio de que basta mover as VMs no local para a cloud tal como estão para que os custos baixem. No entanto, é pouco provável que esta abordagem tenha esse efeito.

O problema é que as respetivas infraestruturas já estavam pagas. Os utilizadores podiam criar e manter em execução VMs grandes se quisessem, inativas ou não, e as consequências eram poucas. Mover VMs grandes ou inativas para a cloud leva a um provável *aumento* dos custos. É importante ter em conta a alocação de custos para recursos quando celebra contratos com fornecedores de serviços cloud. Tem de pagar aquilo com que se comprometeu, quer utilize os recursos na totalidade ou não.

O relatório Recomendações de Dimensionamento Económico identifica potenciais poupanças anuais, ao comparar a capacidade do tipo de instância de VM com os dados históricos de utilização de CPU e memória.  

No menu na parte superior do portal, clique em **Otimizador** > **Otimização de Dimensionamento** > **Recomendações de Dimensionamento Económico**. Se for útil, aplique um filtro para reduzir os resultados. Segue-se uma imagem de exemplo.

![VMs do Azure](./media/tutorial-review-usage/sizing01.png)

Neste exemplo, seguir as recomendações para alterar os tipos de instância de VM levaria a uma poupança de 2382 $. Clique no sinal de adição (+) em **Detalhes** na primeira recomendação. Seguem-se os detalhes da primeira recomendação.

![detalhes da recomendação](./media/tutorial-review-usage/sizing02.png)

Clique no sinal de mais junto a **Lista de Candidatos** para ver os IDs das instâncias de VMs.

![Lista de Candidatos](./media/tutorial-review-usage/sizing03.png)

Para ver um tutorial em vídeo sobre como detetar ineficiências de utilização, veja [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Otimizar o Tamanho das VMs na Cloudyn).

O Azure Cost Management também fornece recomendações de poupança de custos para serviços do Azure. Para obter mais informações, veja [Tutorial: otimizar os custos de recomendações](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Criar alertas para despesas invulgares

Pode alertar os outros intervenientes automaticamente sobre despesas anómalas e riscos de incorrer em gastos excessivos. Pode criar rápida e facilmente os alertas através de relatórios que os suportem com base em limiares de orçamentos e de custos.

Pode utilizar qualquer relatório Custos para criar um alerta para qualquer despesa. Neste exemplo, utilize o relatório Custo Efetivo ao Longo do Tempo para ser notificado quando os gastos com VMs do Azure estiverem prestes a atingir o seu orçamento total. Todos os passos seguintes são necessários para criar o alerta. No menu na parte superior do portal, clique em **Custos** > **Análise de Custos** > **Custo Efetivo ao Longo do Tempo**. Defina **Grupos** como **Serviços** e defina **Filtrar por serviço** como **Azure/VM**. No canto superior direito do relatório, clique em **Ações** e selecione **Agendar relatório**.

Na caixa Guardar ou Agendar este relatório, utilize o separador **Agendamento** para enviar um e-mail para si próprio com o relatório de acordo com a frequência que pretenda. Lembre-se de selecionar **Enviar por e-mail**. Todos os grupos, etiquetas e filtragem que utilizou são incluídos no e-mail com o relatório. Clique no separador **Limiar** e selecione **Custo Efetivo vs. Limiar**. Se tiver um orçamento total de 20 000 $ e quiser ser notificado quando os custos estiverem quase a atingir metade do orçamento, pode criar um **Alerta vermelho** nos 10 000 $ e um **Alerta amarelo** nos 9 000 $. Não inclua vírgulas nos valores introduzidos. Depois, escolha o número de alertas consecutivos. Quando receber o número total de alertas que especificou, não são enviados alertas adicionais. Guarde o relatório agendado.

![relatório de exemplo](./media/tutorial-review-usage/schedule-alert01.png)

Também pode escolher a métrica Percentagem de Custos vs. Limiar de orçamento para criar os alertas. Com esta métrica, pode utilizar percentagens do orçamento em vez de valores de moeda.

## <a name="export-data"></a>Exportar dados

Da mesma forma que cria alertas para relatórios, também pode exportar dados a partir de qualquer relatório. Por exemplo, poderá exportar uma lista de contas do Cloudyn ou outros dados do utilizador. Para exportar qualquer relatório, abra o relatório e, em seguida, no canto superior direito do relatório, clique em **Ações**. Algumas das ações que poderá realizar são **Exportar todos os dados de relatório**, para transferir ou imprimir as informações. Em alternativa, pode selecionar **Agendar relatório**, para agendar o relatório para ser enviado como um e-mail.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver as tendências de utilização e de custos
> * Detetar ineficiências na utilização
> * Criar alertas para despesas ou gastos excessivos invulgares
> * Exportar dados


Avance para o tutorial seguinte para aprender a prever despesas através de dados históricos.

> [!div class="nextstepaction"]
> [Prever despesas futuras](tutorial-forecast-spending.md)
