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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6121ca6c1636c8839110712310a1b94fe7fada49
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619250"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividade de início de sessão no portal do Azure Active Directory

A arquitetura de relatórios no Azure Active Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - Os logs de auditoria dos logs de auditoria fornecem informações de atividade do sistema sobre gerenciamento de usuários e grupos, aplicativos gerenciados e atividades de diretório.[](concept-audit-logs.md)  - 
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** – um [usuário arriscado](concept-user-at-risk.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral do relatório de entradas.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Usuários nas funções administrador de segurança, leitor de segurança e leitor de relatório
* Administradores globais
* Além disso, qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Que licença do Azure AD precisa para aceder à atividade de entrada?
* Seu locatário deve ter uma licença de Azure AD Premium associada a ele para ver o relatório de atividade de entrada. Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory. Observe que, se você não tiver dados de atividades antes da atualização, levará alguns dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium.

## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas do usuário fornece respostas para as seguintes perguntas:

* O que é o padrão de início de sessão de um utilizador?
* Quantos utilizadores iniciaram sessão ao longo de uma semana?
* Qual é o estado destes inícios de sessão?

Você pode acessar o relatório de entradas selecionando **entradas** na seção **atividade** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Observe que pode levar até duas horas para que alguns registros de entrada apareçam no Portal.

![Atividade de início de sessão](./media/concept-sign-ins/61.png "Atividade de início de sessão")

> [!IMPORTANT]
> O relatório de entradas exibe apenas as entradas interativas, ou seja, entradas em que um usuário faz logon manualmente usando seu nome de usuários e senha. As entradas não interativas, como a autenticação serviço a serviço, não são exibidas no relatório de entradas. 

Um registo de inícios de sessão tem uma vista de listas predefinidas que mostra:

- A data de início de sessão
- O utilizador relacionado
- O aplicativo ao qual o usuário entrou
- O estado de início de sessão
- O estado da deteção de risco
- O estado do requisito de autenticação multifator (MFA)

![Atividade de início de sessão](./media/concept-sign-ins/01.png "Atividade de início de sessão")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Atividade de início de sessão](./media/concept-sign-ins/19.png "Atividade de início de sessão")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Atividade de início de sessão](./media/concept-sign-ins/02.png "Atividade de início de sessão")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Atividade de início de sessão](./media/concept-sign-ins/03.png "Atividade de início de sessão")

> [!NOTE]
> Agora, os clientes podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de entrada. Ao clicar na guia **acesso condicional** para um registro de entrada, os clientes podem examinar o status de acesso condicional e aprofundar-se nos detalhes das políticas que foram aplicadas à entrada e ao resultado de cada política.
> Para obter mais informações, consulte as perguntas frequentes [sobre informações de autoridade de certificação em todas as entradas](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de início de sessão

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de entradas usando o campo de data como filtro padrão. Além disso, o Azure AD oferece uma ampla variedade de filtros adicionais que podem ser definidos.

![Atividade de início de sessão](./media/concept-sign-ins/04.png "Atividade de início de sessão")

O filtro **Utilizador** permite-lhe especificar o nome ou o nome principal de utilizador (UPN) do utilizador que mais lhe interessa.

O filtro **Aplicação** permite-lhe especificar o nome da aplicação que mais lhe interessa.

O filtro **Estado do início de sessão** permite-lhe selecionar:

- Todos
- Êxito
- Falha

O filtro de **acesso condicional** permite que você selecione o status da política de CA para a entrada:

- Todos
- Não Aplicado
- Êxito
- Falha

O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Intervalo de Tempo Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Se adicionar mais campos à vista de inícios de sessão, estes campos são adicionados automaticamente à lista de filtros. Por exemplo, ao adicionar o campo **Aplicação Cliente** à sua lista, também obtém outra opção de filtro que lhe permite definir os seguintes filtros:  
![Atividade de início de sessão](./media/concept-sign-ins/12.png "Atividade de início de sessão")

- **Browser**  
    Esse filtro mostra todos os eventos em que as tentativas de entrada foram executadas usando fluxos de navegador.
- **Exchange ActiveSync (com suporte)**  
    Esse filtro mostra todas as tentativas de entrada em que o protocolo EAS (Exchange ActiveSync) foi tentado a partir de plataformas com suporte, como iOS, Android e Windows Phone.
- **Exchange ActiveSync (sem suporte)**  
    Esse filtro mostra todas as tentativas de entrada nas quais o protocolo EAS foi tentado a partir de plataformas sem suporte, como o Linux distribuições.
- **Aplicativos móveis e clientes de área de trabalho** Esse filtro mostra todas as tentativas de entrada que não estavam usando fluxos de navegador. Isso pode ser aplicativos móveis de qualquer plataforma usando qualquer protocolo ou de aplicativos cliente de desktop, como Office no Windows ou MacOS.
  
- **Outros clientes**
    - **IMAP**  
        Um cliente de email herdado usando IMAP para recuperar email.
    - **MAPI**  
        Office 2013, onde a ADAL está habilitada e está usando MAPI.
    - **Clientes mais antigos do Office**  
        Office 2013 em sua configuração padrão em que a ADAL não está habilitada e está usando MAPI ou o Office 2016 em que a ADAL foi desabilitada.
    - **POP**  
        Um cliente de email herdado usando POP3 para recuperar email.
    - **SMTP**  
        Um cliente de email herdado usando SMTP para enviar email.

## <a name="download-sign-in-activities"></a>Transferir atividades de início de sessão

Você pode [baixar os dados de entradas](quickstart-download-sign-in-report.md) se quiser trabalhar com eles fora do portal do Azure. Clicar em **baixar** oferece a opção de criar um arquivo CSV ou JSON dos registros 250.000 mais recentes.  

![Transferir](./media/concept-sign-ins/71.png "Transferir")

> [!IMPORTANT]
> O número de registros que você pode baixar é restrito pelo [Azure Active Directory políticas de retenção de relatório](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos de dados de entradas

Além do AD do Azure, o portal do Azure fornece pontos de entrada adicionais para os dados de entrada:

- Visão geral da proteção de segurança de identidade
- Utilizadores
- Grupos
- Aplicações empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Os usuários entram em dados na proteção de segurança de identidade

O grafo de entrada do usuário na página Visão geral da **proteção de segurança de identidade** mostra as agregações semanais de entradas para todos os usuários em um determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de início de sessão](./media/concept-sign-ins/06.png "Atividade de início de sessão")

Quando clica num dia no gráfico de início de sessão, obtém uma descrição geral das atividades de início de sessão para este dia.

Cada linha na lista de atividades de início de sessão mostra:

* Quem iniciou sessão?
* Que aplicação foi o destino do início de sessão?
* Qual o estado do início de sessão?
* Qual o estado MFA do início de sessão?

Ao clicar num item, obtém mais detalhes sobre a operação de início de sessão:

- ID de utilizador
- Utilizador
- Nome de utilizador
- ID da aplicação
- Aplicação
- Cliente
- Location
- Endereço IP
- Date
- MFA Necessário
- Estado de início de sessão

> [!NOTE]
> Os endereços IP são emitidos de forma que não haja nenhuma conexão definitiva entre um endereço IP e onde o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é complicado pelo fato de que os provedores móveis e as VPNs emitem endereços IP de pools centrais que geralmente são muito longe de onde o dispositivo cliente é realmente usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.

Na página **Utilizadores**, pode obter uma descrição geral completa de todos os inícios de sessão dos utilizadores ao clicar em **Inícios de sessão** na secção **Atividade**.

![Atividade de início de sessão](./media/concept-sign-ins/08.png "Atividade de início de sessão")

## <a name="usage-of-managed-applications"></a>Utilização de aplicações geridas

Com uma vista centrada em aplicações dos seus dados de início de sessão, poderá responder a perguntas como:

* Quem está a utilizar as minhas aplicações?
* Quais são as três aplicações mais utilizadas na sua organização?
* Lancei recentemente uma aplicação. Como está a correr?

O ponto de entrada para estes dados são as três aplicações mais utilizadas na sua organização no relatório dos últimos 30 dias na secção **Descrição geral** em **Aplicações empresariais**.

![Atividade de início de sessão](./media/concept-sign-ins/10.png "Atividade de início de sessão")

As agregações semanais do grafo de uso do aplicativo de entradas para os três principais aplicativos em um determinado período de tempo. A predefinição do período de tempo é 30 dias.

![Atividade de início de sessão](./media/concept-sign-ins/47.png "Atividade de início de sessão")

Se quiser, pode colocar o foco numa aplicação específica.

![Relatórios](./media/concept-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando clica num dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de início de sessão.

A opção **Inícios de sessão** dá uma visão geral completa de todos os eventos de início de sessão nas suas aplicações.

![Atividade de início de sessão](./media/concept-sign-ins/11.png "Atividade de início de sessão")

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

Você pode exibir os logs de atividade do Office 365 no [centro de administração do Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora a atividade do Office 365 e os logs de atividades do Azure AD compartilhem muitos recursos de diretório, somente o Microsoft 365 centro de administração fornece uma visão completa dos logs de atividade do Office 365. 

Você também pode acessar os logs de atividade do Office 365 programaticamente usando as [APIs de gerenciamento do office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos Seguintes

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências de relatório do Azure AD](reference-reports-latencies.md)

