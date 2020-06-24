---
title: Segurança avançada de dados para o IaaS no Centro de Segurança Azure Microsoft Docs
description: Saiba como permitir a segurança avançada de dados para máquinas SQL no Azure Security Center.
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
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 1c269ad13072ae4dcff9caba892ccc0643647e5c
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254232"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Segurança avançada de dados para máquinas SQL (Pré-visualização)

A segurança avançada de dados do Azure Security Center para máquinas SQL protege os SqL Servers hospedados em Azure, em outros ambientes de nuvem e até mesmo máquinas no local. Isto alarga as proteções para os seus SqL Servers nativos do Azure para suportar totalmente ambientes híbridos.

Esta funcionalidade de pré-visualização inclui funcionalidades para identificar e mitigar potenciais vulnerabilidades de bases de dados e detetar atividades anómalas que possam indicar ameaças à sua base de dados: 

* **Avaliação de vulnerabilidade** - O serviço de digitalização para descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. As análises fornecem uma visão geral do estado de segurança das suas máquinas SQL e detalhes de quaisquer conclusões de segurança.

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) - O serviço de deteção que monitoriza continuamente os seus servidores SQL por ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Este serviço fornece alertas de segurança orientados para a ação no Centro de Segurança Azure com detalhes da atividade suspeita, orientações sobre como mitigar as ameaças e opções para continuar as suas investigações com a Azure Sentinel.

>[!TIP]
> A segurança avançada de dados para máquinas SQL é uma extensão do pacote avançado de segurança de [dados](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)do Azure Security Center, já disponível para Azure SQL Databases, Synapse e SQL Managed Instances.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Criar segurança de dados avançada para máquinas SQL 

A criação da segurança avançada de dados do Azure Security Center para máquinas SQL envolve dois passos:

* Provisionar o agente Log Analytics no anfitrião do seu servidor SQL. Isto fornece a ligação ao Azure.

* Ativar o pacote opcional na página de preços e definições do Security Center.

Ambos são descritos abaixo.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Passo 1. Provisionar o agente Log Analytics no anfitrião do seu servidor SQL:

- **SQL Server em Azure VM** - Se a sua máquina SQL estiver hospedada num VM Azure, pode [providenciar automaticamente o agente Log Analytics](security-center-enable-data-collection.md#workspace-configuration). Em alternativa, pode seguir o procedimento manual de [adição de um VM Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server em Azure Arc** - Se o seu SQL Server estiver hospedado numa máquina [Azure Arc,](https://docs.microsoft.com/azure/azure-arc/) pode implantar o agente Log Analytics utilizando a recomendação do Centro de Segurança "O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Preview)". Em alternativa, pode seguir o procedimento manual na documentação do [Arco Azure](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server on-prem** - Se o seu SQL Server estiver hospedado numa máquina Windows no local sem Azure Arc, tem duas opções para ligá-lo ao Azure:
    
    - **Implementar Azure Arc** - Pode ligar qualquer máquina do Windows ao Centro de Segurança. No entanto, o Azure Arc proporciona uma integração mais profunda em *todo* o seu ambiente Azure. Se configurar o Azure Arc, verá a página **SQL Server – Azure Arc** no portal e os seus alertas de segurança aparecerão num separador de **Segurança** dedicado nessa página. Assim, a primeira opção recomendada é [configurar o Arco Azure no anfitrião](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) e seguir as instruções para o **SQL Server em Azure Arc**, acima.
        
    - **Conecte a máquina Do Windows sem arco Azure** - Se optar por ligar um Servidor SQL a funcionar numa máquina Windows sem utilizar o Arco Azure, siga as instruções das [máquinas Connect Windows ao Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Passo 2. Ativar o pacote opcional na página de preços e definições do Centro de Segurança:

1. Da barra lateral do Security Center, abra a página **de definições de preços &.**

    - Se estiver a utilizar o **espaço de trabalho predefinido do Azure Security Center** (denominado "espaço de trabalho predefinido-[o seu id de subscrição]-[região]"), selecione a **subscrição**relevante .

    - Se estiver a utilizar **um espaço de trabalho não padrão,** selecione o espaço de **trabalho** relevante (introduza o nome do espaço de trabalho no filtro, se necessário):

        ![título](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Alternar a opção para **servidores SQL em máquinas (Pré-visualização)** para ativar. 

    [![Página de preços do Centro de Segurança com pacotes opcionais](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    A Segurança avançada de dados para servidores SQL em máquinas será ativada em todos os servidores SQL ligados ao espaço de trabalho selecionado. A proteção estará totalmente ativa após o primeiro reinício do SQL Server. 

    >[!TIP] 
    > Para criar um novo espaço de trabalho, siga as instruções no [Criar um espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Opcionalmente, configurar a notificação de e-mail para alertas de segurança. 
    Pode definir uma lista de destinatários para receber uma notificação por e-mail quando os alertas do Centro de Segurança forem gerados. O e-mail contém uma ligação direta ao alerta no Azure Security Center com todos os detalhes relevantes. Para mais informações, consulte [Configurar notificações de email para alertas de segurança.](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)



## <a name="explore-vulnerability-assessment-reports"></a>Explore relatórios de avaliação de vulnerabilidades

O serviço de avaliação de vulnerabilidades verifica as suas bases de dados uma vez por semana. As tomografias são executadas no mesmo dia da semana em que ativou o serviço.

O painel de avaliação de vulnerabilidades fornece uma visão geral dos resultados da sua avaliação em todas as suas bases de dados, juntamente com um resumo de bases de dados saudáveis e pouco saudáveis, e um resumo geral de verificações falhadas de acordo com a distribuição de riscos.

Pode ver os resultados da avaliação da vulnerabilidade diretamente do Centro de Segurança.

1. A partir da barra lateral do Security Center, abra a página **recomendações** e selecione a recomendação **Vulnerabilidades nos servidores de base de dados SQL em máquinas devem ser remediadas (Pré-visualização)**. Para mais informações, consulte [as Recomendações do Centro de Segurança.](security-center-recommendations.md) 


    [![**As vulnerabilidades nas bases de dados sql em máquinas devem ser remediadas (Pré-visualização)** recomendação](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    A opinião pormenorizada desta recomendação aparece.

    [![A visão detalhada das vulnerabilidades nas bases de dados do SQL em máquinas deve ser remediada (Pré-visualização)** recomendação](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Para mais detalhes, desacaia:

    * Para uma visão geral dos recursos digitalizados (bases de dados) e da lista de verificações de segurança que foram testadas, selecione o servidor de interesse.

    * Para uma visão geral das vulnerabilidades agrupadas por uma base de dados SQL específica, selecione a base de dados de interesses.

    Em cada vista, os controlos de segurança são classificados pela **Severidade**. Clique numa verificação de segurança específica para ver um painel de detalhes com uma **Descrição**, como **remediar** e outras informações relacionadas, como **Impacto** ou **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Proteção avançada de ameaças para servidores SQL em alertas de máquinas
Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar máquinas SQL. Estes eventos podem desencadear alertas apresentados nos Alertas para a [Base de Dados SQL e na secção SQL Data Warehouse da página de referência dos alertas](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Explore e investigue alertas de segurança

Os alertas de segurança estão disponíveis na página de alerta do Security Center, no separador de segurança do recurso ou no link direto nos e-mails de alerta.

1. Para visualizar alertas, selecione **alertas** de segurança da barra lateral do Centro de Segurança e selecione um alerta.

1. Os alertas são projetados para serem autossuficientes, com medidas de reparação detalhadas e informações de investigação em cada um deles. Você pode investigar mais adiante usando outras capacidades do Azure Security Center e Azure Sentinel para uma visão mais ampla:

    * Ativar o recurso de auditoria do SQL Server para mais investigações. Se você é um utilizador do Azure Sentinel, você pode carregar os registos de auditoria SQL dos eventos do Windows Security Log para Sentinel e desfrutar de uma experiência de investigação rica. [Saiba mais sobre a Auditoria do Servidor SQL.](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)
    * Para melhorar a sua postura de segurança, utilize as recomendações do Centro de Segurança para a máquina de anfitrião indicada em cada alerta. Isto reduzirá os riscos de futuros ataques. 

    [Saiba mais sobre como gerir e responder a alertas.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)


## <a name="next-steps"></a>Passos seguintes

Para material relacionado, consulte o seguinte artigo:

- [Alertas de segurança para base de dados SQL e Armazém de Dados SQL](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurar notificações de email para alertas de segurança](security-center-provide-security-contact-details.md)
- [Saiba mais sobre a Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [O pacote avançado de segurança de dados do Azure Security Center](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)