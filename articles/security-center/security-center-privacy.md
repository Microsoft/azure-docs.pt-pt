---
title: Gerir os dados de utilizador no Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como gerir os dados de utilizador no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: fcec410df631a58b76878a4cb327ca2fb04a2105
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117860"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gerir os dados de utilizador no Centro de segurança do Azure
Este artigo fornece informações sobre como pode gerir os dados de utilizador no Centro de segurança do Azure. Gestão de dados de utilizador inclui a capacidade de aceder, eliminar ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode aceder a dados do cliente dentro da ferramenta. Ver [funções incorporadas para controlo de acesso baseado em funções do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções do leitor e contribuinte proprietário. Ver [os administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md) para saber mais sobre a função de administrador de conta.

## <a name="searching-for-and-identifying-personal-data"></a>A procurar e identificar os dados pessoais
Um utilizador do Centro de segurança pode ver os dados pessoais, através do portal do Azure. Centro de segurança só armazena os detalhes de contacto de segurança, como endereços de e-mail e números de telefone. Ver [fornecer detalhes de contactos de segurança no Centro de segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações.

No portal do Azure, um usuário pode visualizar as configurações de IP permitidas através do Centro de segurança just-in-funcionalidade de acesso VM de tempo. Veja [Gerir o acesso da máquina virtual através do just in time](security-center-just-in-time.md), para obter mais informações.

No portal do Azure, um utilizador pode ver os alertas de segurança fornecidos pelo centro de segurança, incluindo endereços IP e os detalhes do invasor. Ver [gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações.

## <a name="classifying-personal-data"></a>Classificar dados pessoais
Não é necessário classificar dados pessoais encontrados no recurso de contacto de segurança do Centro de segurança. Os dados salvos são um endereço de e-mail (ou vários endereços de e-mail) e um número de telefone. [Entre em contato com dados](security-center-provide-security-contact-details.md) é validado pelo centro de segurança.

Não é necessário classificar os endereços IP e guardados pelo centro de segurança de números de porta [just-in-time](security-center-just-in-time.md) funcionalidade.

Apenas um utilizador atribuído a função de administrador, pode classificar dados pessoais pelo [visualizar alertas](security-center-managing-and-responding-alerts.md) no Centro de segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protegendo e controlando o acesso aos dados pessoais
Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode aceder [dados de contacto de segurança](security-center-provide-security-contact-details.md).

Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode aceder aos seus [just-in-time](security-center-just-in-time.md) políticas.

Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode ver as respetivas [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>A atualizar os dados pessoais
Um utilizador do Centro de segurança atribuída a função de proprietário, contribuinte, ou pode atualizar o administrador de conta [dados de contacto de segurança](security-center-provide-security-contact-details.md) através do portal do Azure.

A função de proprietário, contribuinte, atribuída a um utilizador de centro de segurança ou administrador de conta pode atualizar seus [just-in Políticas de tempo](security-center-just-in-time.md).

Um administrador de conta não é possível editar os incidentes de alertas. Uma [incidente de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e é só de leitura.

## <a name="deleting-personal-data"></a>A eliminar os dados pessoais
Um utilizador do Centro de segurança atribuída a função de proprietário, contribuinte, ou pode eliminar a conta de administrador [dados de contacto de segurança](security-center-provide-security-contact-details.md) através do portal do Azure.

A função de proprietário, contribuinte, atribuída a um utilizador de centro de segurança ou administrador de conta pode eliminar a [just-in Políticas de tempo](security-center-just-in-time.md) através do portal do Azure.

Um utilizador do Centro de segurança não é possível eliminar o alerta incidentes. Devido às necessidades de segurança, um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado apenas os dados de leitura.

## <a name="exporting-personal-data"></a>Exportar os dados pessoais
Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode exportar [dados de contacto de segurança](security-center-provide-security-contact-details.md) por:

- Efetuar uma cópia do portal do Azure
- Executar a chamada de API REST do Azure, o GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um utilizador do Centro de segurança atribuído a função de administrador de conta pode exportar os [just-in Políticas de tempo](security-center-just-in-time.md) que contém o IP de endereços por:

- Efetuar uma cópia do portal do Azure
- Executar a chamada de API REST do Azure, o GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um administrador de conta pode exportar os detalhes do alerta por:

- Efetuar uma cópia do portal do Azure
- Executar a chamada de API REST do Azure, o GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Ver [obter alertas de segurança (obter coleção)](https://msdn.microsoft.com/library/mt704050.aspx) para obter mais informações.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringir a utilização de dados pessoais para a criação de perfis ou de marketing sem consentimento
Um utilizador do Centro de segurança pode optar por optar ativamente por participar, eliminando seus [dados de contacto de segurança](security-center-provide-security-contact-details.md).

[Apenas nos dados de tempo](security-center-just-in-time.md) é considerado não identificáveis dados e é mantido durante um período de 30 dias.

[Dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e é mantido durante um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Registos de contacto de segurança, de auditoria just-in-time e alertá-atualizações são mantidas na [registos de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a gestão de dados do utilizador, consulte [gerir os dados de utilizador encontrados numa investigação do Centro de segurança do Azure](security-center-investigation-user-data.md).
