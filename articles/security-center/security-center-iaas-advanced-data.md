---
title: Segurança de dados de avançada para IaaS no Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como ativar a segurança de dados avançados para IaaS no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e7420adfe1608df39ef72124817f1d6dadf07db8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400148"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Segurança de dados avançados para servidores SQL no IaaS
Segurança de dados avançados para servidores do SQL Server em máquinas de virtuais do Azure é um pacote unificado para funções de segurança avançadas do SQL. Atualmente, ele inclui uma funcionalidade para analisar e mitigar potenciais vulnerabilidades de base de dados e detetar atividades anómalas que poderá indicar uma ameaça à sua base de dados. 

Este recurso para servidores SQL de VMs do Azure de segurança baseia-se com a mesma tecnologia fundamental utilizada na [pacote de segurança de dados avançada do Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Descrição geral

Segurança de dados avançada fornece um conjunto de capacidades avançadas de segurança SQL, que consiste de avaliação de vulnerabilidade e proteção avançada contra ameaças.

* [Avaliação de vulnerabilidade](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é fácil de configurar o serviço que pode detetar, controlar e ajudá-lo a remediar potenciais vulnerabilidades das bases de dados. Ele fornece visibilidade sobre o estado da segurança e inclui os passos para resolver problemas de segurança e melhorar seu fortifications de base de dados.
* [Proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração do SQL server. Continuamente monitoriza a sua base de dados para atividades suspeitas e fornece alertas de segurança orientadas por ações nos padrões de acesso de base de dados anómalas. Estes alertas indique os detalhes de atividade suspeita e as ações recomendadas para investigar e mitigar a ameaça.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Comece com segurança avançada de dados SQL em VMs do Azure

Os seguintes passos ajudá-lo com segurança avançada de dados para SQL em VMs do Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurar a segurança avançada de dados para SQL em VMs do Azure

**Antes de começar**: Precisa de uma área de trabalho do Log Analytics para armazenar os registos de segurança a ser analisados. Se não tiver um, então, pode criá-lo facilmente, conforme explicado [criar uma área de trabalho do Log Analytics no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Ligue-se a VM que aloja o SQL server para a área de trabalho do Log Analytics. Para obter instruções, consulte [computadores Windows ligar para o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Do Azure Marketplace, vá para o [solução de segurança de dados avançada SQL](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Pode encontrá-lo usando a opção de pesquisa de mercado, como ver na imagem seguinte.) O **segurança de dados avançada SQL** é aberta a página.

    ![Segurança de dados avançados para IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Clique em **Criar**. As áreas de trabalho são apresentadas.

    ![Criar segurança de dados avançada](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selecione a área de trabalho para utilizar e clique em **criar**.

   ![Selecionar área de trabalho](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Reinicie o [servidor SQL da VM](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Explore e investigar alertas de segurança

Pode ver e gerir os alertas de segurança atual.

1. Clique em **Centro de segurança** > **alertas de segurança**e clique num alerta.

    ![Encontrar alerta](./media/security-center-advanced-iaas-data/find-alert.png)

1. Partir do **recurso atacado** coluna, clique num recurso que tenha sido atacado.

1. Para ver detalhes do alerta e ações para investigar a ameaça atual e abordar ameaças futuras, desloque para baixo a **informações gerais** página e, no **passos de remediação** secção, clique nas  **PASSOS de investigação** ligação.

    ![Passos de remediação](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Para ver os registos que estão associados com o acionamento do alerta, aceda a **áreas de trabalho de análise de registo** e siga os passos abaixo:

     > [!NOTE]
     > Se **áreas de trabalho de análise de registo** não aparecer no menu à esquerda, clique em **todos os serviços**e procure **áreas de trabalho de análise de registo**.

    1. Certifique-se de que as colunas são apresentados os **escalão de preço** e **WorkspaceID** colunas. (**Áreas de trabalho de análise de registo** > **editar colunas**, adicionar **escalão de preço** e **WorkspaceID**.)

     ![Editar colunas](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Clique na área de trabalho que tem os registos de alerta.

    1. Sob o **gerais** menu, clique em **registos**

    1. Clique junto aos olhos **SQLAdvancedThreatProtection** tabela. Os registos são listados.

     ![Ver registos](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurar notificações por E-Mail para alertas do ATP 

Pode definir uma lista de destinatários para receber uma notificação por e-mail quando são gerados alertas ASC. O e-mail contém uma ligação direta para o alerta no Centro de segurança do Azure com todos os detalhes relevantes. 

1. Aceda a **Centro de segurança** > **política de segurança** e, na linha a subscrição relevante, clique em **editar definições >** .

    ![Definições de subscrição](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Do **configurações** menu clique **notificações por E-Mail**. 
1. Na **endereço de E-Mail** texto, introduza os endereços de e-mail para receber as notificações. Pode introduzir mais do que um endereço de correio eletrónico, separando os endereços de e-mail com uma vírgula (,).  Por exemplo admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Definições de E-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. Na **notificação por E-Mail** definições, definir as seguintes opções:
  
    * **Enviar notificação por e-mail para alertas de gravidade elevada**: Em vez de enviar e-mails para todos os alertas, envie apenas para alertas de gravidade elevada.
    * **Também enviar notificações por e-mail para proprietários de subscrições**:  Envie notificações para os proprietários de subscrições demasiado.

1. Na parte superior dos **notificações por E-Mail** ecrã, clique em **guardar**.

  > [!NOTE]
  > Certifique-se de que clique em **salvar** antes de fechar a janela ou no novo **notificação por E-Mail** definições não serão guardadas.

## <a name="explore-vulnerability-assessment-reports"></a>Explore os relatórios de avaliação de vulnerabilidade

O dashboard de avaliação de vulnerabilidade fornece uma visão geral dos seus resultados de avaliação em todas as suas bases de dados. Pode ver a distribuição de bases de dados de acordo com a versão do SQL Server, juntamente com um resumo de falha versus passando bases de dados e um resumo geral de verificações de acordo com a distribuição do risco de falha.

Pode ver os resultados da avaliação de vulnerabilidade e os relatórios diretamente a partir do Log Analytics.

1. Navegue até à sua área de trabalho do Log Analytics com a solução de segurança de dados avançada.
1. Navegue para **soluções** e selecione o **avaliação de vulnerabilidades do SQL** solução.
1. Na **resumo** painel, clique em **Ver resumo** e selecione seu **relatório de avaliação de vulnerabilidade de SQL**.

    ![Relatório de avaliação do SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Carrega o dashboard de relatório. Certifique-se a janela de tempo é definida para, pelo menos, o **últimos 7 dias** , uma vez que as verificações de avaliação de vulnerabilidade são executadas nas suas bases de dados com base numa agenda fixa de uma vez por 7 dias.

    ![Definir os últimos 7 dias](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Para desagregar para obter mais detalhes, clique em qualquer um dos elementos do dashboard. Por exemplo:

   1. Clique numa verificação de vulnerabilidades no **falhada verifica resumo** secção para ver uma tabela de Log Analytics com os resultados para esta verificação nas bases de dados. Aqueles que têm os resultados são listados primeiro.

   1. Em seguida, clicar para ver os detalhes de cada vulnerabilidade, incluindo a descrição da vulnerabilidade e o impacto, o estado, o risco associado e os resultados reais nesta base de dados. Também pode ver a consulta real que foi executada para efetuar esta verificação e informações de remediação para resolver esta vulnerabilidade.

    ![Selecionar área de trabalho](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Selecionar área de trabalho](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Pode executar consultas do Log Analytics nos seus dados de resultados de avaliação de vulnerabilidades, a segmentação e decomposição de dados, de acordo com suas necessidades.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Proteção avançada contra ameaças para servidores SQL sobre alertas de VMs do Azure
Os alertas são gerados pelas tentativas invulgares e potencialmente prejudiciais de acesso ou exploração servidores SQL. Esses eventos podem disparar os seguintes alertas:

### <a name="anomalous-access-pattern-alerts"></a>Alertas de padrão de acesso anómalos

* **Acesso a partir de uma localização invulgar:** Este alerta é acionado quando ocorre uma alteração no padrão de acesso ao SQL server, em que alguém iniciou sessão para o SQL server a partir de uma localização geográfica invulgar. Causas possíveis:
     * Um empregam malicioso atacante ou anterior acedeu seu SQL Server.
     * Um usuário legítimo acedeu seu SQL Server a partir de uma localização nova.
* **Acesso a partir de uma aplicação potencialmente prejudicial**: seu alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder a base de dados. Causas possíveis:
     * Um atacante a tentar violar o seu SQL através de ferramentas de ataque comuns.
     * Uma legítima testes de penetração em ação.
* **Acesso a partir de principal invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso ao SQL server, onde alguém fez logon no SQL server utilizar um principal invulgar (utilizador SQL). Causas possíveis:
     * Um empregam malicioso atacante ou anterior acedeu seu SQL Server. 
     * Um usuário legítimo acedeu do SQL Server com um novo principal.
* **Credenciais SQL de força bruta**: Este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com credenciais diferentes. Causas possíveis:
     * Um atacante a tentar violar o seu SQL através de força bruta.
     * Uma legítima testes de penetração em ação.

### <a name="potential-sql-injection-attacks-coming"></a>Potenciais ataques de Injeção de SQL (trazer)

* **Vulnerabilidade a injeção de SQL**: Este alerta é acionado quando uma aplicação gera uma instrução SQL defeituosa na base de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Causas possíveis:
     * Um defeito no código da aplicação que constitui a instrução SQL defeituosa
     * O código de aplicação ou os procedimentos armazenados não saneiam a entrada de utilizador ao criar a instrução SQL defeituosa, o que pode ser explorado para Injeção SQL
* **Potencial injeção de SQL**: Este alerta é acionado quando uma exploração ativa ocorre contra uma vulnerabilidade de aplicação identificada a injeção de SQL. Significa que o atacante está a tentar injetar instruções SQL maliciosas através dos procedimentos armazenados ou código de aplicação com vulnerabilidade.
