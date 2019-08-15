---
title: Relatórios de atividade de auditoria no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividade de auditoria no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a31fab8dd1490b58e743c149e4a54cae11bed1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988349"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatório de atividade de auditoria no portal do Azure Active Directory 

Com os relatórios do Azure Active Directory (Azure AD), você pode obter as informações necessárias para determinar o funcionamento do seu ambiente.

A arquitetura de relatórios consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – o [relatório](concept-sign-ins.md) de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Registos de auditoria** - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário. 
    - **Usuários sinalizados para risco** – um [usuário arriscado](concept-user-at-risk.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Usuários nas funções **administrador de segurança**, **leitor de segurança**, leitor de **relatório** ou **administrador global**
* Além disso, todos os usuários (não administradores) podem ver suas próprias atividades de auditoria

## <a name="audit-logs"></a>Registos de auditoria

Os logs de auditoria do Azure AD fornecem registros de atividades do sistema para fins de conformidade. Para acessar o relatório de auditoria, selecione **logs de auditoria** na seção **atividade** de **Azure Active Directory**. Observe que os logs de auditoria podem ter uma latência de até uma hora, portanto, pode levar tanto tempo para que os dados da atividade de auditoria apareçam no portal depois que você concluir a tarefa.



Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o serviço que registrou a ocorrência
- a categoria e o nome da atividade (*o que*) 
- o status da atividade (êxito ou falha)
- o destino
- o iniciador/ator (quem) de uma atividade

![Registos de auditoria](./media/concept-audit-logs/listview.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/concept-audit-logs/columns.png "Registos de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Registos de auditoria](./media/concept-audit-logs/columnselect.png "Registos de auditoria")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Registos de auditoria](./media/concept-audit-logs/details.png "Registos de auditoria")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Você pode filtrar os dados de auditoria nos seguintes campos:

- Serviço
- Category
- Atividade
- Estado
- Destino
- Iniciado por (Ator)
- Intervalo de datas

![Registos de auditoria](./media/concept-audit-logs/filter.png "Registos de auditoria")

O filtro de **serviço** permite que você selecione em uma lista suspensa dos seguintes serviços:

- Todos
- Revisões de Acesso
- Aprovisionamento de Contas 
- SSO de aplicativo
- Métodos de Autenticação
- B2C
- Acesso Condicional
- Diretório do Núcleo
- Gestão de Direitos
- Identity Protection
- Utilizadores Convidados
- PIM
- Gestão de Grupos Personalizada
- Gestão de Palavra-passe Personalizada
- Termos de Utilização

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Outros
- Política
- ResourceManagement
- RoleManagement
- UserManagement

O filtro de **atividade** é baseado na categoria e na seleção do tipo de recurso de atividade que você faz. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

Você pode obter a lista de todas as atividades de auditoria usando o API do Graph:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

O filtro de **status** permite filtrar com base no status de uma operação de auditoria. O status pode ser um dos seguintes:

- Todos
- Êxito
- Falha

O filtro de **destino** permite que você pesquise um destino específico por nome ou nome principal do usuário (UPN). O nome de destino e o UPN diferenciam maiúsculas de minúsculas. 

O filtro **iniciado por** permite que você defina o nome de um ator ou um nome de entidade universal (UPN). O nome e o UPN diferenciam maiúsculas de minúsculas.

O filtro de **intervalo de datas** permite que você defina um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizar

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Você também pode optar por baixar os dados filtrados, até 250.000 registros, selecionando o botão **baixar** . Você pode baixar os logs no formato CSV ou JSON. O número de registros que você pode baixar é restrito pelo [Azure Active Directory políticas de retenção de relatório](reference-reports-data-retention.md).

![Registos de auditoria](./media/concept-audit-logs/download.png "Registos de auditoria")

## <a name="audit-logs-shortcuts"></a>Atalhos dos registos de auditoria

Para além do **Azure Active Directory**, o portal do Azure proporciona-lhe dois pontos de entrada adicionais para dados de auditoria:

- Utilizadores e grupos
- Aplicações Empresariais

### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações os utilizadores aplicaram?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se você quiser revisar apenas os dados de auditoria relacionados aos usuários, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **atividade** da guia **usuários** . Este ponto de entrada tem usermanagement como categoria preselecionada.

![Registos de auditoria](./media/concept-audit-logs/users.png "Registos de auditoria")

Se você quiser revisar apenas os dados de auditoria relacionados a grupos, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **atividade** da guia **grupos** . Este ponto de entrada tem **GroupManagement** como categoria preselecionada.

![Registos de auditoria](./media/concept-audit-logs/groups.png "Registos de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Uma entidade de serviço foi alterada para um aplicativo?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se você quiser examinar os dados de auditoria relacionados aos seus aplicativos, poderá encontrar uma exibição filtrada em **logs de auditoria** na seção **atividade** da folha **aplicativos empresariais** . Esse ponto de entrada tem **aplicativos empresariais** selecionados como o **tipo de aplicativo**.

![Registos de auditoria](./media/concept-audit-logs/enterpriseapplications.png "Registos de auditoria")

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

Você pode exibir os logs de atividade do Office 365 no [centro de administração do Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora a atividade do Office 365 e os logs de atividades do Azure AD compartilhem muitos recursos de diretório, somente o Microsoft 365 centro de administração fornece uma visão completa dos logs de atividade do Office 365. 

Você também pode acessar os logs de atividade do Office 365 programaticamente usando as [APIs de gerenciamento do office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos Seguintes

- [Referência de atividade de auditoria do Azure AD](reference-audit-activities.md)
- [Referência de retenção de relatórios do Azure AD](reference-reports-data-retention.md)
- [Referência de latências de log do Azure AD](reference-reports-latencies.md)
