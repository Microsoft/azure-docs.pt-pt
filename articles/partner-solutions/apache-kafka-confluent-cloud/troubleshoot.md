---
title: Resolução de problemas Apache Kafka para Soluções de parceiros Confluent Cloud - Azure
description: Este artigo fornece informações sobre resolução de problemas e perguntas frequentes (FAQ) para Confluent Cloud on Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253529"
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
