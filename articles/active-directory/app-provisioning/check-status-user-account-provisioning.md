---
title: Reportar fornecimento automático de conta de utilizador às aplicações SaaS
description: Saiba como verificar o estado dos postos de trabalho de fornecimento automático de conta de utilizador e como resolver o fornecimento de utilizadores individuais.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282193"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatório sobre o provisionamento automático da conta de utilizador

O Azure Ative Directory (Azure AD) inclui um serviço de [prestação](user-provisioning.md) de contas de utilizador que ajuda a automatizar o fornecimento de contas de utilizadores em aplicações SaaS e outros sistemas, para efeitos de gestão de ciclos de vida de identidade de ponta a ponta. A Azure AD suporta conectores de fornecimento de utilizadores pré-integrados para todas as aplicações e sistemas com tutoriais de provisionamento de utilizadores [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

Este artigo descreve como verificar o estado do fornecimento de postos de trabalho após a sua criação e como resolver o fornecimento de utilizadores e grupos individuais.

## <a name="overview"></a>Descrição geral

Os conectores de provisionamento são configurados e configurados através do [portal Azure,](https://portal.azure.com)seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para a aplicação apoiada. Uma vez configurados e em execução, os postos de trabalho de provisionamento podem ser reportados utilizando um de dois métodos:

* **Portal Azure** - Este artigo descreve principalmente a recuperação de informações de relatório do [portal Azure,](https://portal.azure.com)que fornece tanto um relatório resumo de fornecimento como registos de auditoria de fornecimento detalhado para uma determinada aplicação.
* **Auditoria API** - Azure Ative Directory também fornece uma API de auditoria que permite a recuperação programática dos registos de auditoria de provisionamento detalhado. Consulte a referência da API de [auditoria do Azure Ative Diretório](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para documentação específica para a utilização desta API. Embora este artigo não cubra especificamente a forma de utilizar a API, detalha os tipos de eventos de provisionamento que são registados no registo de auditoria.

### <a name="definitions"></a>Definições

Este artigo utiliza os seguintes termos, definidos abaixo:

* **Sistema Fonte** - O repositório de utilizadores de que o serviço de fornecimento de AD Azure sincroniza. O Azure Ative Directory é o sistema de origem para a maioria dos conectores de provisionamento pré-integrados, no entanto existem algumas exceções (exemplo: Sincronização de entrada no dia de trabalho).
* **Target System** - O repositório de utilizadores a que o serviço de fornecimento de AD Azure sincroniza. Esta é tipicamente uma aplicação SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas em alguns casos pode ser um sistema no local, como Ative Directory (exemplo: Sincronização de entrada no workday para Diretório Ativo).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal Azure

Para obter informações sobre o relatório de fornecimento para uma determinada aplicação, comece por lançar o [portal Azure](https://portal.azure.com) e **o Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning (pré-visualização)** na secção **Atividade.** Também pode navegar na Aplicação da Empresa para a qual o fornecimento está configurado. Por exemplo, se estiver a fornecer utilizadores ao LinkedIn Elevate, a rota de navegação para os detalhes da aplicação é:

**Azure Ative Directory > Aplicações empresariais > todas as aplicações > LinkedIn Elevate**

A partir daqui, pode aceder tanto à barra de provisão de progresso como aos registos de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Fornecimento de barra de progresso

A barra de [provisão](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) de progresso é visível no separador **provisionamento** para determinada aplicação. Está localizado na secção **Estado atual** por baixo **das Definições,** e mostra o estado do ciclo inicial ou incremental atual. Esta secção também mostra:

* O número total de utilizadores e/grupos que foram sincronizados e que estão atualmente em aberto para o fornecimento entre o sistema de origem e o sistema-alvo.
* A última vez que a sincronização foi executada. As sincronizações ocorrem normalmente a cada 20-40 minutos, depois de um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) ter terminado.
* Se um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi ou não concluído.
* Se o processo de provisionamento foi ou não colocado em quarentena, e qual é a razão para o estado de quarentena (por exemplo, a falta de comunicação com o sistema-alvo devido a credenciais de administração inválidas).

O **Estado atual** deve ser o primeiro local que os administradores procuram verificar a saúde operacional do trabalho de provisionamento.

 ![Relatório sumário](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Registos de fornecimento (pré-visualização)

Todas as atividades realizadas pelo serviço de provisionamento são registadas nos registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)da AD Azure. Pode aceder aos registos de fornecimento no portal Azure selecionando registos de provisionamento de &gt; **aplicações** &gt; de **diretório ativo azure** **(pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de provisionamento com base no nome do utilizador ou no identificador no sistema de origem ou no sistema de destino. Para mais detalhes, consulte [os registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os tipos de eventos de atividade sessão incluem:

## <a name="troubleshooting"></a>Resolução de problemas

O relatório sumário de fornecimento e os registos de fornecimento desempenham um papel fundamental, ajudando os administradores a resolver em vários problemas o fornecimento de contas de utilizador.

Para obter orientações baseadas em cenários sobre como resolver o fornecimento automático de utilizadores, consulte problemas de configuração e fornecimento de [utilizadores a uma aplicação](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
