---
title: A exportação contínua pode enviar alertas e recomendações do Azure Security Center para log analytics espaços de trabalho ou hubs de eventos Azure
description: Saiba como configurar a exportação contínua de alertas e recomendações de segurança para log analytics espaços de trabalho ou hubs de eventos Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: 59cfe7b990523e5cb165d1037291b3c1b1301624
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289240"
---
# <a name="continuously-export-security-center-data"></a>Exportar continuamente dados do Centro de Segurança

O Centro de Segurança Azure gera alertas e recomendações de segurança detalhadas. Pode vê-los no portal ou através de ferramentas programáticas. Também pode precisar de exportar algumas ou todas estas informações para rastrear com outras ferramentas de monitorização no seu ambiente. 

**A exportação contínua** permite-lhe personalizar totalmente *o que* será exportado e para *onde* irá. Por exemplo, pode configumentá-lo de modo a que:

- Todos os alertas de alta severidade são enviados para um Centro de Eventos Azure
- Todos os resultados de severidade média ou superior a partir de análises de vulnerabilidade dos seus servidores SQL são enviados para um espaço de trabalho específico do Log Analytics
- Recomendações específicas são entregues a um centro de eventos ou espaço de trabalho Log Analytics sempre que são geradas 

Este artigo descreve como configurar a exportação contínua para espaços de trabalho Log Analytics ou Azure Event Hubs.

> [!NOTE]
> Se precisar de integrar o Centro de Segurança com um SIEM, consulte [alertas stream para uma solução siem, soar ou gestão de serviços de TI.](export-to-siem.md)

> [!TIP]
> O Security Center também oferece a opção de realizar uma exportação manual única para o CSV. Saiba mais no [Manual de exportação única de alertas e recomendações.](#manual-one-time-export-of-alerts-and-recommendations)


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|<ul><li>**Administrador de segurança** ou **Proprietário** no grupo de recursos</li><li>Escreva permissões para o recurso alvo</li><li>Se estiver a utilizar as políticas de Azure 'DeployIfNotExist' descritas abaixo, também necessitará de permissões para atribuir políticas</li></ul>|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) US Gov, Outro Gov<br>![Yes](./media/icons/yes-icon.png) China Gov (para o Centro de Eventos)|
|||





## <a name="set-up-a-continuous-export"></a>Criar uma exportação contínua 

Pode configurar a exportação contínua a partir das páginas do Centro de Segurança no portal Azure, através do Centro de Segurança REST API, ou em escala utilizando os modelos de Política Azure fornecidos. Selecione o separador apropriado abaixo para obter detalhes de cada um.

### <a name="use-the-azure-portal"></a>[**Utilizar o portal do Azure**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Configure a exportação contínua das páginas do Centro de Segurança no portal Azure

Os passos abaixo são necessários quer esteja a configurar uma exportação contínua para o log analytics ou para o Azure Event Hubs.

1. A partir da barra lateral do Security Center, **selecione definições de preços &**.
1. Selecione a subscrição específica para a qual pretende configurar a exportação de dados.
1. A partir da barra lateral da página de definições para essa subscrição, selecione **Exportação Contínua**.
    [ ![ Opções de exportação no Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Aqui você vê as opções de exportação. Há um separador para cada alvo de exportação disponível. 
1. Selecione o tipo de dados que pretende exportar e escolha entre os filtros de cada tipo (por exemplo, exportar apenas alertas de alta gravidade).
1. Opcionalmente, se a sua seleção incluir uma destas quatro recomendações, pode incluir os resultados da avaliação da vulnerabilidade juntamente com eles:
    - Os resultados da Avaliação de Vulnerabilidades nas suas bases de dados SQL devem ser remediados
    - As conclusões da Avaliação de Vulnerabilidade nos seus servidores SQL em máquinas devem ser remediadas (Pré-visualização)
    - As vulnerabilidades nas imagens do Registo de Contentores de Azure devem ser remediadas (alimentadas por Qualys)
    - As vulnerabilidades nas suas máquinas virtuais devem ser remediadas

    Para incluir as conclusões com estas recomendações, permitir a opção **incluindo as conclusões de segurança.**

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir resultados de segurança alternando na configuração contínua da exportação" :::

1. A partir da área "Export target", escolha onde quer que os dados guardados. Os dados podem ser guardados num alvo numa subscrição diferente (por exemplo, numa instância Central do Centro de Eventos ou num espaço de trabalho central do Log Analytics).
1. Selecione **Guardar**.

### <a name="use-the-rest-api"></a>[**Utilizar a API REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Configure a exportação contínua utilizando a API REST

A exportação contínua pode ser configurada e gerida através do Azure Security Center [automas API](/rest/api/securitycenter/automations). Utilize esta API para criar ou atualizar regras para exportação para qualquer um dos seguintes destinos possíveis:

- Hub de Eventos do Azure
- Área de trabalho do Log Analytics
- Azure Logic Apps 

A API fornece funcionalidade adicional não disponível a partir do portal Azure, por exemplo:

* **Maior volume** - A API permite criar múltiplas configurações de exportação numa única subscrição. A página **de Exportação Contínua** no portal UI do Security Center suporta apenas uma configuração de exportação por subscrição.

* **Características adicionais** - A API oferece parâmetros adicionais que não são mostrados na UI. Por exemplo, pode adicionar tags ao seu recurso de automatização, bem como definir a sua exportação com base num conjunto mais alargado de propriedades de alerta e recomendação do que as oferecidas na página **de Exportação Contínua** no portal UI do Security Center.

* **Âmbito mais focado** - A API proporciona um nível mais granular para o âmbito das suas configurações de exportação. Ao definir uma exportação com a API, pode fazê-lo ao nível do grupo de recursos. Se estiver a utilizar a página **de Exportação Contínua** no portal UI do Security Center, tem de defini-la ao nível da subscrição.

    > [!TIP]
    > Se tiver configurado várias configurações de exportação utilizando a API, ou se tiver usado parâmetros apenas para API, essas funcionalidades extra não serão apresentadas na UI do Centro de Segurança. Em vez disso, haverá um banner informando-o de que existem outras configurações.

Saiba mais sobre as automatizações API na [documentação REST API](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Implementar em escala com a Política Azure**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Configure a exportação contínua à escala utilizando as políticas fornecidas

Automatizar os processos de monitorização e resposta a incidentes da sua organização pode melhorar consideravelmente o tempo necessário para investigar e mitigar incidentes de segurança.

Para implementar as suas configurações de exportação contínuas em toda a sua organização, utilize as políticas fornecidas da Azure Policy 'DeployIfNotExist' descritas abaixo para criar e configurar procedimentos de exportação contínua.

**Implementar estas políticas**

1. A partir da tabela abaixo, selecione a política que pretende aplicar:

    |Objetivo  |Política  |ID de política  |
    |---------|---------|---------|
    |Exportação contínua para centro de eventos|[Implementar a exportação no Hub de Eventos para alertas e recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Exportação contínua para log analytics espaço de trabalho|[Implementar a exportação na área de trabalho do Log Analytics para alertas e recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Também pode encontrá-las pesquisando a Política de Azure:
    > 1. Política aberta do Azure.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Aceder à Política de Azure":::
    > 2. A partir do menu Azure Policy, selecione **Definições** e procure-as pelo nome. 

1. Na página política Azure relevante, selecione **Atribuir**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Atribuição da Política Azure":::

1. Abra cada separador e desabrohe os parâmetros conforme desejado:
    1. No separador **Básico,** estabeleça a margem de manobra para a apólice. Para utilizar a gestão centralizada, atribua a política ao Grupo de Gestão que contenha as subscrições que utilizarão a configuração contínua da exportação. 
    1. No **separador Parâmetros,** desenhe o grupo de recursos e os detalhes do tipo de dados. 
        > [!TIP]
        > Cada parâmetro tem uma ponta de ferramenta explicando as opções disponíveis para si.
        >
        > O separador de parâmetros da Azure Policy (1) fornece acesso a opções de configuração semelhantes às da página de exportação contínua do Security Center (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Comparar os parâmetros em exportação contínua com a Política Azure" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Opcionalmente, para aplicar esta atribuição às subscrições existentes, abrir o separador **Remediação** e selecionar a opção de criar uma tarefa de remediação.
1. Reveja a página de resumo e **selecione Criar**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informação sobre exportação para um espaço de trabalho Log Analytics

Se quiser analisar os dados do Azure Security Center dentro de um espaço de trabalho do Log Analytics ou utilizar alertas Azure juntamente com alertas do Security Center, confmie uma exportação contínua para o seu espaço de trabalho Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Registar tabelas e esquemas de Analítica

Os alertas e recomendações de segurança são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendations,* respectivamente. 

O nome da solução Log Analytics que contém estas tabelas depende se tem o Azure Defender ativado: Segurança ('Segurança e Auditoria') ou SecurityCenterFree. 

> [!TIP]
> Para ver os dados sobre o espaço de trabalho de destino, deve ativar uma destas soluções **Segurança e Auditoria** ou **SecurityCenterFree**.

![A tabela *SecurityAlert* em Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas](https://aka.ms/ASCAutomationSchemas)de tabela Log Analytics .


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Ver alertas e recomendações exportadas no Azure Monitor

Em alguns casos, pode optar por visualizar os alertas de segurança exportados e/ou recomendações no [Azure Monitor](../azure-monitor/platform/alerts-overview.md). 

O Azure Monitor fornece uma experiência de alerta unificada para uma variedade de alertas Azure, incluindo Log de Diagnóstico, alertas métricos e alertas personalizados baseados em consultas de espaço de trabalho Log Analytics.

Para visualizar alertas e recomendações do Security Center no Azure Monitor, configure uma regra de alerta baseada em consultas de Log Analytics (Alerta de Registo):

1. Na página **alertas** do Monitor Azure, selecione **Nova regra de alerta**.

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página de regras de criar, configuure a sua nova regra (da mesma forma que configuraria uma regra de [alerta de registo no Azure Monitor):](../azure-monitor/platform/alerts-unified-log.md)

    * Para **obter recursos,** selecione o espaço de trabalho Log Analytics para o qual exportou alertas e recomendações de segurança.

    * Para **obter a condição** , selecione Custom log **search**. Na página que aparece, configurar o período de consulta, de resguardo e de frequência. Na consulta de pesquisa, pode escrever *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados que o Security Center exporta continuamente para que permita a exportação Contínua para log analytics. 
    
    * Opcionalmente, configuure o [Grupo de Ação](../azure-monitor/platform/action-groups.md) que gostaria de desencadear. Os grupos de ação podem desencadear o envio de e-mails, bilhetes ITSM, WebHooks e muito mais.
    ![Regra de alerta do Monitor Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verá agora novos alertas ou recomendações do Azure Security Center (dependendo das regras de exportação contínua configuradas e da condição definida na sua regra de alerta do Monitor Azure) nos alertas do Azure Monitor, com o desencadeamento automático de um grupo de ação (se fornecido).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Exportação manual de alertas e recomendações

Para fazer o download de um relatório CSV para alertas ou recomendações, abra a página **alertas** de Segurança ou **Recomendações** e selecione o botão **de relatório de CSV de descarregamento.**

[![Descarregue dados de alerta como um ficheiro CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Estes relatórios contêm alertas e recomendações para recursos das subscrições atualmente selecionadas.


## <a name="faq---continuous-export"></a>FAQ - Exportação contínua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quais são os custos envolvidos na exportação de dados?

Não há qualquer custo para ativar a exportação contínua. Os custos podem ser incorridos para a ingestão e retenção de dados no seu espaço de trabalho Log Analytics, dependendo da sua configuração lá. 

Saiba mais sobre [os preços do espaço de trabalho Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Saiba mais sobre [os preços do Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>A exportação inclui dados sobre o estado atual de todos os recursos?

Não. A exportação contínua é construída para o streaming de **eventos:**

- **Os alertas recebidos** antes de permitir a exportação não serão exportados.
- **As recomendações** são enviadas sempre que o estado de conformidade de um recurso muda. Por exemplo, quando um recurso passa de saudável para insalubre. Portanto, tal como nos alertas, as recomendações para recursos que não mudaram de estado, uma vez que permitiu exportar, não serão exportadas.


### <a name="why-are-recommendations-sent-at-different-intervals"></a>Por que motivo é que as recomendações são enviadas em intervalos diferentes?

Diferentes recomendações têm diferentes intervalos de avaliação de conformidade, que podem variar de alguns minutos a cada poucos dias. Consequentemente, as recomendações diferirão no tempo que demora a aparecer nas suas exportações.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>A exportação contínua suporta qualquer cenário de continuidade de negócios ou recuperação de desastres (BCDR) ?

Ao preparar o seu ambiente para cenários BCDR, onde o recurso-alvo está a sofrer uma paragem ou outro desastre, é da responsabilidade da organização prevenir a perda de dados, estabelecendo backups de acordo com as diretrizes dos Azure Event Hubs, Log Analytics e Logic App.

Saiba mais em [Azure Event Hubs - Recuperação de geo-desastres.](../event-hubs/event-hubs-geo-dr.md)


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>A exportação contínua está disponível gratuitamente com o Azure Security Center?

Sim! Note que muitos alertas do Centro de Segurança só são fornecidos quando você ativou o Azure Defender. Uma boa forma de visualizar os alertas que vai receber nos seus dados exportados é ver os alertas mostrados nas páginas do Security Center no portal Azure.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar as exportações contínuas das suas recomendações e alertas. Também aprendeu a descarregar os seus dados de alerta como um ficheiro CSV. 

Para obter material relacionado, consulte a seguinte documentação: 

- Saiba mais sobre [os modelos de automatização do fluxo de trabalho.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)
- [Documentação dos Hubs de Eventos do Azure](../event-hubs/index.yml)
- [Documentação do Azure Sentinel](../sentinel/index.yml)
- [Documentação do Azure Monitor](../azure-monitor/index.yml)
- [Esquemas de tipos de dados de exportação](https://aka.ms/ASCAutomationSchemas)