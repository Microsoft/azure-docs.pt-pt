---
title: Utilizar livros de trabalho do Monitor Azure com serviços de domínio Azure AD [ Microsoft Docs
description: Aprenda a utilizar os Livros de Trabalho do Monitor Azure para rever auditorias de segurança e compreender questões num domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: 4bf6926651a0e2e9289b3c729abea16b1c66bce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108643"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Reveja os eventos de auditoria de segurança nos Serviços de Domínio da AD azure utilizando livros de trabalho do Monitor Azure

Para ajudá-lo a compreender o estado do seu domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS), pode ativar eventos de auditoria de segurança. Estes eventos de auditoria de segurança podem então ser revistos usando livros de trabalho do Monitor Azure que combinam texto, consultas de análise e parâmetros em relatórios interativos ricos. O Azure AD DS inclui modelos de livro para visão geral de segurança e atividade de conta que lhe permitem investigar eventos de auditoria e gerir o seu ambiente.

Este artigo mostra-lhe como usar os Livros de Trabalho do Monitor Azure para rever eventos de auditoria de segurança em Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Eventos de auditoria de segurança habilitados para os seus Serviços de Domínio de Diretório Ativo Azure geridos domínio que transmitem dados para um espaço de trabalho log Analytics.
    * Se necessário, ative auditorias de segurança para os Serviços de [Domínio de Diretório Ativo Azure][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Visão geral dos livros de acompanhamento do Azure

Quando os eventos de auditoria de segurança são ligados em DS AD Azure, pode ser difícil analisar e identificar problemas no domínio gerido. O Azure Monitor permite-lhe agregar estes eventos de auditoria de segurança e consultar os dados. Com os Livros de Trabalho do Monitor Azure, pode visualizar estes dados para torná-lo mais rápido e fácil de identificar problemas.

Os modelos de livro são relatórios com curadoria que são projetados para uma reutilização flexível por vários utilizadores e equipas. Ao abrir um modelo de livro, os dados do seu ambiente Do Monitor Azure são carregados. Pode utilizar modelos sem impacto em outros utilizadores da sua organização, e pode guardar os seus próprios livros com base no modelo.

O Azure AD DS inclui os seguintes dois modelos de livro:

* Relatório de visão geral da segurança
* Relatório de atividade seleção

Para obter mais informações sobre como editar e gerir livros de trabalho, consulte a [visão geral dos Livros de Monitorização do Azure.](../azure-monitor/platform/workbooks-overview.md)

## <a name="use-the-security-overview-report-workbook"></a>Use o livro de trabalho do relatório de visão geral de segurança

Para ajudá-lo a compreender melhor o uso e identificar potenciais ameaças à segurança, o relatório de visão geral de segurança resume os dados de entrada e identifica contas que você pode querer verificar. Pode ver eventos numa determinada gama de datas e perfurar em eventos específicos de entrada, tais como tentativas de senha sacana ou onde a conta foi desativada.

Para aceder ao modelo do livro para o relatório de visão geral de segurança, complete os seguintes passos:

1. Procure e selecione Serviços de **Domínio de Diretório Ativo Azure** no portal Azure.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu do lado esquerdo, escolha **Monitorização > Livros**

    ![Selecione a opção menu Livros de Trabalho no portal Azure](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Escolha o Relatório de **Visão Geral**de Segurança .
1. A partir dos menus suspensos no topo do livro, selecione a sua subscrição Azure e, em seguida, o espaço de trabalho do Azure Monitor. Escolha uma gama de **tempo,** como os *últimos 7 dias.*

    ![Selecione a opção menu Livros de Trabalho no portal Azure](./media/use-azure-monitor-workbooks/select-query-filters.png)

    As opções de **vista tile** e **chart** também podem ser alteradas para analisar e visualizar os dados conforme desejado

1. Para aprofundar um tipo específico de evento, selecione um dos cartões de resultados do **Sign-in,** como *o Account Locked out,* como mostra o seguinte exemplo:

    ![Exemplo Segurança Visão Geral Relatório dados visualizados em Livros de Monitorização De Azure](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A parte inferior do relatório de visão geral de segurança abaixo do gráfico decompõe o tipo de atividade selecionado. Pode filtrar por nomes de utilizadores envolvidos no lado direito, como mostra o seguinte relatório de exemplo:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Utilize o livro de trabalho do relatório da atividade da conta

Para ajudá-lo a resolver problemas para uma conta específica do utilizador, o relatório de atividade da conta decompõe informações detalhadas sobre o registo de eventos de auditoria. Pode rever quando foi fornecido um mau nome de utilizador ou palavra-passe durante o sessão e a origem da tentativa de entrada.

Para aceder ao modelo do livro para o relatório de atividade da conta, complete os seguintes passos:

1. Procure e selecione Serviços de **Domínio de Diretório Ativo Azure** no portal Azure.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*
1. A partir do menu do lado esquerdo, escolha **Monitorização > Livros**
1. Escolha o Relatório de **Atividades da Conta**.
1. A partir dos menus suspensos no topo do livro, selecione a sua subscrição Azure e, em seguida, o espaço de trabalho do Azure Monitor. Escolha um intervalo de **tempo**, como os *últimos 30 dias,* e ntão como pretende que a **vista Tile** represente os dados. Pode filtrar pelo nome de **utilizador da Conta**, como *félix*, como mostra o seguinte relatório de exemplo:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A área abaixo do gráfico mostra eventos individuais de inscrição, juntamente com informações como o resultado da atividade e a estação de trabalho de origem. Estas informações podem ajudar a determinar fontes repetidas de eventos de início de sessão que podem causar bloqueios de conta ou indicar um potencial ataque.

Tal como acontece com o relatório de visão geral de segurança, pode perfurar os diferentes azulejos no topo do relatório para visualizar e analisar os dados conforme necessário.

## <a name="save-and-edit-workbooks"></a>Guardar e editar livros

Os dois livros de modelo fornecidos pelo Azure AD DS são um bom lugar para começar com a sua própria análise de dados. Se precisar de obter mais granular nas consultas e investigações de dados, pode guardar os seus próprios livros e editar as consultas.

1. Para guardar uma cópia de um dos modelos do livro, **selecione Editar > Guarde como > relatórios partilhados,** em seguida, forneça um nome e guarde-o.
1. A partir da sua própria cópia do modelo, selecione **Editar** para introduzir o modo de edição. Pode escolher o botão **de Edição** azul ao lado de qualquer parte do relatório e alterá-lo.

Todos os gráficos e tabelas em Livros de Trabalho do Monitor Azure são gerados usando consultas Kusto. Para obter mais informações sobre a criação das suas próprias consultas, consulte consultas de [registo do Monitor Azure][azure-monitor-queries] e tutorial de [consultas kusto][kusto-queries].

## <a name="next-steps"></a>Passos seguintes

Se precisar de ajustar as políticas de password e de bloqueio, consulte as políticas de [bloqueio de passwords e de conta em domínios geridos][password-policy].

Para problemas com os utilizadores, aprenda a resolver [problemas de sessão de sessão de conta][troubleshoot-sign-in] ou [problemas][troubleshoot-account-lockout]de bloqueio de conta .

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
