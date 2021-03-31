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
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566827"
---
# <a name="what-are-azure-active-directory-reports"></a>O que são os relatórios do Azure Active Directory?

Os relatórios do Azure Ative Directory (Azure AD) proporcionam uma visão abrangente da atividade no seu ambiente. Os dados fornecidos permite-lhe:

- Determinar de que forma é que os utilizadores utilizam os seus serviços e aplicações
- Detetar potenciais riscos que afetem o estado de funcionamento do ambiente
- Resolver problemas que impedem os utilizadores de trabalhar  

A arquitetura dos relatórios baseia-se em dois pilares principais:

- [Relatórios de segurança](#security-reports)
- [Relatórios de atividade](#activity-reports)

![Relatórios](./media/overview-reports/01.png)


## <a name="security-reports"></a>Relatórios de segurança

Relatórios de segurança ajudam-no a proteger as identidades da sua organização. Existem dois tipos de relatórios de segurança:

- **Utilizadores sinalizados para risco** - com o [relatório de segurança de utilizadores sinalizados para risco](../identity-protection/overview-identity-protection.md), obtém uma descrição geral das contas de utilizador que possam ter sido comprometidas.

- **Inícios de sessão de risco** - com o [relatório de segurança de inícios de sessão de risco](../identity-protection/overview-identity-protection.md), obtém um indicador para tentativas de início de sessão que possam ter sido realizados por alguém que não seja o legítimo proprietário das contas de utilizador. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De que licença do Azure AD precisa para aceder a um relatório de segurança?  

Todas as edições do Azure AD fornecem-lhe utilizadores sinalizados para relatórios de ins de risco e de risco. No entanto, o nível de granularidade dos relatórios varia entre as edições: 

- Nas **edições Azure Ative Directory Free and Basic,** obtém uma lista de utilizadores sinalizados para insusitadas de risco e de risco. 

- A edição **Azure Ative Directory Premium 1** alarga este modelo, permitindo-lhe também examinar algumas das deteções de risco subjacentes que foram detetadas para cada relatório. 

- A edição **Azure Ative Directory Premium 2** fornece-lhe as informações mais detalhadas sobre as deteções de risco subjacentes e também permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividade

Os relatórios de atividade ajudam-no a compreender o comportamento dos utilizadores na sua organização. Existem dois tipos de relatórios de atividade em Azure AD:

- **Registos de auditoria** - o [relatório de atividade de registos de auditoria](concept-audit-logs.md) dá-lhe acesso ao histórico de cada tarefa executada no seu inquilino.

- **Inícios de sessão** – com o [relatório de atividade de inícios de sessão](concept-sign-ins.md), pode saber quem realizou as tarefas reportadas no relatório de registos de auditoria.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Relatório de registos de auditoria 

O [relatório de registos de auditoria](concept-audit-logs.md) disponibiliza-lhe registos das atividades do sistema para efeitos de conformidade. Estes dados permitem-lhe abordar cenários comuns, tais como:

- Um utilizador no meu inquilino obteve acesso a um grupo de administração. Quem lhe deu acesso? 

- Quero ver uma lista dos utilizadores que iniciam sessão numa determinada aplicação, porque incluía-a recentemente e quero saber se está a funcionar bem.

- Quero saber quantas reposições de palavras-passe ocorrem no meu inquilino.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>De que licença do Azure AD precisa para aceder aos relatórios de registos de auditoria?  

O relatório de registos de auditoria está disponível para as funcionalidades para as quais tem licenças. Se tiver uma licença para uma funcionalidade específica, também tem acesso às informações de registo de auditoria relativas à mesma. Uma comparação de funcionalidades desaatidada como por [diferentes tipos de licenças](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) pode ser vista na página de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/). Para mais detalhes, consulte [as funcionalidades e capacidades do Azure Ative Directory](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Relatório de inscrições

O [relatório de inscrições permite-lhe](concept-sign-ins.md) encontrar respostas a questões como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>De que licença do Azure AD precisa para aceder aos relatórios de atividade de inícios de sessão?  

Para aceder aos relatórios de atividade de inícios de sessão, o seu inquilino tem de ter uma licença do Azure AD Premium associada ao mesmo.

## <a name="programmatic-access"></a>Acesso programático

Além da interface de utilizador, o Azure AD também lhe fornece [acesso programático](concept-reporting-api.md) aos dados dos relatórios, através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. 

## <a name="next-steps"></a>Passos seguintes

- [Relatório de inícios de sessão de risco](../identity-protection/overview-identity-protection.md)
- [Relatório de registos de auditoria](concept-audit-logs.md)
- [Relatório de registos de inícios de sessão](concept-sign-ins.md)