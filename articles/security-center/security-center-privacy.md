---
title: Gerir os dados dos utilizadores no Azure Security Center | Microsoft Docs
description: Saiba como gerir os dados do utilizador no Azure Security Center. A gestão dos dados dos utilizadores inclui a capacidade de aceder, eliminar ou exportar dados.
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
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595646"
---
# <a name="manage-user-data-in-azure-security-center"></a>Manage user data in Azure Security Center (Gerir dados de utilizadores no Centro de Segurança do Azure)
Este artigo fornece informações sobre como pode gerir os dados do utilizador no Azure Security Center. A gestão dos dados dos utilizadores inclui a capacidade de aceder, eliminar ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um utilizador do Security Center atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode aceder aos dados do cliente dentro da ferramenta. Para saber mais sobre a função de Administrador de Conta, consulte [funções incorporadas para o controlo de acesso baseado em funções Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções de Leitor, Proprietário e Contribuinte. Consulte [os administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Procurar e identificar dados pessoais
Um utilizador do Security Center pode visualizar os seus dados pessoais através do portal Azure. O Security Center apenas armazena dados de contacto de segurança, como endereços de e-mail e números de telefone. Para mais informações, consulte [fornecer detalhes de contacto de segurança no Centro de Segurança Azure.](security-center-provide-security-contact-details.md)

No portal Azure, um utilizador pode visualizar configurações IP permitidas utilizando a funcionalidade de acesso VM do Centro de Segurança just-in-time. Para obter mais informações, consulte [Gerir o acesso à máquina virtual utilizando o tempo.](security-center-just-in-time.md)

No portal Azure, um utilizador pode ver alertas de segurança fornecidos pelo Security Center, incluindo endereços IP e detalhes do intruso. Para mais informações, consulte [Managing e responder aos alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Classificação de dados pessoais
Não é necessário classificar os dados pessoais encontrados na funcionalidade de contacto de segurança do Security Center. Os dados guardados são um endereço de e-mail (ou vários endereços de e-mail) e um número de telefone. [Os dados de contacto](security-center-provide-security-contact-details.md) são validados pelo Security Center.

Não é necessário classificar os endereços IP e os números de porta guardados pela funcionalidade [just-in-time](security-center-just-in-time.md) do Security Center.

Apenas um utilizador designado para o papel de Administrador pode classificar os dados pessoais [visualizando alertas](security-center-managing-and-responding-alerts.md) no Centro de Segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Assegurar e controlar o acesso aos dados pessoais
Um utilizador do Security Center atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode aceder aos [dados de contacto de segurança.](security-center-provide-security-contact-details.md)

Um utilizador do Security Center atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode aceder às suas políticas [just-in-time.](security-center-just-in-time.md)

Um utilizador do Centro de Segurança atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode ver os seus [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Atualizar dados pessoais
Um utilizador do Security Center atribuiu o papel de Proprietário, Colaborador ou Administrador de Conta pode atualizar [dados](security-center-provide-security-contact-details.md) de contacto de segurança através do portal Azure.

Um utilizador do Centro de Segurança atribuiu o papel de Proprietário, Colaborador ou Administrador de Conta pode atualizar as suas [políticas just-in-time](security-center-just-in-time.md).

Um administrador de conta não pode editar incidentes de alerta. Um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados de segurança e é lido apenas.

## <a name="deleting-personal-data"></a>Eliminar dados pessoais
Um utilizador do Security Center atribuiu o papel de Proprietário, Colaborador ou Administrador de Conta pode eliminar [dados](security-center-provide-security-contact-details.md) de contacto de segurança através do portal Azure.

Um utilizador do Security Center atribuiu o papel de Proprietário, Colaborador ou Administrador de Conta pode eliminar as [políticas just-in-time](security-center-just-in-time.md) através do portal Azure.

Um utilizador do Centro de Segurança não pode apagar incidentes de alerta. Por razões de segurança, um incidente de [alerta](security-center-managing-and-responding-alerts.md) é considerado apenas dados de leitura.

## <a name="exporting-personal-data"></a>Exportar dados pessoais
Um utilizador do Security Center atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode exportar [dados](security-center-provide-security-contact-details.md) de contacto de segurança através de:

- Cópia do portal Azure
- Executando a chamada Azure REST API, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um utilizador do Centro de Segurança designado pelo papel de Administrador de Conta pode exportar as [políticas just-in-time](security-center-just-in-time.md) que contêm os endereços IP:

- Cópia do portal Azure
- Executando a chamada Azure REST API, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um Administrador de Conta pode exportar os detalhes do alerta através de:

- Cópia do portal Azure
- Executando a chamada Azure REST API, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Para mais informações, consulte [Get Security Alerts (GET Collection)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringir a utilização de dados pessoais para perfis ou marketing sem consentimento
Um utilizador do Security Center pode optar por não optar por eliminar os seus [dados de contacto de segurança.](security-center-provide-security-contact-details.md)

[Os dados just-in-time](security-center-just-in-time.md) são considerados dados não identificáveis e conservados por um período de 30 dias.

[Os dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e são conservados por um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e reporte
Os registos de auditoria do contacto de segurança, just-in-time e atualizações de alerta são mantidos em [Registos de Atividade Azure](../azure-monitor/essentials/platform-logs-overview.md).