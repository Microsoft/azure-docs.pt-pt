---
title: Monitorizar e controlar a utilização dos serviços gratuitos do Azure
description: Saiba como consultar a utilização dos serviços gratuitos no portal do Azure.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992832"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Verificar a utilização dos serviços gratuitos incluídos com a conta gratuita do Azure

Os serviços incluídos gratuitamente na conta gratuita do Azure não lhe serão faturados, a não ser que exceda os limites dos serviços. Para não exceder os limites, pode utilizar o portal do Azure para controlar a utilização dos serviços gratuitos.

## <a name="check-usage-in-the-azure-portal"></a>Consultar a utilização no portal do Azure

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).

2.  Escreva **Subscrições** na caixa de pesquisa.

    ![Captura de ecrã que mostra a pesquisa de subscrições no portal](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Selecione a subscrição que criou quando se inscreveu na conta gratuita do Azure.

4.  Desloque-se para baixo para localizar a tabela que mostra a utilização do serviço gratuito.

    ![Captura de ecrã que mostra a utilização dos serviços gratuitos](./media/check-free-service-usage/subscription-usage-free-services.png)

    A tabela tem as seguintes colunas:

* **Medidor:** identifica a unidade de medida do serviço a ser consumido.
* **Utilização/Limite:** utilização e limite do mês atual para o medidor.
* **Estado:** o estado de utilização do serviço. Com base na utilização, pode ter um dos seguintes estados:
  * **Não está em utilização:** não utilizou o medidor ou a utilização do medidor não chegou ao sistema de faturação.
  * **Excedido em \<Data>:** excedeu o limite do medidor em \<Data>.
  * **Pouco Provável que Exceda:** é pouco provável que exceda o limite do medidor.
  * **Excede em \<Data>:** é provável que exceda o limite do medidor em \<Data>.

> [!IMPORTANT]
>
> Os serviços gratuitos só estão disponíveis para a subscrição que foi criada quando se inscreveu na conta gratuita do Azure. Se não conseguir ver a tabela dos serviços gratuitos na página de descrição geral da subscrição, significa que não estão disponíveis para a subscrição.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Atualizar a conta gratuita do Azure](upgrade-azure-subscription.md)
