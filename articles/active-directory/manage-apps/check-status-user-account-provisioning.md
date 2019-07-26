---
title: Relatar o provisionamento automático de conta de usuário para aplicativos SaaS | Microsoft Docs
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
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381254"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o provisionamento automático de conta de usuário

O Azure Active Directory (AD do Azure) inclui um [serviço de provisionamento de conta de usuário](user-provisioning.md) que ajuda a automatizar o provisionamento de desprovisionamento de contas de usuário em aplicativos SaaS e outros sistemas, para fins de gerenciamento de ciclo de vida de identidade de ponta a ponta. O Azure AD dá suporte a conectores de provisionamento de usuário pré-integrados para todos os aplicativos e sistemas na seção "em destaque" da [Galeria de aplicativos do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Este artigo descreve como verificar o status dos trabalhos de provisionamento depois que eles foram configurados e como solucionar problemas de provisionamento de usuários individuais e grupos.

## <a name="overview"></a>Descrição geral

Os conectores de provisionamento são configurados e definidos usando o [portal do Azure](https://portal.azure.com), seguindo a [documentação fornecida](../saas-apps/tutorial-list.md) para o aplicativo com suporte. Uma vez configurado e em execução, os trabalhos de provisionamento podem ser relatados usando um dos dois métodos:

* **Portal do Azure** -este artigo descreve principalmente a recuperação de informações de relatório do [portal do Azure](https://portal.azure.com), que fornece um relatório de Resumo de provisionamento, bem como os logs de auditoria de provisionamento detalhado para um determinado aplicativo.
* **API de auditoria** -Azure Active Directory também fornece uma API de auditoria que permite a recuperação programática dos logs de auditoria de provisionamento detalhados. Consulte [Azure Active Directory referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para obter a documentação específica para usar essa API. Embora este artigo não aborde especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos abaixo:

* **Sistema de origem** -o repositório de usuários do qual o serviço de provisionamento do Azure ad faz a sincronização. Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento pré-integrados, no entanto, há algumas exceções (exemplo: Sincronização de entrada do workday).
* **Sistema de destino** – o repositório de usuários ao qual o serviço de provisionamento do Azure ad faz a sincronização. Normalmente, isso é um aplicativo SaaS (exemplos: Salesforce, ServiceNow, G Suite, Dropbox for Business), mas, em alguns casos, pode ser um sistema local, como Active Directory (exemplo: Sincronização de entrada do WORKDAY para Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtendo relatórios de provisionamento do portal do Azure

Para obter informações de relatório de provisionamento para um determinado aplicativo, comece iniciando o [portal do Azure](https://portal.azure.com) e navegando até o aplicativo empresarial para o qual o provisionamento está configurado. Por exemplo, se você estiver Provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Active Directory > aplicativos empresariais > todos os aplicativos > LinkedIn Elevate**

A partir daqui, você pode acessar o relatório de Resumo de provisionamento e os logs de auditoria de provisionamento, ambos descritos abaixo.

## <a name="provisioning-summary-report"></a>Relatório de Resumo de provisionamento

O relatório de Resumo de provisionamento é visível na guia **provisionamento** para determinado aplicativo. Ele está localizado na seção **detalhes de sincronização** abaixo de **configurações**e fornece as seguintes informações:

* O número total de usuários e/grupos que foram sincronizados e estão atualmente em escopo para provisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações normalmente ocorrem a cada 20-40 minutos, após a conclusão de uma [sincronização inicial](user-provisioning.md#what-happens-during-provisioning) .
* Se uma [sincronização inicial](user-provisioning.md#what-happens-during-provisioning) foi concluída ou não.
* Se o processo de provisionamento foi colocado em quarentena e qual é o motivo do status de quarentena (por exemplo, falha ao se comunicar com o sistema de destino devido a credenciais de administrador inválidas).

O relatório de Resumo de provisionamento deve ser o primeiro lugar em que os administradores procuram verificar a integridade operacional do trabalho de provisionamento.

 ![Relatório de resumo](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Provisionando logs de auditoria

Todas as atividades executadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD, que podem ser exibidas na guia **logs de auditoria** , na categoria provisionamento de **conta** . Os tipos de eventos de atividade registrados incluem:

* **Importar eventos** – um evento de "importação" é registrado sempre que o serviço de provisionamento do Azure ad recupera informações sobre um usuário ou grupo individual de um sistema de origem ou sistema de destino. Durante a sincronização, os usuários são recuperados do sistema de origem primeiro, com os resultados registrados como eventos de "importação". As IDs correspondentes dos usuários recuperados são consultadas em relação ao sistema de destino para verificar se existem, com os resultados também registrados como eventos de "importação". Esses eventos registram todos os atributos de usuário mapeados e seus valores que foram vistos pelo serviço de provisionamento do Azure AD no momento do evento.
* **Eventos de regra de sincronização** -esses eventos relatam sobre os resultados das regras de mapeamento de atributo e quaisquer filtros de escopo configurados, depois que os dados do usuário tiverem sido importados e avaliados dos sistemas de origem e de destino. Por exemplo, se um usuário em um sistema de origem for considerado como no escopo para provisionamento e considerado não existir no sistema de destino, esse evento registrará que o usuário será provisionado no sistema de destino...
* **Eventos de exportação** -um evento de "exportação" é registrado toda vez que o serviço de provisionamento do Azure ad grava uma conta de usuário ou objeto de grupo em um sistema de destino. Esses eventos registram todos os atributos de usuário e seus valores que foram gravados pelo serviço de provisionamento do Azure AD no momento do evento. Se houve um erro durante a gravação da conta de usuário ou do objeto de grupo no sistema de destino, ele será exibido aqui.
* **Eventos de caução de processo** -as caução de processo ocorrem quando o serviço de provisionamento encontra uma falha ao tentar uma operação e começa a repetir a operação em um intervalo de retirada do tempo. Um evento de "caução" é registrado toda vez que uma operação de provisionamento foi repetida.

Ao examinar os eventos de provisionamento de um usuário individual, os eventos normalmente ocorrem nesta ordem:

1. Importar evento: O usuário é recuperado do sistema de origem.
1. Importar evento: O sistema de destino é consultado para verificar a existência do usuário recuperado.
1. Evento de regra de sincronização: Os dados do usuário dos sistemas de origem e de destino são avaliados em relação às regras de mapeamento de atributo configuradas e aos filtros de escopo para determinar qual ação, se houver, deve ser executada.
1. Exportar evento: Se o evento de regra de sincronização tiver ditado que uma ação deve ser executada (adicionar, atualizar, excluir), os resultados da ação serão registrados em um evento de exportação.

   ![Exemplo: Página log de auditoria que mostra as atividades e o status](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Pesquisando eventos de provisionamento para um usuário específico

O caso de uso mais comum para os logs de auditoria de provisionamento é verificar o status de provisionamento de uma conta de usuário individual. Para pesquisar os últimos eventos de provisionamento para um usuário específico:

1. Vá para a seção **logs de auditoria** .
1. No menu **categoria** , selecione **provisionamento de conta**.
1. No menu **intervalo de datas** , selecione o intervalo de datas que você deseja pesquisar.
1. Na barra de **pesquisa** , insira a ID de usuário do usuário que você deseja pesquisar. O formato do valor de ID deve corresponder ao que você selecionou como a ID de correspondência primária na configuração de mapeamento de atributo (por exemplo, userPrincipalName ou número de ID do funcionário). O valor de ID necessário estará visível na coluna destino (s).
1. Pressione Enter para pesquisar. Os eventos de provisionamento mais recentes serão retornados primeiro.
1. Se os eventos forem retornados, observe os tipos de atividade e se eles tiveram êxito ou falharam. Se nenhum resultado for retornado, isso significará que o usuário não existe ou ainda não foi detectado pelo processo de provisionamento se uma sincronização completa ainda não tiver sido concluída.
1. Clique em eventos individuais para exibir detalhes estendidos, incluindo todas as propriedades de usuário que foram recuperadas, avaliadas ou gravadas como parte do evento.

Para ver uma demonstração sobre como usar os logs de auditoria, consulte o vídeo abaixo. Os logs de auditoria são apresentados em volta da marca 5:30:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Dicas para exibir os logs de auditoria de provisionamento

Para melhor legibilidade no portal do Azure, selecione o botão **colunas** e escolha estas colunas:

* **Data** – mostra a data em que o evento ocorreu.
* **Destino (s)** – mostra o nome do aplicativo e a ID de usuário que são as entidades do evento.
* **Atividade** -o tipo de atividade, conforme descrito anteriormente.
* **Status** -se o evento foi bem-sucedido ou não.
* **Razão do status** -um resumo do que aconteceu no evento de provisionamento.

## <a name="troubleshooting"></a>Resolução de problemas

O relatório de Resumo de provisionamento e os logs de auditoria desempenham uma função fundamental que ajuda os administradores a solucionar problemas de vários usuários de provisionamento de conta de usuário.

Para obter diretrizes baseadas em cenários sobre como solucionar problemas de provisionamento automático de usuário, confira [problemas ao configurar e provisionar usuários em um aplicativo](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
