---
title: Como utilizar o Azure Defender para o SQL
description: Saiba como usar o Azure Security Center opcional para o plano SQL
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: 96af34b5b68fca5ab8061c8c99f03bee094dc175
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590376"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>Azure Defender para servidores SQL em máquinas 

Este plano Azure Defender deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Você verá alertas quando há atividades de base de dados suspeitas, potenciais vulnerabilidades, ou ataques de injeção DE SQL, e padrões anómalos de acesso e consulta de bases de dados.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|**O Azure Defender para servidores SQL em máquinas** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Versões SQL protegidas:|Azure SQL Server (todas as versões abrangidas pelo suporte da Microsoft)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Outro Gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Configurar o Azure Defender para servidores SQL em máquinas

Para ativar este plano:

* Provisionar o agente Log Analytics no anfitrião do seu servidor SQL. Isto fornece a ligação ao Azure.

* Ativar o plano opcional na página de preços e definições do Centro de Segurança.

Ambos são descritos abaixo.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Passo 1. Provisionar o agente Log Analytics no anfitrião do seu servidor SQL:

- **SQL Server em Azure VM** - Se a sua máquina SQL estiver hospedada num VM Azure, pode [ativar o fornecimento automático do agente <a name="auto-provision-mma"></a> Log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Em alternativa, pode seguir o procedimento manual para [A bordo dos seus VMs Azure Stack](quickstart-onboard-machines.md#onboard-your-azure-stack-vms).
- **SQL Server em Azure Arc** - Se o seu SQL Server for gerido por servidores ativados pelo [Azure Arc,](../azure-arc/index.yml) pode implementar o agente Log Analytics utilizando a recomendação do Centro de Segurança "O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Preview)". Em alternativa, pode seguir os métodos de instalação descritos na documentação do [Arco Azure](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server on-prem** - Se o seu SQL Server estiver hospedado numa máquina Windows no local sem Azure Arc, tem duas opções para ligá-lo ao Azure:
    
    - **Implementar Azure Arc** - Pode ligar qualquer máquina do Windows ao Centro de Segurança. No entanto, o Azure Arc proporciona uma integração mais profunda em *todo* o seu ambiente Azure. Se configurar o Azure Arc, verá a página **SQL Server – Azure Arc** no portal e os seus alertas de segurança aparecerão num separador de **Segurança** dedicado nessa página. Assim, a primeira opção recomendada é [configurar o Arco Azure no anfitrião](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) e seguir as instruções para o **SQL Server em Azure Arc**, acima.
        
    - **Conecte a máquina Do Windows sem arco Azure** - Se optar por ligar um Servidor SQL a funcionar numa máquina Windows sem utilizar o Arco Azure, siga as instruções das [máquinas Connect Windows ao Azure Monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Passo 2. Ativar o plano opcional na página de preços e definições do Centro de Segurança:

1. A partir do menu do Security Center, abra a página **de definições de preços &.**

    - Se estiver a utilizar o **espaço de trabalho predefinido do Azure Security Center** (denominado "espaço de trabalho predefinido-[o seu id de subscrição]-[região]"), selecione a **subscrição** relevante .

    - Se estiver a utilizar **um espaço de trabalho não padrão,** selecione o espaço de **trabalho** relevante (introduza o nome do espaço de trabalho no filtro, se necessário):

        ![Encontrar o seu espaço de trabalho não padrão por título](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Desconfie a opção **para O Azure Defender para servidores SQL em máquinas** planejar **para ligado**. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Página de preços do Centro de Segurança com planos opcionais":::

    O plano será ativado em todos os servidores SQL ligados ao espaço de trabalho selecionado. A proteção estará totalmente ativa após o primeiro reinício da instância SQL Server.

    >[!TIP] 
    > Para criar um novo espaço de trabalho, siga as instruções no [Criar um espaço de trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md).


1. Opcionalmente, configurar a notificação de e-mail para alertas de segurança. 
    Pode definir uma lista de destinatários para receber uma notificação por e-mail quando os alertas do Centro de Segurança forem gerados. O e-mail contém uma ligação direta ao alerta no Azure Security Center com todos os detalhes relevantes. Para mais informações, consulte [Configurar notificações de email para alertas de segurança.](security-center-provide-security-contact-details.md)



## <a name="explore-vulnerability-assessment-reports"></a>Explore relatórios de avaliação de vulnerabilidades

O serviço de avaliação de vulnerabilidades verifica as suas bases de dados uma vez por semana. As tomografias são executadas no mesmo dia da semana em que ativou o serviço.

O painel de avaliação de vulnerabilidades fornece uma visão geral dos resultados da sua avaliação em todas as suas bases de dados, juntamente com um resumo de bases de dados saudáveis e pouco saudáveis, e um resumo geral de verificações falhadas de acordo com a distribuição de riscos.

Pode ver os resultados da avaliação da vulnerabilidade diretamente do Centro de Segurança.

1. A partir da barra lateral do Centro de Segurança, abra a página **recomendações** e selecione a recomendação **Vulnerabilidades nos seus servidores SQL em máquinas devem ser remediadas (Pré-visualização)**. Para mais informações, consulte [as Recomendações do Centro de Segurança.](security-center-recommendations.md) 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="As conclusões da Avaliação de Vulnerabilidade nos seus servidores SQL em máquinas devem ser remediadas (Pré-visualização)":::

    A opinião pormenorizada desta recomendação aparece.

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="Visão detalhada da recomendação":::

1. Para mais detalhes, desacaia:

    * Para uma visão geral dos recursos digitalizados (bases de dados) e da lista de verificações de segurança que foram testadas, selecione o servidor de interesse.

    * Para uma visão geral das vulnerabilidades agrupadas por uma base de dados SQL específica, selecione a base de dados de interesses.

    Em cada vista, os controlos de segurança são classificados pela **Severidade**. Clique numa verificação de segurança específica para ver um painel de detalhes com uma **Descrição**, como **remediar** e outras informações relacionadas, como **Impacto** ou **Benchmark**.

## <a name="azure-defender-for-sql-alerts"></a>Azure Defender para alertas SQL
Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar máquinas SQL. Estes eventos podem desencadear alertas mostrados na página de referência dos [alertas](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Explore e investigue alertas de segurança

Os alertas do Azure Defender para SQL estão disponíveis na página de alertas do Security Center, no separador de segurança do recurso, no [painel Azure Defender,](azure-defender-dashboard.md)ou através do link direto nos e-mails de alerta.

1. Para visualizar alertas, selecione **alertas** de segurança do menu do Security Center e selecione um alerta.

1. Os alertas são projetados para serem autossuficientes, com medidas de reparação detalhadas e informações de investigação em cada um deles. Você pode investigar mais adiante usando outras capacidades do Azure Security Center e Azure Sentinel para uma visão mais ampla:

    * Ativar o recurso de auditoria do SQL Server para mais investigações. Se você é um utilizador do Azure Sentinel, você pode carregar os registos de auditoria SQL dos eventos do Windows Security Log para Sentinel e desfrutar de uma experiência de investigação rica. [Saiba mais sobre a Auditoria do Servidor SQL.](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15)
    * Para melhorar a sua postura de segurança, utilize as recomendações do Centro de Segurança para a máquina de anfitrião indicada em cada alerta. Isto reduzirá os riscos de futuros ataques. 

    [Saiba mais sobre como gerir e responder a alertas.](security-center-managing-and-responding-alerts.md)


## <a name="next-steps"></a>Passos seguintes

Para material relacionado, consulte o seguinte artigo:

- [Alertas de segurança para A SQL Database e Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurar notificações por e-mail para alertas de segurança](security-center-provide-security-contact-details.md)
- [Saiba mais sobre a Azure Sentinel](../sentinel/index.yml)