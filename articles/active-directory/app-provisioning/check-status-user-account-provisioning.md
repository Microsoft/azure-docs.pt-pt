---
title: Relatar o provisionamento automático de conta de usuário para aplicativos SaaS
description: Saiba como verificar o status de trabalhos de provisionamento automático de conta de usuário e como solucionar problemas de provisionamento de usuários individuais.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bfbd3aa57f34361323ecbdc78cddc5cf34a1179
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066983"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: relatórios sobre o provisionamento automático de conta de usuário

O Azure Ative Directory (Azure AD) inclui um serviço de [prestação](user-provisioning.md) de contas de utilizador que ajuda a automatizar o fornecimento de contas de utilizadores em aplicações SaaS e outros sistemas, para efeitos de gestão de ciclos de vida de identidade de ponta a ponta. A Azure AD suporta conectores de fornecimento de utilizadores pré-integrados para todas as aplicações e sistemas na secção "Em destaque" da [galeria de aplicações Azure AD.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Este artigo descreve como verificar o status dos trabalhos de provisionamento depois que eles foram configurados e como solucionar problemas de provisionamento de usuários individuais e grupos.

## <a name="overview"></a>Descrição geral

Os conectores de provisionamento são configurados e configurados através do [portal Azure,](https://portal.azure.com)seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para a aplicação apoiada. Uma vez configurado e em execução, os trabalhos de provisionamento podem ser relatados usando um dos dois métodos:

* **Portal Azure** - Este artigo descreve principalmente a recuperação de informações de relatório do [portal Azure,](https://portal.azure.com)que fornece tanto um relatório resumo de fornecimento como registos de auditoria de fornecimento detalhado para uma determinada aplicação.
* **Auditoria API** - Azure Ative Directory também fornece uma API de auditoria que permite a recuperação programática dos registos de auditoria de provisionamento detalhado. Consulte a referência da API de [auditoria do Azure Ative Diretório](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para documentação específica para a utilização desta API. Embora este artigo não aborde especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos abaixo:

* **Sistema Fonte** - O repositório de utilizadores de que o serviço de fornecimento de AD Azure sincroniza. Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento pré-integrados, no entanto, há algumas exceções (exemplo: sincronização de entrada de workday).
* **Target System** - O repositório de utilizadores a que o serviço de fornecimento de AD Azure sincroniza. Esse é normalmente um aplicativo SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas, em alguns casos, pode ser um sistema local, como Active Directory (exemplo: sincronização de entrada de WORKDAY para Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal do Azure

Para obter informações sobre o relatório de fornecimento para uma determinada aplicação, comece por lançar o [portal Azure](https://portal.azure.com) e **o Azure Ative Directory** &gt; **Enterprise Apps** &gt; Registos de **Provisionamento (pré-visualização)** na secção **Atividade.** Você também pode navegar até o aplicativo empresarial para o qual o provisionamento está configurado. Por exemplo, se você estiver Provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Ative Directory > Enterprise Applications > Todas as aplicações > LinkedIn Elevate**

A partir daqui, você pode acessar a barra de progresso de provisionamento e os logs de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Barra de progresso de provisionamento

A barra de [provisão](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) de progresso é visível no separador **provisionamento** para determinada aplicação. Está localizado na secção **Estado atual** por baixo **das Definições,** e mostra o estado do ciclo inicial ou incremental atual. Esta seção também mostra:

* O número total de usuários e/grupos que foram sincronizados e estão atualmente em escopo para provisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações ocorrem normalmente a cada 20-40 minutos, depois de um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) ter terminado.
* Se um [ciclo inicial](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi ou não concluído.
* Se o processo de provisionamento foi colocado em quarentena e qual é o motivo do status de quarentena (por exemplo, falha ao se comunicar com o sistema de destino devido a credenciais de administrador inválidas).

O **Estado atual** deve ser o primeiro local que os administradores procuram verificar a saúde operacional do trabalho de provisionamento.

 ![Relatório de resumo](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Provisionando logs (versão prévia)

Todas as atividades realizadas pelo serviço de provisionamento são registadas nos registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)da AD Azure. Pode aceder aos registos de fornecimento no portal Azure selecionando o **Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning (pré-visualização)** na secção **Atividade.** Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para mais detalhes, consulte [os registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os tipos de eventos de atividade registrados incluem:

## <a name="troubleshooting"></a>Resolução de Problemas

O relatório de Resumo de provisionamento e os logs de provisionamento desempenham uma função importante que ajuda os administradores a solucionar problemas de provisionamento de várias contas de usuário.

Para obter orientações baseadas em cenários sobre como resolver o fornecimento automático de utilizadores, consulte problemas de configuração e fornecimento de [utilizadores a uma aplicação](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
