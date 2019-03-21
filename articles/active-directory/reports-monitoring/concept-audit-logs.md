---
title: Relatórios de atividade de auditoria no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividade de auditoria no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf069e053068eaee3f36153c3888e6c711724267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182171"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatório de atividade de auditoria no portal do Azure Active Directory 

Com os relatórios do Azure Active Directory (Azure AD), pode obter as informações que necessárias para determinar o estado de funcionamento do ambiente.

A arquitetura de relatórios é composta pelos seguintes componentes:

- **Atividade** 
    - **Inícios de sessão** – a [relatório de inícios de sessão](concept-sign-ins.md) fornece informações sobre a utilização de aplicações geridas e utilizador atividades de início de sessão.
    - **Registos de auditoria** - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem as alterações efetuadas a quaisquer recursos no Azure AD, como adição ou remoção de utilizadores, aplicações, grupos, funções e as políticas.
- **Segurança** 
    - **Inícios de sessão arriscados** - uma [o início de sessão arriscado](concept-risky-sign-ins.md) é um indicador de uma tentativa de início de sessão poderão ter sido executada por alguém que não é o proprietário legítimo de uma conta de utilizador. 
    - **Utilizadores sinalizados para risco** - uma [utilizador de risco](concept-user-at-risk.md) é um indicador de uma conta de utilizador que pode ter sido comprometida.

Este artigo fornece uma descrição geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Os utilizadores a **administrador de segurança**, **leitor de segurança**, **leitor de relatório** ou **Administrador Global** funções
* Além disso, todos os utilizadores (não-administradores) podem ver as suas próprias atividades de auditoria

## <a name="audit-logs"></a>Registos de auditoria

A auditoria do Azure AD registos fornecem registos das atividades de sistema de conformidade. Para aceder ao relatório de auditoria, selecione **registos de auditoria** no **atividade** secção **Azure Active Directory**. Tenha em atenção que os registos de auditoria podem ter uma latência de até uma hora, pelo que poderá demorar tanto tempo para dados de atividade de auditoria sejam apresentados no portal depois de concluir a tarefa.

![Registos de auditoria](./media/concept-audit-logs/61.png "Registos de auditoria")

Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o serviço que a ocorrência de registados
- a categoria e o nome da atividade (*o que*) 
- o estado da atividade (êxito ou falha)
- o destino
- O iniciador / ator (quem) de uma atividade

![Registos de auditoria](./media/concept-audit-logs/listview.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/concept-audit-logs/columns.png "Registos de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Registos de auditoria](./media/concept-audit-logs/columnselect.png "Registos de auditoria")

Selecione um item na vista de lista para obter informações mais detalhadas.

![Registos de auditoria](./media/concept-audit-logs/details.png "Registos de auditoria")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Pode filtrar os dados de auditoria nos seguintes campos:

- Serviço
- Categoria
- Atividade
- Estado
- Destino
- Iniciado por (Actor)
- Intervalo de datas

![Registos de auditoria](./media/concept-audit-logs/filter.png "Registos de auditoria")

O **serviço** filtro permite-lhe selecionar a partir de uma lista suspensa dos seguintes serviços:

- Todos
- Revisões de Acesso
- Aprovisionamento de Contas 
- Aplicação SSO
- Métodos de Autenticação
- B2C
- Acesso Condicional
- Diretório do Núcleo
- Gestão de Direitos
- Identity Protection
- Utilizadores Convidados
- PIM
- Gestão de Grupos Personalizada
- Gestão de Passord self-service
- Termos de Utilização

O **categoria** filtro permite-lhe selecionar um dos seguintes filtros:

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

O **atividade** filtro baseia-se a seleção de tipo de recurso categoria e a atividade que fizer. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

Pode obter a lista de todas as Atividades de Auditoria com a Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, em que $tenantdomain é o nome do seu domínio, ou ver o artigo [Audit report events](reference-audit-activities.md) (Eventos de relatórios de auditoria).

O **estado** filtro permite-lhe filtrar com base no estado de uma operação de auditoria. O estado pode ser um dos seguintes:

- Todos
- Êxito
- Falha

O **destino** filtro permite-lhe procurar um destino específico por nome ou nome principal de utilizador (UPN). O nome de destino e o UPN diferenciam maiúsculas de minúsculas. 

O **iniciado por** filtro permite-lhe definir o nome de um ator ou um nome principal universal (UPN). O nome e o UPN diferenciam maiúsculas de minúsculas.

O **intervalo de datas** filtro permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Também pode optar por transferir os dados filtrados, registos de até 250.000, selecionando o **transferir** botão. Pode optar por transferir os registos no formato JSON ou CSV. O número de registos, pode transferir é restrita pela [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).

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

Se quiser apenas rever dados de auditoria que estejam relacionados com utilizadores, pode encontrar um vista filtrada em **registos de auditoria** no **atividade** seção o **utilizadores** separador. Este ponto de entrada tem **UserManagement** como categoria pré-selecionado.

![Registos de auditoria](./media/concept-audit-logs/users.png "Registos de auditoria")

Se quiser apenas rever dados de auditoria que estejam relacionados com grupos, pode encontrar um vista filtrada em **registos de auditoria** no **atividade** seção o **grupos** separador. Este ponto de entrada tem **GroupManagement** como categoria pré-selecionado.

![Registos de auditoria](./media/concept-audit-logs/groups.png "Registos de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Um principal de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se pretender rever dados de auditoria relacionados com aplicações, pode encontrar um vista filtrada em **registos de auditoria** no **atividade** seção o **aplicações empresariais** painel. Este ponto de entrada tem **aplicações empresariais** pré-selecionado como o **tipo de aplicação**.

![Registos de auditoria](./media/concept-audit-logs/enterpriseapplications.png "Registos de auditoria")

## <a name="office-365-activity-logs"></a>Registos de atividades do Office 365

Pode ver registos de atividade do Office 365 a partir da [Centro de administração do Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Apesar de atividades do Office 365 e partilha de registos de atividade do Azure AD muitos dos recursos de diretório, apenas o Centro de administração do Microsoft 365 fornece uma visão completa dos registos de atividades do Office 365. 

Também pode aceder a registos de atividades do Office 365 programaticamente usando a [APIs de gestão do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos Seguintes

- [Referência de atividades de auditoria do Azure AD](reference-audit-activities.md)
- [Referência de retenção de relatórios do Azure AD](reference-reports-data-retention.md)
- [Referenciam de latências de registo do Azure AD](reference-reports-latencies.md)
