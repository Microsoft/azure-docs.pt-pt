---
title: Utilize livros de trabalho do Monitor Azure com serviços de domínio Azure AD / Microsoft Docs
description: Saiba como usar os livros de trabalho do Monitor Azure para rever as auditorias de segurança e compreender problemas num domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 5d19cddc82d9fb092887c5cdeba36b8ab7127f94
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175139"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Rever eventos de auditoria de segurança em Azure Ative Directory Domain Services usando livros de trabalho do Azure Monitor

Para ajudá-lo a compreender o estado do seu domínio gerido Azure Ative Directory Domain Services (Azure AD DS), pode ativar eventos de auditoria de segurança. Estes eventos de auditoria de segurança podem então ser revistos usando livros de trabalho do Azure Monitor que combinam texto, consultas analíticas e parâmetros em relatórios interativos ricos. O Azure AD DS inclui modelos de livros para visão geral de segurança e atividade de conta que permitem investigar eventos de auditoria e gerir o seu ambiente.

Este artigo mostra-lhe como usar os Livros de Trabalho do Monitor Azure para rever eventos de auditoria de segurança em Azure AD DS.

## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Eventos de auditoria de segurança habilitados para o seu domínio gerido que fluem dados para um espaço de trabalho Log Analytics.
    * Se necessário, [permitir auditorias de segurança para a Azure AD DS][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Visão geral dos livros de trabalho do Monitor Azure

Quando os eventos de auditoria de segurança são ligados em Azure AD DS, pode ser difícil analisar e identificar problemas no domínio gerido. O Azure Monitor permite-lhe agregar estes eventos de auditoria de segurança e consultar os dados. Com os Livros de Trabalho do Monitor Azure, pode visualizar estes dados para torná-lo mais rápido e fácil de identificar problemas.

Os modelos do livro são relatórios curados que são projetados para reutilização flexível por vários utilizadores e equipas. Quando abre um modelo de livro, os dados do ambiente do Monitor Azure estão carregados. Pode utilizar modelos sem impacto em outros utilizadores da sua organização e pode guardar os seus próprios livros com base no modelo.

Azure AD DS inclui os seguintes dois modelos de livro:

* Relatório geral de segurança
* Relatório de atividade da conta

Para obter mais informações sobre como editar e gerir livros, consulte [a visão geral dos livros de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Use o livro de relatório de relatório de segurança

Para ajudá-lo a entender melhor o uso e identificar potenciais ameaças à segurança, o relatório geral de segurança resume os dados de inscrição e identifica as contas que pode querer verificar. Você pode ver eventos em um determinado intervalo de datas, e aprofundar em eventos específicos de entrada, tais como más tentativas de senha ou onde a conta foi desativada.

Para aceder ao modelo de livro para o relatório geral de segurança, complete os seguintes passos:

1. Procure e selecione **Azure Ative Directory Domain Services** no portal Azure.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu do lado esquerdo, escolha **Monitoring > Workbooks**

    ![Screenshot que hightlights onde selecionar o Relatório geral de segurança e o Relatório de Atividade da Conta.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Escolha o **Relatório geral de segurança**.
1. A partir dos menus suspensos no topo do livro, selecione a sua subscrição Azure e, em seguida, um espaço de trabalho do Azure Monitor.

    Escolha um **intervalo de tempo**, como os *últimos 7 dias*, como mostrado no seguinte exemplo de imagem:

    ![Selecione a opção de menu de livros de trabalho no portal Azure](./media/use-azure-monitor-workbooks/select-query-filters.png)

    As opções **de visualização de azulejos** e **gráficos** também podem ser alteradas para analisar e visualizar os dados conforme desejado.

1. Para aprofundar um tipo de evento específico, selecione um dos cartões **de resultado de entrada de inscrição,** como conta *bloqueada,* como mostra o seguinte exemplo:

    ![Exemplo Dados do Relatório Geral de Segurança visualizados nos livros de trabalho do Monitor Azure](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A parte inferior do relatório geral de segurança abaixo do gráfico, em seguida, decompõe o tipo de atividade selecionado. Pode filtrar por nomes de utilizador envolvidos no lado direito, como mostra o seguinte relatório de exemplo:

    [![Detalhes dos bloqueios de conta nos livros de trabalho do Monitor Azure.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Use o livro de relatório de atividade da conta

Para ajudá-lo a resolver problemas para uma conta de utilizador específica, o relatório de atividade da conta decompõe informações detalhadas de registo de eventos de auditoria. Pode rever quando foi fornecido um mau nome de utilizador ou palavra-passe durante a sindução e a origem da tentativa de iniciar seduca.

Para aceder ao modelo de livro para o relatório de atividade da conta, complete os seguintes passos:

1. Procure e selecione **Azure Ative Directory Domain Services** no portal Azure.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu do lado esquerdo, escolha **Monitoring > Workbooks**
1. Escolha o **Relatório de Atividade da Conta.**
1. A partir dos menus suspensos no topo do livro, selecione a sua subscrição Azure e, em seguida, um espaço de trabalho do Azure Monitor.

    Escolha um **intervalo de tempo**, como os *últimos 30 dias*, então como pretende que a vista de **Azulejos** represente os dados.

    Pode filtrar o **nome de utilizador da conta**, como o *félix,* como mostra o seguinte relatório de exemplo:

    [![Relatório de atividade da conta nos livros de trabalho do Monitor Azure.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A área abaixo do gráfico mostra eventos individuais de entrada, juntamente com informações como o resultado da atividade e a estação de trabalho de origem. Estas informações podem ajudar a determinar fontes repetidas de eventos de entrada que podem causar bloqueios de conta ou indicar um potencial ataque.

Tal como acontece com o relatório geral de segurança, pode perfurar os diferentes azulejos no topo do relatório para visualizar e analisar os dados conforme necessário.

## <a name="save-and-edit-workbooks"></a>Guardar e editar livros de trabalho

Os dois livros de modelo fornecidos pela Azure AD DS são um bom lugar para começar com a sua própria análise de dados. Se precisar de obter mais granular nas consultas de dados e investigações, pode guardar os seus próprios livros e editar as consultas.

1. Para guardar uma cópia de um dos modelos do livro, **selecione Editar > Guardar como > Relatórios Partilhados**, em seguida, forneça um nome e guarde-o.
1. A partir da sua própria cópia do modelo, **selecione Editar** para entrar no modo de edição. Pode escolher o botão **de Edição** azul ao lado de qualquer parte do relatório e alterá-lo.

Todos os gráficos e tabelas em Azure Monitor Workbooks são gerados usando consultas kusto. Para obter mais informações sobre a criação das suas próprias consultas, consulte [as consultas de registo do Azure Monitor][azure-monitor-queries] e o tutorial de consultas de [Kusto.][kusto-queries]

## <a name="next-steps"></a>Passos seguintes

Se precisar de ajustar as políticas de senha e bloqueio, consulte [as políticas de bloqueio de passwords e de conta em domínios geridos][password-policy].

Para problemas com os utilizadores, aprenda a resolver [problemas de sessão de conta][troubleshoot-sign-in] ou [problemas][troubleshoot-account-lockout]de bloqueio de conta .

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer