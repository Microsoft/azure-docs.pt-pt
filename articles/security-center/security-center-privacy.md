---
title: Gerenciar dados do usuário na central de segurança do Azure | Microsoft Docs
description: " Saiba como gerenciar dados do usuário na central de segurança do Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 40299b2ff9a20792cf25828051d2f937f3f1e9da
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201283"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gerenciar dados do usuário na central de segurança do Azure
Este artigo fornece informações sobre como você pode gerenciar os dados do usuário na central de segurança do Azure. O gerenciamento de dados do usuário inclui a capacidade de acessar, excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode acessar dados do cliente dentro da ferramenta. Consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções de leitor, proprietário e colaborador. Consulte [Administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md) para saber mais sobre a função de administrador da conta.

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisando e identificando dados pessoais
Um usuário da central de segurança pode exibir seus dados pessoais por meio do portal do Azure. A central de segurança só armazena detalhes de contato de segurança, como endereços de email e números de telefone. Consulte [fornecer detalhes de contato de segurança na central de segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações.

No portal do Azure, um usuário pode exibir as configurações de IP permitidas usando o recurso de acesso à VM just in time da central de segurança. Veja [Gerir o acesso da máquina virtual através do just in time](security-center-just-in-time.md), para obter mais informações.

No portal do Azure, um usuário pode exibir os alertas de segurança fornecidos pela central de segurança, incluindo os endereços IP e os detalhes do invasor. Consulte [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações.

## <a name="classifying-personal-data"></a>Classificando dados pessoais
Você não precisa classificar os dados pessoais encontrados no recurso de contato de segurança da central de segurança. Os dados salvos são um endereço de email (ou vários endereços de email) e um número de telefone. [Os dados de contato](security-center-provide-security-contact-details.md) são validados pela central de segurança.

Você não precisa classificar os endereços IP e os números de porta salvos pelo recurso [just in time](security-center-just-in-time.md) da central de segurança.

Somente um usuário atribuído à função de administrador pode classificar dados pessoais [exibindo alertas](security-center-managing-and-responding-alerts.md) na central de segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protegendo e controlando o acesso a dados pessoais
Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador de conta pode acessar [dados de contato de segurança](security-center-provide-security-contact-details.md).

Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode acessar suas políticas [just in time](security-center-just-in-time.md) .

Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode exibir seus [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>A atualizar os dados pessoais
Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode atualizar [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode atualizar suas [políticas just in time](security-center-just-in-time.md).

Um administrador de conta não pode editar incidentes de alerta. Um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados de segurança e é somente leitura.

## <a name="deleting-personal-data"></a>A eliminar os dados pessoais
Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode excluir [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode excluir as [políticas just in time](security-center-just-in-time.md) por meio do portal do Azure.

Um usuário da central de segurança não pode excluir incidentes de alerta. Devido a necessidades de segurança, um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados somente leitura.

## <a name="exporting-personal-data"></a>Exportar os dados pessoais
Um usuário da central de segurança que atribuiu a função de leitor, proprietário, colaborador ou administrador de conta pode exportar [dados de contato de segurança](security-center-provide-security-contact-details.md) :

- Executando uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um usuário da central de segurança que atribuiu a função de administrador da conta pode exportar as [políticas just in time](security-center-just-in-time.md) que contêm os endereços IP:

- Executando uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um administrador de conta pode exportar os detalhes do alerta:

- Executando uma cópia do portal do Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Consulte [obter alertas de segurança (obter coleção)](https://msdn.microsoft.com/library/mt704050.aspx) para obter mais informações.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringindo o uso de dados pessoais para criação de perfil ou marketing sem consentimento
Um usuário da central de segurança pode optar por recusar excluindo seus [dados de contato de segurança](security-center-provide-security-contact-details.md).

[Os dados Just in time](security-center-just-in-time.md) são considerados dados não identificáveis e são mantidos por um período de 30 dias.

[Os dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e são mantidos por um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os logs de auditoria de contato de segurança, just in time, e atualizações de alertas são mantidos nos [logs de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como gerenciar dados de usuário, consulte [gerenciar dados de usuário encontrados em uma investigação da central de segurança do Azure](security-center-investigation-user-data.md).
