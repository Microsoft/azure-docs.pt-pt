---
title: Monitorizar e controlar a utilização dos serviços gratuitos do Azure
description: Saiba como consultar a utilização dos serviços gratuitos no portal do Azure. Os serviços incluídos nas contas gratuitas não incorrem em custos, a não ser que exceda os limites dos mesmos.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: banders
ms.openlocfilehash: c7c28e64822a6aefa17e8baa4ef42a3b3fea8adb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589781"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Verificar a utilização dos serviços gratuitos incluídos com a conta gratuita do Azure

Os serviços incluídos gratuitamente na conta gratuita do Azure não lhe serão faturados, a não ser que exceda os limites dos serviços. Para não exceder os limites, pode utilizar o portal do Azure para controlar a utilização dos serviços gratuitos.

## <a name="check-usage-in-the-azure-portal"></a>Consultar a utilização no portal do Azure

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
1.  Escreva **Subscrições** na caixa de pesquisa.  
    ![Captura de ecrã que mostra a pesquisa de subscrições no portal](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Selecione a subscrição que criou quando se inscreveu na conta gratuita do Azure.
1.  Desloque-se para baixo para localizar a tabela que mostra a utilização do serviço gratuito.  
    ![Captura de ecrã que mostra a utilização dos serviços gratuitos](./media/check-free-service-usage/subscription-usage-free-services.png)

A tabela tem as seguintes colunas:

* **Medidor:** identifica a unidade de medida do serviço a ser consumido.
* **Utilização/Limite:** utilização e limite do mês atual para o medidor.
* **Estado:** o estado de utilização do serviço. Com base na utilização, pode ter um dos seguintes estados:
  * **Não está em utilização:** não utilizou o medidor ou a utilização do medidor não chegou ao sistema de faturação.
  * **Excedido em \<Date>:** Excedeu o limite do medidor em \<Date>.
  * **Pouco Provável que Exceda:** é pouco provável que exceda o limite do medidor.
  * **Excede em \<Date>:** É provável que exceda o limite do medidor em \<Date>.

> [!IMPORTANT]
>
> Os serviços gratuitos só estão disponíveis para a subscrição que foi criada quando se inscreveu na conta gratuita do Azure. Se não conseguir ver a tabela dos serviços gratuitos na página de descrição geral da subscrição, significa que não estão disponíveis para a subscrição.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Atualizar a conta gratuita do Azure](upgrade-azure-subscription.md)
