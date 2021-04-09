---
title: Reportar provisão automática de conta de utilizador às aplicações do SaaS
description: Saiba como verificar o estado do fornecimento automático de postos de trabalho na conta de utilizador e como resolver o fornecimento de utilizadores individuais.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579489"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatório sobre o provisionamento automático da conta de utilizador

O Azure Ative Directory (Azure AD) inclui um serviço de [fornecimento de conta de utilizador](user-provisioning.md) que ajuda a automatizar o fornecimento de contas de utilizadores em aplicações SaaS e outros sistemas, para efeitos de gestão do ciclo de vida da identidade de ponta a ponta. A Azure AD suporta conectores pré-integrados de fornecimento de utilizadores para todas as aplicações e sistemas com tutoriais de fornecimento de utilizadores [aqui](../saas-apps/tutorial-list.md).

Este artigo descreve como verificar o estado do provisionamento de postos de trabalho após a sua criação e como resolver os problemas de provisionamento de utilizadores e grupos individuais.

## <a name="overview"></a>Descrição Geral

Os conectores de provisionamento são configurados e configurados através do [portal Azure,](https://portal.azure.com)seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para a aplicação suportada. Uma vez configurados e em funcionamento, os postos de trabalho de provisionamento podem ser comunicados através de um de dois métodos:

* **Portal Azure** - Este artigo descreve principalmente a obtenção de informações de relatórios do [portal Azure,](https://portal.azure.com)que fornece simultaneamente um relatório sumário de provisionamento, bem como registos de auditoria de provisionamento detalhado para uma determinada aplicação.
* **A API de Auditoria** - Azure Ative Directy também fornece uma API de Auditoria que permite a recuperação programática dos registos de auditoria de provisionamento detalhado. Consulte [a referência da API de auditoria do Azure Ative Directory](/graph/api/resources/directoryaudit) para documentação específica da utilização desta API. Embora este artigo não cubra especificamente a forma de utilizar a API, detalha os tipos de eventos de provisionamento que são registados no registo de auditoria.

### <a name="definitions"></a>Definições

Este artigo utiliza os seguintes termos, definidos abaixo:

* **Sistema fonte** - O repositório de utilizadores de que o serviço de fornecimento AD AZure sincroniza. O Azure Ative Directory é o sistema de origem da maioria dos conectores de provisionamento pré-integrados, no entanto existem algumas exceções (exemplo: Sincronização de entrada de trabalho).
* **Sistema Alvo** - O repositório de utilizadores a que o serviço de fornecimento AD AZure sincroniza. Esta é tipicamente uma aplicação SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox para Negócios), mas em alguns casos pode ser um sistema no local, como o Ative Directory (exemplo: Workday Inbound Synchronization to Ative Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtenção de relatórios de provisionamento do portal Azure

Para obter informações de relatório de provisionamento para uma determinada aplicação, comece por lançar o [portal Azure](https://portal.azure.com) e **o Azure Ative Directory** &gt; **Enterprise Applications** &gt; **Registos de Provisionamento (pré-visualização)** na secção **Atividade.** Também pode navegar na Aplicação Enterprise para a qual o provisionamento está configurado. Por exemplo, se estiver a a provisionar utilizadores ao LinkedIn Elevate, o caminho de navegação para os detalhes da aplicação é:

**Azure Ative Directory > Aplicações empresariais > todas as aplicações > LinkedIn Elevate**

A partir daqui, pode aceder tanto à barra de provisão como aos registos de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Provisão de barras de progresso

A [barra de provisão para progressos](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) é visível no **separador Provisioning** para um determinado pedido. Está localizado na secção **Estado Atual** e mostra o estado do ciclo inicial ou incremental atual. Esta secção também mostra:

* O número total de utilizadores e/grupos que foram sincronizados e estão atualmente em possibilidade de provisão entre o sistema de origem e o sistema-alvo.
* A última vez que a sincronização foi executada. As sincronizações ocorrem normalmente a cada 20-40 minutos, depois de concluído um [ciclo inicial.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Se um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi ou não concluído.
* Se o processo de provisionamento foi ou não colocado em quarentena, e qual a razão para o estado de quarentena (por exemplo, não comunicar com o sistema-alvo devido a credenciais de administração inválidas).

O **Estado Atual** deve ser o primeiro local onde os administradores procuram verificar a saúde operacional do trabalho de a provisionamento.

 ![Relatório sumário](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Registos de provisionamento (pré-visualização)

Todas as atividades realizadas pelo serviço de fornecimento são registadas nos [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)Azure AD . Pode aceder aos registos de provisionamento no portal Azure selecionando registos de provisionamento de aplicações empresariais do **Azure Ative** &gt;  &gt; **Directory (pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de fornecimento com base no nome do utilizador ou do identificador no sistema de origem ou no sistema alvo. Para mais informações, consulte [registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os tipos de eventos de atividade registados incluem:

## <a name="troubleshooting"></a>Resolução de problemas

O relatório de provisionamento e os registos de provisionamento desempenham um papel fundamental, ajudando os administradores a resolverem vários problemas de provisionamento de contas de utilizador.

Para obter orientações baseadas em cenários sobre como resolver o fornecimento automático de utilizadores, consulte [problemas de configuração e provisionamento dos utilizadores para uma aplicação](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)