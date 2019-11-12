---
title: Segurança de dados avançada para IaaS na central de segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a segurança de dados avançada para IaaS na central de segurança do Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907032"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança de dados avançada para servidores SQL em máquinas virtuais do Azure (versão prévia)
A segurança de dados avançada para servidores SQL em máquinas virtuais do Azure é um pacote unificado para recursos avançados de segurança do SQL. Esse recurso de visualização inclui a funcionalidade para identificar e reduzir possíveis vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar ameaças ao seu banco de dados. 

Esta oferta de segurança para SQL Servers de VMs do Azure baseia-se na mesma tecnologia fundamental usada no [pacote de segurança de dados avançado do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Descrição geral

A segurança avançada de dados fornece um conjunto de recursos avançados de segurança do SQL, que consistem em avaliação de vulnerabilidades e proteção avançada contra ameaças.

* A [avaliação de vulnerabilidades](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade sobre seu estado de segurança e inclui as etapas para resolver problemas de segurança e aprimorar seu banco de dados fortifications.
* A [proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar o SQL Server. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança orientados a ações em padrões de acesso de banco de dados anormais. Esses alertas fornecem os detalhes da atividade suspeita e as ações recomendadas para investigar e atenuar a ameaça.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Introdução à segurança de dados avançada para SQL em VMs do Azure

As etapas a seguir o ajudarão a começar a usar a segurança de dados avançada para SQL na visualização pública de VMs do Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurar a segurança de dados avançada para SQL em VMs do Azure

Habilitar a segurança de dados avançada para servidores SQL em máquinas virtuais no nível de assinatura/espaço de trabalho:
 
1. Na barra lateral da central de segurança, abra a página **configurações de preço &** .

1. Selecione a assinatura ou o espaço de trabalho para o qual você deseja habilitar a segurança de dados avançada para SQL em VMs do Azure.

1. Alterne a opção para **SQL Servers na VM (visualização)** para habilitado. 

    (Clique na captura de tela para expandir)

    [![recomendações e alertas da central de segurança como visto no centro de administração do Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    A segurança de dados avançada para servidores SQL será habilitada em todos os SQL Servers conectados ao espaço de trabalho selecionado ou ao espaço de trabalho padrão da assinatura selecionada.

    >[!NOTE]
    > A solução estará totalmente ativa após a primeira reinicialização do SQL Server. 

Para criar um novo espaço de trabalho, siga as instruções em [criar um log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Para conectar o host do SQL Server a um espaço de trabalho, siga as instruções em [conectar computadores Windows ao Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-atp-alerts"></a>Configurar notificação por email para alertas ATP 

Você pode definir uma lista de destinatários para receber uma notificação por email quando os alertas da central de segurança são gerados. O email contém um link direto para o alerta na central de segurança do Azure com todos os detalhes relevantes. 

1. Vá para a **central de segurança** > **preços & configurações** e clique na assinatura relevante

    ![Configurações de assinatura](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. No menu configurações, clique em **notificações de email**. 
1. Na caixa de texto **endereço de email** , insira os endereços de email para receber as notificações. Você pode inserir mais de um endereço de email separando os endereços de email com uma vírgula (,).  Por exemplo admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![Definições de E-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nas configurações de **notificação de email** , defina as seguintes opções:
  
    * **Enviar notificação por email para alertas de alta gravidade**: em vez de enviar emails para todos os alertas, envie somente para alertas de severidade alta.
    * **Também enviar notificações por email para proprietários de assinatura**: enviar notificações para os proprietários de assinaturas também.

1. Na parte superior da tela de **notificações de email** , clique em **salvar**.

  > [!NOTE]
  > Certifique-se de clicar em **salvar** antes de fechar a janela, ou as novas configurações de **notificação de email** não serão salvas.

## <a name="explore-vulnerability-assessment-reports"></a>Explorar relatórios de avaliação de vulnerabilidade

O painel de avaliação de vulnerabilidade fornece uma visão geral dos resultados da avaliação em todos os seus bancos de dados. Você pode exibir a distribuição de bancos de dados de acordo com a versão SQL Server, juntamente com um resumo de falhas em vez de passar bancos de dados e um resumo geral das verificações com falha de acordo com a distribuição de risco.

Você pode exibir os resultados da avaliação de vulnerabilidade diretamente da central de segurança.

1. Na barra lateral da central de segurança, em higiene de segurança de recursos, selecione **dados & armazenamento**.

1. Selecione as **vulnerabilidades de recomendação em seus bancos de dados SQL em VMS devem ser corrigidas (versão prévia)** . Para obter mais informações, consulte [recomendações da central de segurança](security-center-recommendations.md). 

    [![* * vulnerabilidades em seus bancos de dados SQL em VMs devem ser corrigidas (visualização) * * recomendação](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    A exibição detalhada para essa recomendação é exibida.

    [![exibição detalhada para as * * vulnerabilidades em seus bancos de dados SQL em VMs devem ser corrigidas (visualização) * * recomendação](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Para fazer uma busca detalhada para obter mais detalhes:

    * Para obter uma visão geral dos recursos verificados (bancos de dados) e da lista de verificações de segurança que foram testadas, clique no servidor de interesse.
    [![vulnerabilidades agrupadas pelo SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Para obter uma visão geral das vulnerabilidades agrupadas por um banco de dados SQL específico, clique no banco de dados de interesse.
    [![vulnerabilidades agrupadas pelo SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    Em cada exibição, as verificações de segurança são classificadas por **severidade**. Clique em uma verificação de segurança específica para ver um painel de detalhes com uma **Descrição**, como **corrigi** -lo e outras informações relacionadas, como **impacto** ou **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Proteção avançada contra ameaças para servidores SQL em alertas de VMs do Azure
Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores SQL. Esses eventos podem disparar os seguintes alertas:

### <a name="anomalous-access-pattern-alerts-preview"></a>Alertas de padrão de acesso anormal (versão prévia)

* **Acesso de local incomum:** Esse alerta é disparado quando há uma alteração no padrão de acesso para o SQL Server, em que alguém fez logon no SQL Server a partir de uma localização geográfica incomum. Possíveis causas:
    * Um invasor ou um antigo emprego mal-intencionado acessou seu SQL Server.
    * Um usuário legítimo acessou seu SQL Server de um novo local.
* **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Possíveis causas:
    * Um invasor tentando violar o SQL usando as ferramentas de ataque comuns.
    * Um teste de penetração legítimo em ação.
* **Acesso de um principal invulgar**: este alerta é acionado quando ocorre uma alteração no padrão de acesso ao servidor SQL, no qual alguém iniciou sessão no servidor SQL com recurso a um principal (utilizador SQL) invulgar. Possíveis causas:
    * Um invasor ou um antigo emprego mal-intencionado acessou seu SQL Server. 
    * Um usuário legítimo acessou seu SQL Server de com uma nova entidade de segurança.
* **Credenciais SQL de força bruta**: este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com diferentes credenciais. Possíveis causas:
    * Um invasor tentando violar o SQL usando a força bruta.
    * Um teste de penetração legítimo em ação.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Possíveis ataques de injeção de SQL (com suporte no SQL Server 2019)

* **Vulnerabilidade à injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com falha no banco de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Possíveis causas:
    * Um defeito no código da aplicação que constitui a instrução SQL defeituosa
    * O código de aplicação ou os procedimentos armazenados não saneiam a entrada de utilizador ao criar a instrução SQL defeituosa, o que pode ser explorado para Injeção SQL
* **Potencial injeção SQL**: este alerta é acionado quando uma exploração ativa ocorre contra uma vulnerabilidade de aplicação identificada para a injeção SQL. Significa que o atacante está a tentar injetar instruções SQL maliciosas através dos procedimentos armazenados ou código de aplicação com vulnerabilidade.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Comando não seguro (com suporte no SQL Server 2019)

* **Ação potencialmente não segura**: esse alerta é disparado quando um comando altamente privilegiado e potencialmente não seguro é executado. Possíveis causas:
    * O comando que é recomendado para ser desabilitado para melhor postura de segurança está habilitado.
    * Um invasor tentando explorar o acesso ao SQL ou escalonar privilégios.   


## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Os alertas de segurança de dados estão disponíveis na página de alertas da central de segurança, na guia Segurança do recurso ou por meio do link direto nos emails de alerta.

1. Para exibir alertas:

    * Na central de segurança, clique em **alertas de segurança** na barra lateral e selecione um alerta.
    * No escopo do recurso-Abra a página de recursos relevante e, na barra lateral, clique em **segurança**. 

1. Os alertas são projetados para serem independentes, com etapas de correção detalhadas e informações de investigação em cada um. Você pode investigar ainda mais usando outros recursos da central de segurança do Azure e do Azure Sentinel para uma visão mais ampla:

    * Habilite o recurso de auditoria do SQL Server para investigações adicionais. Se você for um usuário do Azure Sentinel, poderá carregar os logs de auditoria do SQL dos eventos do log de segurança do Windows para sentinela e aproveitar uma experiência de investigação rica.
    * Para melhorar sua postura de segurança, use as recomendações da central de segurança para o computador host indicado em cada alerta. Isso reduzirá os riscos de futuros ataques. 


## <a name="next-steps"></a>Passos seguintes

Para obter material relacionado, consulte o seguinte artigo:

- [Como corrigir recomendações](security-center-remediate-recommendations.md)