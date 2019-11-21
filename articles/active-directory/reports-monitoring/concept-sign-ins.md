---
title: Relatórios de atividade de início de sessão no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividade de início de sessão no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e7b0c379783af2f9131d487f45c0f4e2009e258
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232136"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividade de início de sessão no portal do Azure Active Directory

The reporting architecture in Azure Active Directory (Azure AD) consists of the following components:

- **Atividade** 
    - **Sign-ins** – Information about the usage of managed applications and user sign-in activities.
    - **Audit logs** - [Audit logs](concept-audit-logs.md) provide system activity information about users and group management, managed applications, and directory activities.
- **Segurança** 
    - **Risky sign-ins** - A [risky sign-in](concept-risky-sign-ins.md) is an indicator for a sign-in attempt by someone who isn't the legitimate owner of a user account.
    - **Users flagged for risk** - A [risky user](concept-user-at-risk.md) is an indicator for a user account that might have been compromised.

This article gives you an overview of the sign-ins report.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Users in the Security Administrator, Security Reader, Global Reader, and Report Reader roles
* Global Administrators
* Qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Que licença do Azure AD precisa para aceder à atividade de entrada?

* Your tenant must have an Azure AD Premium license associated with it to see the all up sign-in activity report. See [Getting started with Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) to upgrade your Azure Active Directory edition. It will take a couple of days for the data to show up in the reports after you upgrade to a premium license with no data activities before the upgrade.

## <a name="sign-ins-report"></a>Sign-ins report

The user sign-ins report provides answers to the following questions:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

Start with [Azure portal](https://portal.azure.com). To access the sign-ins report select **Sign-ins**, continue to the **Monitoring.** It may take up to two hours for some sign-in records to show up in the portal.

![Sign-in activity](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Atividade de início de sessão")

> [!IMPORTANT]
> The sign-ins report only displays the **interactive** sign-ins, that is, sign-ins where a user manually signs in using their username and password. Non-interactive sign-ins, such as service-to-service authentication, are not displayed in the sign-ins report. 

Um registo de inícios de sessão tem uma vista de listas predefinidas que mostra:

- A data de início de sessão
- O utilizador relacionado
- The application the user has signed in to
- O estado de início de sessão
- O estado da deteção de risco
- O estado do requisito de autenticação multifator (MFA)

![Sign-in activity](./media/concept-sign-ins/sign-in-activity.png "Atividade de início de sessão")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Sign-in activity](./media/concept-sign-ins/19.png "Atividade de início de sessão")

Displays additional fields or remove fields that are already displayed.

![Sign-in activity](./media/concept-sign-ins/02.png "Atividade de início de sessão")

Select an item in the list view to get more detailed information.

![Sign-in activity](./media/concept-sign-ins/basic-sign-in.png "Atividade de início de sessão")

> [!NOTE]
> Customers can now troubleshoot Conditional Access policies through all sign-in reports. By clicking on the **Conditional Access** tab for a sign-in record, customers can review the Conditional Access status and dive into the details of the policies that applied to the sign-in and the result for each policy.
> For more information, see the [Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de início de sessão

First, narrowing down the reported data to a level that works for you. Second, filter sign-ins data using date field as default filter. Azure AD provides you with a broad range of additional filters you can set.

![Sign-in activity](./media/concept-sign-ins/04.png "Atividade de início de sessão")

O filtro **Utilizador** permite-lhe especificar o nome ou o nome principal de utilizador (UPN) do utilizador que mais lhe interessa.

O filtro **Aplicação** permite-lhe especificar o nome da aplicação que mais lhe interessa.

O filtro **Estado do início de sessão** permite-lhe selecionar:

- Tudo
- Êxito
- Falha

The **Conditional Access** filter enables you to select the CA policy status for the sign-in:

- Tudo
- Not Applied
- Êxito
- Falha

O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- One month
- 7 dias
- 24 horas
- Intervalo de tempo personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Se adicionar mais campos à vista de inícios de sessão, estes campos são adicionados automaticamente à lista de filtros. Por exemplo, ao adicionar o campo **Aplicação Cliente** à sua lista, também obtém outra opção de filtro que lhe permite definir os seguintes filtros:  
![Sign-in activity](./media/concept-sign-ins/12.png "Atividade de início de sessão")

- **Browser**  
    This filter shows all events where sign-in attempts were attempted using browser flows.
- **Exchange ActiveSync (supported)**  
    This filter shows all sign-in attempts where the Exchange ActiveSync (EAS) protocol has been attempted from supported platforms like iOS, Android, and Windows Phone.
- **Exchange ActiveSync (unsupported)**  
    This filter shows all sign-in attempts where the EAS protocol has been attempted from unsupported platforms like, Linux distros.
- **Mobile Apps and Desktop clients** The filter shows all sign-in attempts that were not using browser flows. For example, mobile apps from any platform using any protocol or from Desktop client apps like Office on Windows or MacOS.
  
- **Other clients**
    - **IMAP**  
        A legacy mail client using IMAP to retrieve email.
    - **MAPI**  
        Office 2013, where ADAL is enabled and it is using MAPI.
    - **Old Office clients**  
        Office 2013 in its default configuration where ADAL is not enabled and it is using MAPI, or Office 2016 where ADAL has been disabled.
    - **POP**  
        A legacy mail client using POP3 to retrieve email.
    - **SMTP**  
        A legacy mail client using SMTP to send email.

## <a name="download-sign-in-activities"></a>Transferir atividades de início de sessão

Click the **Download** option to create a CSV or JSON file of the most recent 250,000 records. Start with [download the sign-ins data](quickstart-download-sign-in-report.md) if you want to work with it outside the Azure portal.  

![Transferência](./media/concept-sign-ins/71.png "Transferir")

> [!IMPORTANT]
> The number of records you can download is constrained by the [Azure Active Directory report retention policies](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Sign-ins data shortcuts

Azure AD and the Azure portal both provide you with additional entry points to sign-ins data:

- The Identity security protection overview
- Utilizadores
- Grupos
- Aplicações empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Users sign-ins data in Identity security protection

The user sign-in graph in the **Identity security protection** overview page shows weekly aggregations of sign-ins. The default for the time period is 30 days.

![Sign-in activity](./media/concept-sign-ins/06.png "Atividade de início de sessão")

Quando clica num dia no gráfico de início de sessão, obtém uma descrição geral das atividades de início de sessão para este dia.

Cada linha na lista de atividades de início de sessão mostra:

* Quem iniciou sessão?
* Que aplicação foi o destino do início de sessão?
* Qual o estado do início de sessão?
* Qual o estado MFA do início de sessão?

Ao clicar num item, obtém mais detalhes sobre a operação de início de sessão:

- ID de Utilizador
- Utilizador
- Nome de utilizador
- ID da aplicação
- Candidatura
- Cliente
- Localização
- Endereço IP
- Date
- MFA Necessário
- Estado de início de sessão

> [!NOTE]
> IP addresses are issued in such a way that there is no definitive connection between an IP address and where the computer with that address is physically located. Mapping IP addresses is complicated by the fact that mobile providers and VPNs issue IP addresses from central pools that are often very far from where the client device is actually used. Currently in Azure AD reports, converting IP address to a physical location is a best effort based on traces, registry data, reverse look ups and other information.

Na página **Utilizadores**, pode obter uma descrição geral completa de todos os inícios de sessão dos utilizadores ao clicar em **Inícios de sessão** na secção **Atividade**.

![Sign-in activity](./media/concept-sign-ins/08.png "Atividade de início de sessão")

## <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* What are the top three applications in your organization?
* How is my newest application doing?

The entry point to this data is the top three applications in your organization. The data is contained within the last 30 days report in the **Overview** section under **Enterprise applications**.

![Sign-in activity](./media/concept-sign-ins/10.png "Atividade de início de sessão")

The app-usage graphs weekly aggregations of sign-ins for your top three applications in a given time period. A predefinição do período de tempo é 30 dias.

![Sign-in activity](./media/concept-sign-ins/graph-chart.png "Atividade de início de sessão")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/concept-sign-ins/single-app-usage-graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

## <a name="office-365-activity-logs"></a>Office 365 activity logs

You can view Office 365 activity logs from the [Microsoft 365 admin center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Consider the point  that, Office 365 activity and Azure AD activity logs share a significant number of the directory resources. Only the Microsoft 365 admin center provides a full view of the Office 365 activity logs. 

You can also access the Office 365 activity logs programmatically by using the [Office 365 Management APIs](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos seguintes

* [Sign-in activity report error codes](reference-sign-ins-error-codes.md)
* [Azure AD data retention policies](reference-reports-data-retention.md)
* [Azure AD report latencies](reference-reports-latencies.md)

