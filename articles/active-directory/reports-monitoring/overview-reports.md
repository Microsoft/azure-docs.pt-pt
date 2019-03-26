---
title: O que são os relatórios do Azure Active Directory? | Microsoft Docs
description: Mostra uma descrição geral dos relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ccf8014938e813676e9338e9bfc4eaa18f63f9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436953"
---
# <a name="what-are-azure-active-directory-reports"></a>O que são os relatórios do Azure Active Directory?

Os relatórios de Active Directory (Azure AD) do Azure fornecem uma visão abrangente de atividade no seu ambiente. Os dados fornecidos permite-lhe:

- Determinar de que forma é que os utilizadores utilizam os seus serviços e aplicações
- Detetar potenciais riscos que afetem o estado de funcionamento do ambiente
- Resolver problemas que impedem os utilizadores de trabalhar  

A arquitetura dos relatórios baseia-se em dois pilares principais:

- [Relatórios de segurança](#security-reports)
- [Relatórios de atividade](#activity-reports)

![Relatórios](./media/overview-reports/01.png)


## <a name="security-reports"></a>Relatórios de segurança

Relatórios de segurança ajudam a proteger as identidades da sua organização. Existem dois tipos de relatórios de segurança:

- **Utilizadores sinalizados para risco** - com o [relatório de segurança de utilizadores sinalizados para risco](concept-user-at-risk.md), obtém uma descrição geral das contas de utilizador que possam ter sido comprometidas.

- **Inícios de sessão de risco** - com o [relatório de segurança de inícios de sessão de risco](concept-risky-sign-ins.md), obtém um indicador para tentativas de início de sessão que possam ter sido realizados por alguém que não seja o legítimo proprietário das contas de utilizador. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure AD fornecem utilizadores sinalizados para risco e relatórios de inícios de sessão de risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Na **edições do Azure Active Directory gratuito e Basic**, obtém uma lista de utilizadores sinalizados para risco e inícios de sessão de risco. 

- A edição **Azure Active Directory Premium 1** expande este modelo, permitindo-lhe também examinar alguns dos eventos de risco subjacentes que foram detetados em cada relatório. 

- A edição **do Azure Active Directory Premium 2** proporciona-lhe as informações mais detalhadas sobre os eventos de risco subjacentes e também lhe permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividade

Relatórios de atividade de ajudar a compreender o comportamento dos utilizadores na sua organização. Existem dois tipos de relatórios de atividade no Azure AD:

- **Registos de auditoria** - o [relatório de atividade de registos de auditoria](concept-audit-logs.md) dá-lhe acesso ao histórico de cada tarefa executada no seu inquilino.

- **Inícios de sessão** – com o [relatório de atividade de inícios de sessão](concept-sign-ins.md), pode saber quem realizou as tarefas reportadas no relatório de registos de auditoria.


### <a name="audit-logs-report"></a>Relatório de registos de auditoria 

O [relatório de registos de auditoria](concept-audit-logs.md) disponibiliza-lhe registos das atividades do sistema para efeitos de conformidade. Estes dados permitem-lhe abordar cenários comuns, tais como:

- Um utilizador no meu inquilino obteve acesso a um grupo de administração. Quem lhe deu acesso? 

- Quero ver uma lista dos utilizadores que iniciam sessão numa determinada aplicação, porque incluía-a recentemente e quero saber se está a funcionar bem.

- Quero saber quantas reposições de palavras-passe ocorrem no meu inquilino.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Que licença do Azure AD precisa aceder ao relatório de registos de auditoria?  

O relatório de registos de auditoria está disponível para as funcionalidades para as quais tem licenças. Se tiver uma licença para uma funcionalidade específica, também tem acesso às informações de registo de auditoria relativas à mesma. Para obter mais detalhes, consulte [funcionalidades do Azure Active Directory e capacidades](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   

### <a name="sign-ins-report"></a>Relatório de inícios de sessão

O [relatório de inícios de sessão](concept-sign-ins.md) permite-lhe encontrar respostas a perguntas como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Que licença do Azure AD precisa aceder ao relatório de atividade de inícios de sessão?  

Para aceder aos relatórios de atividade de inícios de sessão, o seu inquilino tem de ter uma licença do Azure AD Premium associada ao mesmo.

## <a name="programmatic-access"></a>Acesso programático

Para além da interface do usuário, do Azure AD também fornece [acesso programático](concept-reporting-api.md) para os dados de relatórios, por meio de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. 

## <a name="next-steps"></a>Passos Seguintes

- [Relatório de inícios de sessão de risco](concept-risky-sign-ins.md)
- [Relatório de registos de auditoria](concept-audit-logs.md)
- [Relatório de registos de inícios de sessão](concept-sign-ins.md)
