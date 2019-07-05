---
title: Monitorizar e controlar a utilização do serviço gratuito do Azure
description: Saiba como verificar a utilização do serviço gratuito no ficheiro CSV de utilização e o portal do Azure.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491431"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Verificar a utilização do serviço gratuito incluída com a sua conta gratuita do Azure

Não lhe serem cobrados serviços incluídos gratuitamente com uma conta gratuita do Azure, a menos que excede os limites dos serviços. Para permanecerem nos limites, pode utilizar o portal do Azure ou o ficheiro de utilização para monitorizar e controlar a utilização do serviço gratuito.

## <a name="check-usage-in-the-azure-portal"></a>Verificar a utilização no portal do Azure

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).

2.  Na área de navegação esquerdo, selecione **todos os serviços**.

3.  Selecione **Subscrições**.

4.  Selecione a subscrição que criou quando se inscreveu no gratuitamente conta.

    ![Captura de ecrã que mostra todas as subscrições](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A seção de visão geral mostra informações essenciais sobre a sua subscrição. Por exemplo, o ID de subscrição, o tipo de oferta e o nome da subscrição. Também pode encontrar informações quando o seu crédito de conta gratuita expira.

    ![Captura de ecrã que mostra as informações essenciais de subscrição](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Desloque-se para baixo para localizar informações sobre os seus custos atuais e previstas. O custo inclui o uso de serviço não incluído com a sua conta gratuita e a utilização que exceda os limites de serviços gratuitos.

    ![Captura de ecrã que mostra as informações de custo de subscrição](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final da seção de visão geral tem uma tabela que mostra a utilização do serviço gratuito.

    ![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela tem as seguintes colunas:

* **Nome do medidor:** Identifica a unidade de medida para o medidor de consumo. Para saber mais sobre o serviço para o mapeamento de medidores, veja [compreender o serviço gratuito para o mapeamento de medidores](billing-understand-free-service-meter-mapping.md).
* **Utilização/limite:** Utilização e limite para o Medidor do mês atual. Também pode encontrar estas informações na barra de status.
* **Status:** Estado do medidor de utilização. Com base no seu padrão de utilização, pode ter um dos statutes seguintes:
  * **Não está em utilização:** Ainda não utilizou o medidor ou a utilização para o contador ainda não atingiu o sistema de faturação.
  * **Foi excedido em \<data >:** Excedeu o limite para o medidor no \<data >.
  * **Pouco provável de exceder:** É pouco provável de exceder o limite para o medidor.
  * **Excede em \<data >:** É provável que exceda o limite para o medidor no \<data >.

## <a name="check-usage-with-the-usage-file"></a>Verificar a utilização com o ficheiro de utilização

O ficheiro de utilização fornece informações detalhadas para a sua subscrição do Azure. Pode transferir o ficheiro de utilização diária e mensal a partir do Centro de contas do Azure. Para saber como transferir o ficheiro de utilização e compreender o acesso necessário, veja [obter fatura e utilização](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no ficheiro de utilização, veja [compreender os termos na sua utilização](billing-understand-your-usage.md).

O ficheiro de utilização tem informações de utilização de serviços gratuitos e pagos. Medidores de serviço gratuito teria **gratuito** anexada ao final do nome do medidor. Para encontrar medidores gratuitos, abra o ficheiro no excel e filtro os **coluna de categoria do medidor** células que tenham o texto **- gratuito** (Utilize filtros de texto &rarr; filtro Contains).


![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- [Atualizar a sua subscrição](billing-upgrade-azure-subscription.md)
