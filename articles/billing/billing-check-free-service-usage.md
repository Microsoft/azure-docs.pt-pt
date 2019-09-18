---
title: Monitorizar e controlar a utilização dos serviços gratuitos do Azure
description: Saiba como consultar a utilização dos serviços gratuitos no portal do Azure e no ficheiro CSV de utilização.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491431"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Consultar a utilização dos serviços gratuitos incluídos na conta gratuita do Azure

Os serviços incluídos gratuitamente numa conta gratuita do Azure não lhe são faturados, a não ser que exceda os limites dos serviços. Para não exceder os limites, pode utilizar o portal do Azure ou o ficheiro de utilização para monitorizar e controlar a utilização dos serviços gratuitos.

## <a name="check-usage-in-the-azure-portal"></a>Consultar a utilização no portal do Azure

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).

2.  Na área de navegação esquerda, selecione **Todos os serviços**.

3.  Selecione **Subscrições**.

4.  Selecione a subscrição que criou quando se inscreveu na conta gratuita.

    ![Captura de ecrã que mostra todas as subscrições](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A secção de descrição geral mostra informações essenciais sobre a sua subscrição. Por exemplo, o ID da subscrição, o tipo de oferta e o nome da subscrição. Também pode encontrar informações quando o crédito da conta gratuita expirar.

    ![Captura de ecrã que mostra as informações essenciais da subscrição](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Desloque-se para baixo para encontrar informações sobre o custo atual e previsto. O custo inclui a utilização dos serviços não incluídos na conta gratuita e a utilização que excede os limites dos serviços gratuitos.

    ![Captura de ecrã que mostra as informações dos custos da subscrição](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final da secção de descrição geral tem uma tabela que mostra a utilização dos serviços gratuitos.

    ![Captura de ecrã que mostra a utilização dos serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela tem as seguintes colunas:

* **Nome do Medidor:** identifica a unidade de medida do medidor a ser consumido. Para saber mais sobre o mapeamento dos serviços para os medidores, veja [Compreender o mapeamento de serviços gratuitos para os medidores](billing-understand-free-service-meter-mapping.md).
* **Utilização/Limite:** utilização e limite do mês atual para o medidor. Também pode encontrar estas informações na barra de estado.
* **Estado:** o estado de utilização do medidor. Com base no padrão de utilização, pode ter um dos seguintes estados:
  * **Não está em utilização:** não utilizou o medidor ou a utilização do medidor não chegou ao sistema de faturação.
  * **Excedido em \<Data>:** excedeu o limite do medidor em \<Data>.
  * **Pouco Provável que Exceda:** é pouco provável que exceda o limite do medidor.
  * **Excede em \<Data>:** é provável que exceda o limite do medidor em \<Data>.

## <a name="check-usage-with-the-usage-file"></a>Consultar a utilização com o ficheiro de utilização

O ficheiro de utilização fornece informações detalhadas sobre a sua subscrição do Azure. Pode transferir o ficheiro da utilização mensal e diária no Centro de Contas do Azure. Para saber como transferir o ficheiro de utilização e compreender o acesso necessário, veja [Obter Fatura e Utilização](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no ficheiro de utilização, veja [Compreender os termos relativos à utilização](billing-understand-your-usage.md).

O ficheiro de utilização tem informações de utilização dos serviços gratuitos e pagos. Os medidores dos serviços gratuitos têm o termo **Gratuito** no fim do nome do medidor. Para localizar os medidores gratuitos, abra o ficheiro no Excel e filtre a coluna **Categoria de Medidor** para ver as células com o termo **- Gratuito** (Utilize os Filtros de Texto &rarr; filtro Contém).


![Captura de ecrã que mostra a utilização dos serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Atualizar a subscrição](billing-upgrade-azure-subscription.md)
