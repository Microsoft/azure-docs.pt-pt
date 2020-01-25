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
ms.openlocfilehash: 77865ad3fe23d1413b6b957d351d9d9d92d22fb7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711979"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: relatórios sobre o provisionamento automático de conta de usuário

O Azure Active Directory (AD do Azure) inclui um [serviço de provisionamento de conta de usuário](user-provisioning.md) que ajuda a automatizar o provisionamento de desprovisionamento de contas de usuário em aplicativos SaaS e outros sistemas, para fins de gerenciamento de ciclo de vida de identidade de ponta a ponta. O Azure AD dá suporte a conectores de provisionamento de usuário pré-integrados para todos os aplicativos e sistemas na seção "em destaque" da [Galeria de aplicativos do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Este artigo descreve como verificar o status dos trabalhos de provisionamento depois que eles foram configurados e como solucionar problemas de provisionamento de usuários individuais e grupos.

## <a name="overview"></a>Visão geral

Os conectores de provisionamento são configurados e definidos usando o [portal do Azure](https://portal.azure.com), seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para o aplicativo com suporte. Uma vez configurado e em execução, os trabalhos de provisionamento podem ser relatados usando um dos dois métodos:

* **Portal do Azure** -este artigo descreve principalmente a recuperação de informações de relatório do [portal do Azure](https://portal.azure.com), que fornece um relatório de Resumo de provisionamento, bem como os logs de auditoria de provisionamento detalhado para um determinado aplicativo.
* **API de auditoria** -Azure Active Directory também fornece uma API de auditoria que permite a recuperação programática dos logs de auditoria de provisionamento detalhados. Consulte [Azure Active Directory referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para obter a documentação específica para usar essa API. Embora este artigo não aborde especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos abaixo:

* **Sistema de origem** -o repositório de usuários do qual o serviço de provisionamento do Azure ad faz a sincronização. Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento pré-integrados, no entanto, há algumas exceções (exemplo: sincronização de entrada de workday).
* **Sistema de destino** – o repositório de usuários ao qual o serviço de provisionamento do Azure ad faz a sincronização. Esse é normalmente um aplicativo SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas, em alguns casos, pode ser um sistema local, como Active Directory (exemplo: sincronização de entrada de WORKDAY para Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal do Azure

Para obter informações de relatório de provisionamento para um determinado aplicativo, comece iniciando o [portal do Azure](https://portal.azure.com) e **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **os logs de provisionamento (versão prévia)** na seção **atividade** . Você também pode navegar até o aplicativo empresarial para o qual o provisionamento está configurado. Por exemplo, se você estiver Provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Active Directory > aplicativos empresariais > todos os aplicativos > LinkedIn Elevate**

A partir daqui, você pode acessar a barra de progresso de provisionamento e os logs de provisionamento, descritos abaixo.

## <a name="provisioning-progress-bar"></a>Barra de progresso de provisionamento

A [barra de progresso de provisionamento](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) fica visível na guia **provisionamento** para determinado aplicativo. Ele está localizado na seção **status atual** abaixo de **configurações**e mostra o status do ciclo inicial ou incremental atual. Esta seção também mostra:

* O número total de usuários e/grupos que foram sincronizados e estão atualmente em escopo para provisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações normalmente ocorrem a cada 20-40 minutos, após a conclusão de um [ciclo inicial](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Se um [ciclo inicial](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) foi concluído ou não.
* Se o processo de provisionamento foi colocado em quarentena e qual é o motivo do status de quarentena (por exemplo, falha ao se comunicar com o sistema de destino devido a credenciais de administrador inválidas).

O **status atual** deve ser o primeiro lugar em que os administradores procuram verificar a integridade operacional do trabalho de provisionamento.

 ![Relatório de resumo](media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Provisionando logs (versão prévia)

Todas as atividades executadas pelo serviço de provisionamento são registradas nos [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)do Azure AD. Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **Provisionando logs (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os tipos de eventos de atividade registrados incluem:

## <a name="troubleshooting"></a>Resolução de problemas

O relatório de Resumo de provisionamento e os logs de provisionamento desempenham uma função importante que ajuda os administradores a solucionar problemas de provisionamento de várias contas de usuário.

Para obter diretrizes baseadas em cenários sobre como solucionar problemas de provisionamento automático de usuário, confira [problemas ao configurar e provisionar usuários em um aplicativo](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
