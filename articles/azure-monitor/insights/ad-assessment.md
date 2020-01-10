---
title: Otimize seu ambiente de Active Directory com Azure Monitor | Microsoft Docs
description: Você pode usar a solução de verificação de integridade Active Directory para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: f0de484d58085f598988589d18495c9a6fe1b374
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406138"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Otimize seu ambiente de Active Directory com a solução de verificação de integridade Active Directory no Azure Monitor

![Símbolo de verificação de integridade do AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Você pode usar a solução de verificação de integridade Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo ajuda você a instalar e usar a solução para que você possa tomar ações corretivas para possíveis problemas.

Essa solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco, que ajudam você a entender rapidamente o risco e tomar medidas.

As recomendações são baseadas no conhecimento e na experiência obtidas pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientação sobre por que um problema pode ser importante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso em relação à execução de um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma verificação for concluída, as informações resumidas para áreas de foco serão mostradas no painel de **verificação de integridade do AD** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel de **verificação de integridade do AD** , no qual você pode exibir e, em seguida, executar as ações recomendadas para sua infraestrutura do Active Directory Server.  

![imagem do bloco verificação de integridade do AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do painel de verificação de integridade do AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de integridade Active Directory requer uma versão com suporte do .NET Framework 4.6.2 ou superior instalada em cada computador que tenha o agente de Log Analytics para Windows (também conhecido como Microsoft Monitoring Agent (MMA)) instalado.  O agente é usado pelo System Center 2016-Operations Manager, Operations Manager 2012 R2 e Azure Monitor.
* A solução dá suporte a controladores de domínio que executam o Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2 e Windows Server 2016.
* Um espaço de trabalho Log Analytics para adicionar a solução de verificação de integridade Active Directory do Azure Marketplace no portal do Azure. Não há nenhuma configuração adicional necessária.

  > [!NOTE]
  > Depois de adicionar a solução, o arquivo AdvisorAssessment. exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e enviados para Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para executar a verificação de integridade em seus controladores de domínio que são membros do domínio a ser avaliado, cada controlador de domínio nesse domínio requer um agente e conectividade para Azure Monitor usando um dos seguintes métodos com suporte:

1. Instale o [agente do log Analytics para Windows](../../azure-monitor/platform/agent-windows.md) se o controlador de domínio ainda não estiver monitorado pelo System Center 2016-Operations Manager ou Operations Manager 2012 R2.
2. Se ele for monitorado com o System Center 2016-Operations Manager ou Operations Manager 2012 R2 e o grupo de gerenciamento não estiver integrado com o Azure Monitor, o controlador de domínio poderá ser multihomed com Azure Monitor para coletar dados e encaminhar para o serviço e ainda ser monitorado pelo Operations Manager.  
3. Caso contrário, se o grupo de gerenciamento de Operations Manager estiver integrado ao serviço, você precisará adicionar os controladores de domínio para coleta de dados pelo serviço seguindo as etapas em [Adicionar computadores gerenciados por agente](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de habilitar a solução em seu espaço de trabalho.  

O agente no controlador de domínio que relata para um grupo de gerenciamento de Operations Manager, coleta dados, encaminha para seu servidor de gerenciamento atribuído e é enviado diretamente de um servidor de gerenciamento para Azure Monitor.  Os dados não são gravados nos bancos de dado do Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Detalhes da coleta de dados de verificação de integridade Active Directory

Active Directory verificação de integridade coleta dados das seguintes fontes usando o agente que você habilitou:

- Registo
- LDAP
- .NET Framework
- Registo de eventos
- ADSI (interfaces de serviço Active Directory)
- Windows PowerShell
- Dados de ficheiros
- Windows Management Instrumentation (WMI)
- API da ferramenta DCDIAG
- API do serviço de replicação de arquivo (NTFRS)
- Código C# personalizado

Os dados são coletados no controlador de domínio e encaminhados para Azure Monitor a cada sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Entendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de peso que identifica a importância relativa da recomendação. Somente as 10 recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados

Os pesos são valores agregados com base em três fatores-chave:

* A *probabilidade* de que um problema identificado cause problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
* O *impacto* do problema na sua organização se isso causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais alto é igual a uma pontuação geral menor para a recomendação.

O peso de cada recomendação é expresso como um percentual da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará a pontuação geral de segurança e conformidade em 5%.

### <a name="focus-areas"></a>Áreas de foco

**Segurança e conformidade** – essa área de foco mostra recomendações para possíveis ameaças à segurança e violações, políticas corporativas e requisitos de conformidade técnica, legal e regulatório.

**Disponibilidade e continuidade dos negócios** – essa área de foco mostra recomendações para disponibilidade de serviço, resiliência de sua infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de ti de sua organização, garantir que seu ambiente de ti atenda aos requisitos de desempenho atuais e que seja capaz de responder às necessidades de infraestrutura em constante mudança.

**Atualização, migração e implantação** – essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar Active Directory em sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar a pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações são baseadas no conhecimento e nas experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não há duas infraestruturas de servidor iguais, e recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Os problemas que são importantes para um negócio maduro podem ser menos importantes para uma inicialização. Talvez você queira identificar quais áreas de foco são suas prioridades e, em seguida, examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre por que é importante. Você deve usar essas diretrizes para avaliar se a implementação da recomendação é apropriada para você, considerando a natureza dos seus serviços de ti e as necessidades comerciais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar recomendações da área de foco de verificação de integridade

Após a instalação, você pode exibir o resumo das recomendações usando o bloco verificação de integridade na página solução no portal do Azure.

Exiba as avaliações de conformidade resumidas para sua infraestrutura e, em seguida, faça drill-in nas recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na página **visão geral** , clique no bloco **verificação de integridade Active Directory** .

2. Na página **verificação de integridade** , examine as informações de resumo em uma das folhas da área de foco e clique em uma para exibir as recomendações para essa área de foco.

3. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas feitas para seu ambiente. Clique em uma recomendação em **objetos afetados** para exibir detalhes sobre por que a recomendação é feita.

    ![imagem das recomendações de verificação de integridade](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Você pode tomar ações corretivas sugeridas em **ações sugeridas**. Quando o item foi resolvido, as avaliações posteriores registram que as ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigidos aparecem como **objetos passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se você tiver recomendações que deseja ignorar, poderá criar um arquivo de texto que Azure Monitor será usado para impedir que as recomendações apareçam nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que serão ignoradas

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Use a consulta a seguir para listar as recomendações que falharam para computadores em seu ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Aqui está uma captura de tela mostrando a consulta de log: <

![recomendações com falha](media/ad-assessment/ad-failed-recommendations.png)

Escolha as recomendações que você deseja ignorar. Você usará os valores para a Recomendaçãoid no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e usar um arquivo de texto IgnoreRecommendations. txt

1. Crie um arquivo chamado IgnoreRecommendations. txt.

2. Cole ou digite cada Recomendaçãoid para cada recomendação que você deseja que Azure Monitor ignore em uma linha separada e, em seguida, salve e feche o arquivo.

3. Coloque o arquivo na pasta a seguir em cada computador em que você deseja que Azure Monitor ignore as recomendações.

   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio de Operations Manager)- *systemdrive*: \Program Files\Microsoft Monitoring Agent\Agent
   * No Operations Manager 2012 R2 Management Server- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na Operations Manager servidor de gerenciamento 2016- *systemdrive*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

Após a execução da próxima verificação de integridade agendada, por padrão a cada sete dias, as recomendações especificadas são marcadas como *ignoradas* e não aparecerão no painel.

1. Você pode usar as consultas de log a seguir para listar todas as recomendações ignoradas.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations. txt ou remova o as recommendationids deles.

## <a name="ad-health-check-solutions-faq"></a>Perguntas frequentes sobre soluções de verificação de integridade do AD

*Com que frequência uma verificação de integridade é executada?*

* A verificação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a verificação de integridade é executada?*

* Neste momento, não.

*Se outro servidor for descoberto após ter adicionado uma solução de verificação de integridade, ele será verificado*

* Sim, depois de ser descoberto, ele é verificado a partir de então, a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da verificação de integridade?*

* Se um servidor não enviar dados por 3 semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*

* AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*

* A coleta de dados real no servidor leva cerca de 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores de Active Directory.

*Há uma maneira de configurar quando os dados são coletados?*

* Neste momento, não.

*Por que exibir apenas as 10 principais recomendações?*

* Em vez de fornecer uma lista exaustiva de tarefas, recomendamos que você se concentre primeiro em abordar as recomendações priorizadas. Depois de solucioná-los, recomendações adicionais ficarão disponíveis. Se você preferir ver a lista detalhada, poderá exibir todas as recomendações usando uma consulta de log.

*Há uma maneira de ignorar uma recomendação?*

* Sim, consulte a seção [ignorar recomendações](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes

Use [Azure monitor consultas de log](../log-query/log-query-overview.md) para saber como analisar dados e recomendações de verificação de integridade do AD detalhados.
