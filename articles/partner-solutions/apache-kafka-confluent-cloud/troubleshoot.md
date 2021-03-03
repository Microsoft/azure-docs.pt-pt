---
title: Resolução de problemas Apache Kafka para Soluções de parceiros Confluent Cloud - Azure
description: Este artigo fornece informações sobre resolução de problemas e perguntas frequentes (FAQ) para Confluent Cloud on Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709402"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Resolução de problemas Apache Kafka para soluções Cloud Confluent

Este documento contém informações sobre a resolução de problemas das suas soluções que utilizam o Apache Kafka para a Nuvem Confluente.

Se não encontrar uma resposta ou não conseguir resolver um problema, [crie um pedido através do portal Azure](manage.md#get-support) ou contacte o Suporte [Confluente](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Não consigo encontrar oferta no Mercado

Para encontrar a oferta no Mercado Azure, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.
1. Procure _por Apache Kafka na Nuvem Confluente._
1. Selecione o azulejo da aplicação.

Se a oferta não for apresentada, contacte o [suporte da Confluent](https://support.confluent.io). Sua identificação do inquilino do Azure Ative Directory deve estar na lista de inquilinos permitidos. Para aprender a encontrar a identificação do seu inquilino, veja [como encontrar o seu ID do Azure Ative Directory.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="purchase-errors"></a>Erros de compra

* A compra falha porque um cartão de crédito válido não está ligado à subscrição do Azure ou um método de pagamento não está associado à subscrição.

  Utilize uma subscrição Azure diferente. Ou, adicione ou atualize o cartão de crédito ou o método de pagamento para a subscrição. Para obter mais informações, consulte [a atualização do método de crédito e pagamento.](../../cost-management-billing/manage/change-credit-card.md)

* A subscrição da EA não permite compras no Marketplace.

  Use uma subscrição diferente. Ou, verifique se a subscrição da EA está ativada para a compra do Marketplace. Para mais informações, consulte [as compras do Enable Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Se essas opções não resolverem o problema, contacte o [Suporte Confluente](https://support.confluent.io).

## <a name="conflict-error"></a>Erro de conflito

Se já se registou anteriormente para a Confluent Cloud, tem de usar um novo endereço de e-mail para criar outro recurso da organização Da Nuvem Confluente. Ao utilizar um endereço de e-mail previamente registado, obtém-se um erro **de conflito.** Re-registrar mas desta vez com um novo endereço de e-mail.

## <a name="deploymentfailed-error"></a>Implementação Erro falhado

Se tiver um erro **de Implementação,** verifique o estado da sua subscrição Azure. Certifique-se de que não está suspenso e não tem problemas de faturação.

## <a name="resource-isnt-displayed"></a>O recurso não é exibido

Se a **visão geral** ou as lâminas **de exclusão** para nuvem confluente não forem apresentadas no portal, tente refrescar a página. Este erro pode ser um problema intermitente com o portal. Se isso não funcionar, contacte o [suporte da Confluent.](https://support.confluent.io)

Se o recurso Cloud Confluent não for encontrado na lista de **recursos** Azure, contacte o suporte [da Confluent](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>A criação de recursos leva muito tempo

Se o processo de implantação demorar mais de três horas a ser concluído, contacte o suporte.

Se a implementação falhar e o recurso Confluent Cloud tiver um estado de `Failed` , elimine o recurso. Após a eliminação, tente novamente criar o recurso.

## <a name="offer-plan-doesnt-load"></a>Plano de oferta não carrega

Este erro pode ser um problema intermitente com o portal Azure. Tente mover a oferta de novo.

## <a name="unable-to-delete"></a>Não é possível eliminar

Se não conseguir eliminar recursos confluentes, verifique se tem permissões para eliminar o recurso. Tem de ser autorizado a tomar ações microsoft.Confluent/*/Delete. Para obter informações sobre permissões de visualização, consulte [as atribuições de funções list Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-list-portal.md).

Se tiver as permissões corretas mas ainda não conseguir eliminar o recurso, contacte o [suporte da Confluent](https://support.confluent.io). Esta condição pode estar relacionada com a política de retenção da Confluent. O suporte confluente pode eliminar a organização e o endereço de e-mail para si.

## <a name="unable-to-use-single-sign-on"></a>Incapaz de usar um único sinal

Se o SSO não estiver a trabalhar para o portal Confluent Cloud SaaS, verifique se está a usar o e-mail correto do Azure Ative Directory. Também deve ter consentido em permitir o acesso ao software Confluent Cloud como um portal de serviço (SaaS). Para obter mais informações, consulte a [orientação de inscrição única](manage.md#single-sign-on).

Se o problema persistir, contacte o [suporte da Confluente](https://support.confluent.io).

## <a name="next-steps"></a>Passos seguintes

Saiba [como gerir o seu exemplo](manage.md) de Apache Kafka para a Nuvem Confluente.
