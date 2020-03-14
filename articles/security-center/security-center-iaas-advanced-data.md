---
title: Segurança avançada de dados para IaaS no Centro de Segurança Azure Microsoft Docs
description: " Saiba como permitir a segurança avançada de dados para o IaaS no Centro de Segurança Azure. "
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
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282734"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança avançada de dados para servidores SQL em Máquinas Virtuais Azure (Pré-visualização)
A segurança avançada de dados para servidores SQL em Máquinas Virtuais Azure é um pacote unificado para capacidades avançadas de segurança SQL. Esta funcionalidade de pré-visualização inclui funcionalidades para identificar e mitigar potenciais vulnerabilidades de bases de dados e detetar atividades anómalas que podem indicar ameaças à sua base de dados. 

Esta oferta de segurança para servidores SQL De VMs Azure baseia-se na mesma tecnologia fundamental utilizada no pacote de segurança avançada de dados avançados de [dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Descrição geral

A segurança avançada de dados fornece um conjunto de capacidades avançadas de segurança SQL, compostas por avaliação de vulnerabilidade e proteção avançada de ameaças.

* [A avaliação de vulnerabilidade](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode descobrir, rastrear e ajudá-lo a remediar potenciais vulnerabilidades de base de dados. Fornece visibilidade ao seu estado de segurança, e inclui os passos para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
* [A Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar o seu servidor SQL. Monitoriza continuamente a sua base de dados para atividades suspeitas e fornece alertas de segurança orientados para a ação sobre padrões de acesso anómalos à base de dados. Estes alertas fornecem detalhes de atividades suspeitas e ações recomendadas para investigar e mitigar a ameaça.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Começar com segurança avançada de dados para SQL em VMs Azure

Os seguintes passos iniciam-se com a Advanced Data Security for SQL na Pré-visualização pública de VMs Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Criar segurança avançada de dados para SQL em VMs Azure

Ativar a Segurança Avançada de Dados para Servidores SQL em Máquinas Virtuais ao nível de subscrição/espaço de trabalho:
 
1. A partir da barra lateral do Security Center, abra a página **de Preços e Configurações.**

1. Selecione a subscrição ou espaço de trabalho para o qual pretende ativar a Segurança avançada de dados para SQL em VMs Azure.

1. Alternar a opção para **servidores SQL em VM (Pré-visualização)** para ativar. 

    (Clique na imagem para expandir)

    [![recomendações e alertas do Centro de Segurança, como visto no Windows Admin Center](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    A Segurança avançada de Dados para Servidores SQL será ativada em todos os Servidores SQL ligados ao espaço de trabalho selecionado ou ao espaço de trabalho padrão da subscrição selecionada.

    >[!NOTE]
    > A solução estará totalmente ativa após o primeiro reinício do Servidor SQL. 

Para criar um novo espaço de trabalho, siga as instruções em [Criar um espaço](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)de trabalho Log Analytics .

Para ligar o hospedeiro do Servidor SQL a um espaço de trabalho, siga as instruções em [Ligar os computadores Windows ao Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Configurar notificação por e-mail para alertas de segurança 

Pode definir uma lista de destinatários para receber uma notificação de e-mail quando os alertas do Security Center forem gerados. O e-mail contém uma ligação direta ao alerta no Centro de Segurança Azure com todos os detalhes relevantes. 

1. Vá ao Centro de **Segurança** > **Preços e Definições** e clique na subscrição relevante

    ![Definições de subscrição](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. No menu Definições, clique em notificações de **e-mail.** 
1. Na caixa de texto de **endereço de e-mail,** insira os endereços de e-mail para receber as notificações. Pode introduzir mais do que um endereço de e-mail separando os endereços de e-mail com uma vírvia (,).  Por exemplo, admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![Definições de E-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nas definições de notificação de **e-mail,** detete as seguintes opções:
  
    * **Envie notificação por e-mail para alertas**de alta gravidade : Em vez de enviar e-mails para todos os alertas, envie apenas para alertas de alta gravidade.
    * **Envie também notificações por e-mail aos proprietários de subscrições**: Envie notificações aos proprietários de subscrições também.

1. No topo do ecrã de notificações de **e-mail,** clique em **Guardar**.

  > [!NOTE]
  > Certifique-se de clicar em **Guardar** antes de fechar a janela, ou as novas definições de notificação de **e-mail** não serão guardadas.

## <a name="explore-vulnerability-assessment-reports"></a>Explore relatórios de avaliação de vulnerabilidades

O dashboard de avaliação de vulnerabilidade fornece uma visão geral dos resultados da sua avaliação em todas as suas bases de dados. Pode visualizar a distribuição de bases de dados de acordo com a versão SQL Server, juntamente com um resumo de falhas versus bases de dados de passagem e um resumo geral de verificações falhadas de acordo com a distribuição de risco.

Pode visualizar os resultados da avaliação da vulnerabilidade diretamente do Centro de Segurança.

1. Da barra lateral do Security Center, sob HIGIENE DE SEGURANÇA DE RECURSOS, **selecione Dados e Armazenamento**.

1. Selecione a recomendação As vulnerabilidades nas bases de **dados SQL nas VMs devem ser remediadas (Pré-visualização)** . Para mais informações, consulte recomendações do Centro de [Segurança.](security-center-recommendations.md) 

    [![**Vulnerabilidades nas suas bases de dados SQL em VMs devem ser remediadas (Pré-visualização)** recomendação](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    O ponto de vista pormenorizado desta recomendação aparece.

    [![A visão detalhada para as **Vulnerabilidades nas suas bases de dados SQL em VMs deve ser remediada (Preview)** recomendação](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Para aprofundar para obter mais detalhes:

    * Para uma visão geral dos recursos digitalizados (bases de dados) e da lista de verificações de segurança que foram testadas, clique no servidor de interesses.
    [vulnerabilidades ![agruparadas pelo servidor SQL](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Para uma visão geral das vulnerabilidades agrunadas por uma base de dados SQL específica, clique na base de dados de interesses.
    [vulnerabilidades ![agruparadas pelo servidor SQL](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    Em cada perspetiva, os controlos de segurança são ordenados pela **Severity.** Clique numa verificação de segurança específica para ver um painel de detalhes com uma **Descrição,** como **remediar** e outras informações relacionadas, como **Impacto** ou **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Proteção avançada de ameaças para servidores SQL em alertas de VMs Azure
Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar servidores SQL. Estes eventos podem desencadear os seguintes alertas:

### <a name="anomalous-access-pattern-alerts-preview"></a>Alertas de padrão de acesso anómalo (Pré-visualização)

* **Acesso a partir de uma localização invulgar:** Este alerta é desencadeado quando há uma alteração no padrão de acesso ao servidor SQL, onde alguém acedeu ao servidor SQL a partir de uma localização geográfica incomum. Causas potenciais:
    * Um intruso ou antigo empregado malicioso acedeu ao seu Servidor SQL.
    * Um utilizador legítimo acedeu ao seu Servidor SQL a partir de um novo local.
* **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Causas potenciais:
    * Um intruso a tentar violar o seu SQL usando ferramentas comuns de ataque.
    * Um teste legítimo de penetração em ação.
* **Acesso de um principal invulgar**: este alerta é acionado quando ocorre uma alteração no padrão de acesso ao servidor SQL, no qual alguém iniciou sessão no servidor SQL com recurso a um principal (utilizador SQL) invulgar. Causas potenciais:
    * Um intruso ou antigo empregado malicioso acedeu ao seu Servidor SQL. 
    * Um utilizador legítimo acedeu ao seu Servidor SQL a partir de um novo diretor.
* **Credenciais SQL de força bruta**: este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com diferentes credenciais. Causas potenciais:
    * Um agressor a tentar violar o seu SQL usando força bruta.
    * Um teste legítimo de penetração em ação.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenciais ataques de injeção SQL (Suportados no Servidor SQL 2019)

* **Vulnerabilidade à injeção de SQL**: Este alerta é desencadeado quando uma aplicação gera uma declaração SQL defeituosa na base de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Causas potenciais:
    * Um defeito no código da aplicação que constitui a instrução SQL defeituosa
    * O código de aplicação ou os procedimentos armazenados não saneiam a entrada de utilizador ao criar a instrução SQL defeituosa, o que pode ser explorado para Injeção SQL
* **Potencial injeção SQL**: este alerta é acionado quando uma exploração ativa ocorre contra uma vulnerabilidade de aplicação identificada para a injeção SQL. Significa que o atacante está a tentar injetar instruções SQL maliciosas através dos procedimentos armazenados ou código de aplicação com vulnerabilidade.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Comando inseguro (Suportado no Servidor SQL 2019)

* **Ação Potencialmente Insegura**: Este alerta é desencadeado quando um comando altamente privilegiado e potencialmente inseguro é executado. Causas potenciais:
    * O comando recomendado para ser desativado para uma melhor postura de segurança está ativado.
    * Um intruso que tenta explorar o acesso da SQL ou escalar privilégios.   


## <a name="explore-and-investigate-security-alerts"></a>Explore e investigue alertas de segurança

Os seus alertas de segurança de dados estão disponíveis na página de alertas do Security Center, no separador de segurança do recurso ou através do link direto nos e-mails de alerta.

1. Para ver alertas:

    * No Centro de Segurança - Clique em **alertas** de segurança a partir da barra lateral e selecione um alerta.
    * No âmbito do recurso - Abra a página de recursos relevante, e a partir da barra lateral clique em **Segurança**. 

1. Os alertas destinam-se a ser autossuficientes, com etapas detalhadas de reparação e informações de investigação em cada um deles. Você pode investigar mais através do uso de outras capacidades do Azure Security Center e Do Azure Sentinel para uma visão mais ampla:

    * Ative a funcionalidade de auditoria do SQL Server para mais investigações. Se for um utilizador do Azure Sentinel, pode enviar os registos de auditoria SQL dos eventos de Registo de Segurança do Windows para o Sentinel e desfrutar de uma rica experiência de investigação.
    * Para melhorar a sua postura de segurança, utilize as recomendações do Security Center para a máquina hospedeira indicada em cada alerta. Isto reduzirá os riscos de futuros ataques. 


## <a name="next-steps"></a>Passos seguintes

Para material relacionado, consulte o seguinte artigo:

- [Como remediar recomendações](security-center-remediate-recommendations.md)