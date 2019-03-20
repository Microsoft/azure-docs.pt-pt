---
title: Otimizar seu ambiente do Active Directory com o Azure Monitor | Documentos da Microsoft
description: Pode utilizar a solução de verificação de estado de funcionamento do Active Directory para avaliar o risco e estado de funcionamento dos seus ambientes num intervalo regular.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 949bf52a586603fc6a82c0da553cf70877a8f4be
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085019"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Otimizar seu ambiente do Active Directory com a solução de verificação de estado de funcionamento do Active Directory no Azure Monitor

![Símbolo de verificar o estado de funcionamento do AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode utilizar a solução de verificação de estado de funcionamento do Active Directory para avaliar o risco e estado de funcionamento dos seus ambientes de servidor num intervalo regular. Este artigo ajuda-o a instalar e utilizar a solução para que pode tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para sua infra-estrutura de servidor implementado. As recomendações são categorizadas em quatro áreas de foco, ajudá-lo a rapidamente entender o risco e tomar medidas.

As recomendações baseiam-se o conhecimento e a experiência obtida pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientações sobre por que um problema poderá são importantes para si e como implementar as alterações sugeridas.

Pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar seu progresso em relação a execução de um ambiente gratuito e em bom estado de risco.

Depois de adicionar a solução e é uma verificação de conclusão, resumo informações para áreas de foco são mostradas no **verificação de estado de funcionamento do AD** dashboard para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações sobre o **verificação de estado de funcionamento do AD** dashboard, onde pode ver e, em seguida, efetuar as ações recomendadas para a sua infraestrutura de servidor do Active Directory.  

![imagem do mosaico de verificação de estado de funcionamento do AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do dashboard de verificação de estado de funcionamento do AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de estado de funcionamento do Active Directory requer uma versão suportada do .NET Framework 4.5.2 ou superior instalado em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Operations Manager e o Operations Manager 2012 R2 e o Azure Monitor.
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2 e Windows Server 2016.
* Uma área de trabalho do Log Analytics para adicionar a solução de verificação de estado de funcionamento do Active Directory no Azure marketplace no portal do Azure.  Não há nenhuma configuração adicional.

  > [!NOTE]
  > Depois de adicionar a solução, o ficheiro de AdvisorAssessment.exe é adicionado aos servidores com os agentes. Leia e, em seguida, enviados para o Azure Monitor na cloud para processamento de dados de configuração. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados.
  >
  >

Para efetuar a verificação de estado de funcionamento contra os controladores de domínio que são membros do domínio a ser avaliada, necessitam de um agente e a conectividade para o Azure Monitor utilizando um dos seguintes métodos suportados:

1. Instalar o [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) se o controlador de domínio já não é monitorizado pelo System Center 2016 - Operations Manager ou do Operations Manager 2012 R2.
2. Se está a ser monitorizado com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2 e o grupo de gestão não está integrado com o Azure Monitor, o controlador de domínio pode ser alojada em vários com o Azure Monitor para recolher dados e reencaminhar para o serviço e ainda monitorizados pelo Operations Manager.  
3. Caso contrário, se o grupo de gestão do Operations Manager está integrado com o serviço, terá de adicionar os controladores de domínio para a recolha de dados pelo serviço de seguir os passos em [adicionar computadores geridos por agente](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-log-analytics) depois de ativar a solução na sua área de trabalho.  

O agente no seu controlador de domínio, os relatórios a um grupo de gestão do Operations Manager, recolhe os dados, por sua vez encaminha para o respetivo servidor de gestão atribuído e, em seguida, é enviado diretamente a partir de um servidor de gestão para o Azure Monitor.  Os dados não são gravados para as bases de dados do Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory detalhes de recolha dados a verificação do Estado de funcionamento

Verificação de estado de funcionamento do Active Directory recolhe dados das seguintes origens a utilizar o agente que tiver ativado:

- Registo
- LDAP
- .NET Framework
- Registo de eventos
- Interfaces de serviço do Active Directory (ADSI)
- Windows PowerShell
- Dados de ficheiros
- Windows Management Instrumentation (WMI)
- Ferramenta DCDIAG API
- API de serviço (NTFRS) de replicação de ficheiros
- Código personalizado em C#

Dados são recolhidos no controlador de domínio e reencaminhados para o Azure Monitor, a cada sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são priorizadas
Cada recomendação feita é fornecida um valor de peso que identifica a importância relativa da recomendação. As 10 recomendações mais importantes são apresentadas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados
Pesos são valores agregados com base em três fatores principais:

* O *probabilidade* que um problema identificado causa problemas. Uma probabilidade maior equivale a uma maior pontuação geral para a recomendação.
* O *impacto* do problema na sua organização se ele faz com que um problema. Um impacto maior equivale a uma maior pontuação geral para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço maior equivale a uma menor pontuação geral para a recomendação.

O peso de cada recomendação é expressa como uma percentagem da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tem uma pontuação igual a 5%, implementar essa recomendação aumenta sua % de pontuação por 5 geral da segurança e conformidade.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** -esta área de foco mostra recomendações para potenciais ameaças de segurança e falhas, as políticas empresariais e os requisitos de conformidade técnicos, jurídicos e regulamentares.

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para ajudar a sua organização infraestrutura de TI aumentam, certifique-se de que o seu ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder para alterar a infraestrutura necessidades.

**Atualizar, migração e a implantação** -esta área de foco mostra recomendações para o ajudar a atualizar, migrar e implantar o Active Directory à sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar à pontuação 100% em todas as áreas de foco?
Não necessariamente. As recomendações baseiam-se o conhecimento e experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não infra-estruturas de dois servidor são os mesmos e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes, se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem o relatório e a coleta de dados ad hoc de baixa prioridade. Problemas que são importantes para uma empresa madura e podem ser menos importantes para uma inicialização. Pode querer identificar quais áreas de foco são suas prioridades e, em seguida, examinar como as suas pontuações alteram ao longo do tempo.

Cada recomendação inclui documentação de orientação sobre por que é importante. Deve usar essa orientação para avaliar se implementar a recomendação é adequada para si, tendo em conta a natureza de seus serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Recomendações de área de foco de verificação de integridade de utilização
Depois de ser instalado, pode ver o resumo de recomendações utilizando o mosaico estado de funcionamento Verifique na página da solução no portal do Azure.

Ver as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, no teste de recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Sobre o **descrição geral** página, clique no **verificação de estado de funcionamento do Active Directory** mosaico.
1. Sobre o **verificar o estado de funcionamento** página, reveja as informações de resumo em um dos painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
1. Em qualquer uma das páginas de área de foco, pode ver as fez para o seu ambiente de recomendações ordenadas por prioridade. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre por que motivo a recomendação é feita.<br><br> ![imagem de recomendações de verificar o estado de funcionamento](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
1. Pode efetuar ações corretivas sugeridas na **ações sugeridas**. Quando o item tiver sido resolvido, registos de avaliações posteriores que as ações recomendadas foram executados e irão aumentar a sua pontuação de conformidade. Itens corrigidos aparecem como **objetos transmitidos**.

## <a name="ignore-recommendations"></a>Ignorar recomendações
Se tiver recomendações que deseja ignorar, pode criar um arquivo de texto que o Azure Monitor irá utilizar para impedir que as recomendações de aparecer no seu os resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que irá ignorar
[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Utilize a seguinte consulta para recomendações de lista que tenham falhado para computadores no seu ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Eis uma captura de ecrã que mostra a consulta de registo:<br><br> ![recomendações com falhas](media/ad-assessment/ad-failed-recommendations.png)

Escolha as recomendações que pretende ignorar. Vai utilizar os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Cole ou introduza cada RecommendationId para cada recomendação que pretende que o Azure Monitor para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloca o ficheiro na pasta em cada computador onde pretende que o Azure Monitor para ignorar recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou através do Operations Manager) - *SystemDrive*: \Programas\Microsoft Agent\Agent de monitorização
   * No servidor de gestão do Operations Manager 2012 R2 - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Operations Manager 2016 - *SystemDrive*: \Programas\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que as recomendações são ignoradas
Após a próxima agendada execuções de verificação de estado de funcionamento, por predefinição a cada sete dias, as recomendações especificadas são marcadas *ignorada* e não serão apresentados no dashboard.

1. Pode utilizar as seguintes consultas de registo para listar todas as recomendações ignoradas.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se decidir posteriormente que pretende ver recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="ad-health-check-solutions-faq"></a>FAQ de soluções de verificação do Estado de funcionamento do AD
*A frequência com que é executada uma verificação de estado de funcionamento?*

* A verificação é executada a cada sete dias.

*Existe uma forma de configurar a frequência com que a verificação de estado de funcionamento é executado?*

* Neste momento, não.

*Se outro servidor para é detetado depois adicionei uma solução de verificação de estado de funcionamento, irá ser verificado*

* Sim, assim que o descobrir que seu check-in de, em seguida, no, a cada sete dias.

*Se um servidor está desativado, quando irá este ser removido da verificação de estado de funcionamento?*

* Se um servidor não enviar dados para três semanas, este é removido.

*O que é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora para recolher dados?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais tempo nos servidores que têm um grande número de servidores do Active Directory.

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Por que motivo exibir apenas as recomendações de 10 principais?*

* Em vez de apresentar uma lista exaustiva esmagadora de tarefas, recomendamos que se concentre em endereçamento de recomendações ordenadas por prioridade em primeiro lugar. Depois de corrigi-los, recomendações adicionais estarão disponíveis. Se preferir ver a lista detalhada, pode ver todas as recomendações usando uma consulta de registo.

*Existe uma forma de ignorar uma recomendação?*

* Sim, consulte [ignorar recomendações](#ignore-recommendations) secção acima.

## <a name="next-steps"></a>Passos Seguintes
* Uso [consultas de registo do Azure Monitor](../log-query/log-query-overview.md) para saber como analisar dados de verificação de estado de funcionamento do AD e recomendações detalhadas.
