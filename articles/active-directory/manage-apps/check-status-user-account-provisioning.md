---
title: Relatórios sobre aprovisionamento de contas de utilizadores automático do Azure Active Directory a aplicações SaaS | Documentos da Microsoft
description: Saiba como verificar o estado das tarefas de configuração de contas de utilizadores automático e como resolver problemas de aprovisionamento de utilizadores individuais.
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
ms.openlocfilehash: 6f7386fd26de55911f51f73600f1e2bf1a70ce11
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807700"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o aprovisionamento de contas de utilizadores automático

Azure Active Directory (Azure AD) inclui um [conta de utilizador do serviço de aprovisionamento](user-provisioning.md) que ajuda a automatizar o aprovisionamento Desconfiguração de contas de utilizador em aplicações SaaS e outros sistemas, para efeitos de ciclo de vida de identidade de ponto a ponto gestão. O Azure AD suporta conectores para todos os aplicativos e sistemas na seção "Em destaque" de provisionamento de usuários previamente integrados a [Galeria de aplicações do Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Este artigo descreve como verificar o estado do aprovisionamento tarefas depois de eles foram configurados e como resolver problemas de aprovisionamento de utilizadores individuais e grupos.

## <a name="overview"></a>Descrição geral

Conectores de aprovisionamento são a configurar e configurado com o [portal do Azure](https://portal.azure.com), seguindo a [apresentava uma documentação](../saas-apps/tutorial-list.md) para a aplicação suportada. Depois de configurado e em execução, o aprovisionamento de tarefas pode ser comunicado sobre como utilizar um dos dois métodos:

* **Portal do Azure** -este artigo descreve principalmente a recuperação das informações de relatório do [portal do Azure](https://portal.azure.com), que fornece tanto um relatório de resumo de aprovisionamento, bem como aprovisionamento detalhadas registos de auditoria para um determinado aplicação.
* **API de auditoria** -Azure Active Directory também fornece uma API de auditoria que permite a obtenção programática dos logs de auditoria de aprovisionamento detalhadas. Ver [auditoria do Azure Active Directory referência da API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) para obter a documentação específica para utilizarem esta API. Embora este artigo não abrange como utilizar a API especificamente, de detalhe os tipos de aprovisionamento de eventos que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo utiliza os seguintes termos definidos abaixo:

* **Sistema de origem** -o repositório de utilizadores que sincroniza a partir do serviço de aprovisionamento do AD do Azure. O Azure Active Directory é o sistema de origem para a maioria dos conectores de aprovisionamento previamente integradas, no entanto, existem algumas exceções (exemplo: Workday sincronização de entrada).
* **Sistema de destino** -o repositório de utilizadores que sincroniza os para o Azure AD que o serviço de aprovisionamento. Normalmente, se trata de uma aplicação SaaS (exemplos: Salesforce, ServiceNow, G Suite, o Dropbox for Business), mas em alguns casos, pode ser um sistema no local como o Active Directory (exemplo: Workday sincronização de entrada para o Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obter relatórios a partir do portal do Azure de aprovisionamento

Para obter informações de relatório para um determinado aplicativo de aprovisionamento, comece por iniciar o [portal do Azure](https://portal.azure.com) e navegar para o aplicativo empresarial para os quais o aprovisionamento está configurado. Por exemplo, se estiver a aprovisionar utilizadores para elevar o LinkedIn, é o caminho de navegação para os detalhes da aplicação:

**O Azure Active Directory > aplicações empresariais > todos os aplicativos > elevar o LinkedIn**

A partir daqui, pode acessar o relatório de resumo de aprovisionamento e os registos de auditoria de aprovisionamento, ambos descrito abaixo.

## <a name="provisioning-summary-report"></a>Relatório de resumo de aprovisionamento

O relatório de resumo de aprovisionamento está visível no **aprovisionamento** separador para determinada aplicação. O mesmo está localizado no **detalhes de sincronização** secção abaixo **definições**e fornece as seguintes informações:

* O número total de utilizadores e grupos que foram sincronizadas e estão atualmente no âmbito de aprovisionamento entre o sistema de origem e o sistema de destino.
* A última vez em que a sincronização foi executada. As sincronizações ocorram, normalmente, a cada 20 e 40 minutos, depois de um [inicial sincronização](user-provisioning.md#what-happens-during-provisioning) foi concluída.
* Se pretende ou não uma [inicial sincronização](user-provisioning.md#what-happens-during-provisioning) foi concluída.
* Se pretende ou não o processo de aprovisionamento foi colocado em quarentena e o que é o motivo do Estado de quarentena (por exemplo, falha na comunicação com o sistema de destino devido a credenciais de administrador inválido).

O relatório de resumo de provisionamento deve ser o primeiro contato de administradores local para verificar o estado de funcionamento operacional a tarefa de aprovisionamento.

 ![Relatório de resumo](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Aprovisionamento de registos de auditoria

Todas as atividades executadas pelo serviço de aprovisionamento são registadas nos registos de auditoria do Azure AD, que podem ser visualizados no **registos de auditoria** separador sob a **aprovisionamento da conta** categoria. Tipos de eventos de atividade registada incluem:

* **Importar eventos** -um evento de "Importar" é registrado sempre que o serviço de aprovisionamento do AD do Azure obtém informações sobre um usuário individual ou um grupo de um sistema de origem ou o sistema de destino. Durante a sincronização, os utilizadores são obtidos a partir do sistema de origem em primeiro lugar, com os resultados registados como "Importar" eventos. Os IDs de correspondência dos utilizadores obtidos, em seguida, são consultados contra o sistema de destino para verificar se existirem, com os resultados também registados como eventos "Importar". Esses eventos registram todos os atributos de utilizador mapeadas e os respetivos valores que foram vistos pelo Azure AD do serviço de aprovisionamento no momento do evento.
* **Eventos de regra de sincronização** - esses eventos um relatório sobre os resultados das regras de mapeamento do atributo e qualquer configurado filtros de âmbito, depois de dados do utilizador foi importados e avaliados dos sistemas de origem e de destino. Por exemplo, se um usuário num sistema de origem tiver de ser no âmbito de aprovisionamento e considerado não existir no sistema de destino, em seguida, este evento regista que o utilizador será aprovisionado no sistema de destino.
* **Exportar eventos** -um evento de "exportação" é registrado sempre que o serviço de aprovisionamento do AD Azure escreve um objeto de conta ou grupo do utilizador para um sistema de destino. Esses eventos registram todos os atributos de utilizador e os respetivos valores que foram escritas pelo Azure AD do serviço de aprovisionamento no momento do evento. Se tiver ocorrido um erro ao escrever o objeto de conta ou grupo do utilizador no sistema de destino, será apresentado aqui.
* **Processar eventos de caução** -escrows processo ocorrerem quando o serviço de aprovisionamento encontra uma falha ao tentar uma operação e começa a repetir a operação num intervalo de término de tempo. Um evento de "caução" é registado sempre que uma operação de aprovisionamento foi repetida.

Ao examinar o aprovisionamento de eventos para um usuário individual, ocorrem os eventos normalmente pela seguinte ordem:

1. Evento de importação: Utilizador é recuperado do sistema de origem.
1. Evento de importação: Sistema de destino é consultado para verificar a existência do utilizador obtidos.
1. Evento de regra de sincronização: Dados de utilizador de sistemas de origem e de destino são avaliados em relação a regras de mapeamento do atributo configuradas e filtros de âmbito para determinar a ação que, se houver, deve ser efetuada.
1. Evento de exportação: Se o evento de regra de sincronização ditado que uma ação deve ser executada (Add, Update, Delete), em seguida, os resultados da ação são registrados num evento de exportação.

   ![Exemplo: Página de registo de auditoria que mostra o estado e atividades](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Pesquisa de aprovisionamento de eventos para um utilizador específico

É o caso de uso mais comum para os registos de auditoria de aprovisionamento para verificar o estado de aprovisionamento de uma conta de utilizador individuais. Para pesquisar os últimos eventos de aprovisionamento de um utilizador específico:

1. Vá para o **registos de auditoria** secção.
1. Partir do **categoria** menu, selecione **aprovisionamento da conta**.
1. Na **intervalo de datas** menu, selecione o intervalo de datas que pretende procurar.
1. Na **pesquisa** barra, introduza o ID de utilizador do utilizador que pretende procurar. O formato do valor de ID deve corresponder a tudo o que selecionou como o ID principal de correspondência na configuração do mapeamento do atributo (por exemplo, userPrincipalName ou employee ID número). O valor de ID necessário será visível na coluna destino (s).
1. Prima Enter para procurar. Os eventos mais recentes de aprovisionamento vão ser devolvidos pela primeira vez.
1. Se os eventos são devolvidos, tenha em atenção os tipos de atividade e com êxito ou falhadas. Se não são devolvidos resultados, em seguida, significa que o utilizador não existe, ou não ainda foi detectado pelo processo de aprovisionamento se uma sincronização completa ainda não tiver concluído.
1. Clique em eventos individuais para obter mais detalhes, incluindo todas as propriedades de utilizador que foram obtidas, avaliadas ou escritas como parte do evento de ver.

Para uma demonstração sobre como utilizar os registos de auditoria, veja o vídeo abaixo. Os registos de auditoria são apresentados em torno de 5:30 marcar:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Sugestões para ver os registos de auditoria de aprovisionamento

Para melhor legibilidade no portal do Azure, selecione o **colunas** botão e selecione estas colunas:

* **Data** -mostra a data em que ocorreu o evento.
* **Destino (s)** -mostra o ID de utilizador e nome de aplicação que são os assuntos do evento.
* **Atividade** -o tipo de atividade, tal como descrito anteriormente.
* **Estado** - se o evento com êxito ou não.
* **Razão do estado** -um resumo do que aconteceu no evento de aprovisionamento.

## <a name="troubleshooting"></a>Resolução de problemas

Os registos de auditoria e relatório de resumos aprovisionamento um papel importante ajudar os administradores a resolver problemas de configuração de contas de utilizador de vários.

Para orientação baseada em cenário sobre como resolver problemas de aprovisionamento automático de utilizadores, consulte [problemas ao configurar e aprovisionar utilizadores para uma aplicação](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
