---
title: Monitorar e controlar utilização de serviços gratuitos do Azure | Documentos da Microsoft
description: Saiba como verificar a utilização de serviços gratuitos. Utilize csv de utilização e o portal do Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: cwatson
ms.openlocfilehash: cb3584101dea4dc8d8d888632175415480a6a1b3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581533"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Verificar a utilização de serviços gratuitos incluídos na sua conta gratuita do Azure 

Não são cobradas serviços incluídos gratuitamente com a conta gratuita do Azure, a menos que excede os limites destes serviços. Para permanecerem com os limites, pode utilizar o portal do Azure ou o ficheiro de utilização para monitorizar e controlar a utilização de serviços gratuitos. 

## <a name="check-usage-on-the-azure-portal"></a>Verificar a utilização no portal do Azure

1.  Inicie sessão no [Portal do Azure]( http://portal.azure.com).

2.  Na área de navegação esquerdo, selecione **todos os serviços**.

3.  Selecione **Subscrições**.

4.  Selecione a subscrição que criou quando se inscreveu no gratuitamente conta.

    ![Captura de ecrã que mostra todas as subscrições](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A seção de visão geral mostra-lhe informações essenciais sobre a sua subscrição, como o ID de subscrição, oferecem o tipo e o nome da subscrição. Também pode encontrar informações sobre o quando o seu crédito de conta gratuita seria expirar.

    ![Captura de ecrã que mostra as informações essenciais de subscrição](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Desloque-se para baixo para localizar informações no seus custos atuais e previstas. O custo inclui a utilização de serviços não incluídos na sua conta gratuita e a utilização que exceda os limites de serviços gratuitos. 

    ![Captura de ecrã que mostra as informações de custo de subscrição](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final da seção de visão geral tem uma tabela na utilização de serviços gratuitos. 

    ![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela contém as seguintes colunas:

* **Nome do medidor:** identifica a unidade de medida para o medidor de consumo. Para saber mais sobre o serviço para o mapeamento de medidores, veja [compreender o serviço gratuito para o mapeamento de medidores](billing-understand-free-service-meter-mapping.md). 
* **Utilização/limite:** utilização e o limite para o Medidor do mês atual. Também pode encontrar estas informações na barra de status.
* **Estado:** estado de utilização do medidor de. Com base no seu padrão de utilização, pode ter um destes statutes.
  * **Não está em utilização:** não tiver utilizado o medidor ou a utilização para o contador não atingiu o sistema de faturação.
  * **Foi excedido em \<data >:** excedeu o limite para o medidor no \<data >.
  * **Pouco provável que Exceed:** é pouco provável de exceder o limite para o medidor.
  * **Excede em \<data >:** que é provável que exceder o limite para o medidor no \<data >.


## <a name="check-usage-through-the-usage-file"></a>Verificar a utilização por meio do arquivo de utilização

O ficheiro de utilização fornece informações granulares para a sua subscrição do Azure. Pode baixar seu mensal e diariamente ficheiro de utilização do Centro de contas do Azure. Para saber como transferir o ficheiro de utilização e compreender o acesso necessário, veja [obter fatura e utilização](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no ficheiro de utilização, veja [compreender os termos na sua utilização](billing-understand-your-usage.md). 

O ficheiro de utilização contém informações de utilização de serviços gratuitos e pagos. Medidores de serviço gratuito teria **gratuito** anexada ao final do nome do medidor. Para encontrar medidores gratuitos, abra o ficheiro no excel e filtro os **coluna de categoria do medidor** células que contêm texto **- gratuito** (Utilize filtros de texto &rarr; filtro Contains) &nbsp;

![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).