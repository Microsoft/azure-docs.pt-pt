---
title: Otimize o seu ambiente de Diretório Ativo com o Monitor Azure [ Monitor] Microsoft Docs
description: Pode utilizar a solução Ative Directory Health Check para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 06c8949be681d13b9dc7d5c433197dd9371aeef8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651852"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimize your Active Directory environment with the Active Directory Health Check solution in Azure Monitor (Otimizar o ambiente do Active Directory com a solução Verificação de Estado de Funcionamento do Active Directory no Azure Monitor)

![Símbolo de verificação de saúde ad](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode utilizar a solução Ative Directory Health Check para avaliar o risco e a saúde dos ambientes do seu servidor num intervalo regular. Este artigo ajuda-o a instalar e a utilizar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidores implementadas. As recomendações são categorizadas em quatro áreas de foco, que o ajudam a entender rapidamente o risco e a tomar medidas.

As recomendações baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas de clientes. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de concluído um cheque, são mostradas informações sumárias para áreas de foco no painel de verificação de **saúde da AD** para a infraestrutura no seu ambiente. As seguintes secções descrevem como usar as informações no painel de verificação de saúde da **AD,** onde pode ver e, em seguida, tomar medidas recomendadas para a sua infraestrutura de servidor de Diretório Ativo.  

![imagem do azulejo AD Health Check](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do painel de verificação de saúde da AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução Ative Directory Health Check requer uma versão suportada do .NET Framework 4.6.2 ou superior instalado em cada computador que tenha o agente Log Analytics para windows (também designado por Agente de Monitorização da Microsoft (MMA)) instalado.  O agente é utilizado pelo System Center 2016 - Diretor de Operações, Diretor de Operações 2012 R2 e Monitor Azure.
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, o Windows Server 2012 e o 2012 R2, o Windows Server 2016 e o Windows Server 2019.
* Um espaço de trabalho de Log Analytics para adicionar a solução Ative Directory Health Check do Azure Marketplace no portal Azure. Não é necessária uma configuração adicional.

  > [!NOTE]
  > Depois de ter adicionado a solução, o ficheiro AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e depois enviados para o Monitor Azure na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para efetuar a verificação de saúde contra os seus controladores de domínio que são membros do domínio a avaliar, cada controlador de domínio nesse domínio requer um agente e conectividade ao Monitor Azure utilizando um dos seguintes métodos suportados:

1. Instale o [agente Log Analytics para Windows](../../azure-monitor/platform/agent-windows.md) se o controlador de domínio ainda não estiver monitorizado pelo System Center 2016 - Diretor de Operações ou Gestor de Operações 2012 R2.
2. Se for monitorizado com o System Center 2016 - Diretor de Operações ou Diretor de Operações 2012 R2 e o grupo de gestão não estiver integrado no Monitor Azure, o controlador de domínio pode ser multi-alojado com o Azure Monitor para recolher dados e encaminhar-se para o serviço e ainda ser monitorizado pelo Gestor de Operações.  
3. Caso contrário, se o seu grupo de gestão do Gestor de Operações estiver integrado ao serviço, terá de adicionar os controladores de domínio para recolha de dados pelo serviço, seguindo os passos em [computadores geridos por agentes adicionais](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de ativar a solução no seu espaço de trabalho.  

O agente no seu controlador de domínio que reporta a um grupo de gestão do Gestor de Operações, recolhe dados, reencaminha para o seu servidor de gestão atribuído, e depois é enviado diretamente de um servidor de gestão para o Monitor De operações.  Os dados não estão escritos nas bases de dados do Gestor de Operações.  

## <a name="active-directory-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do Active Directory

Ative Directory Health Check recolhe dados das seguintes fontes utilizando o agente que ativou:

- Registo
- LDAP
- .NET Framework
- Registo de eventos
- Interfaces de Serviço de Diretório Ativo (ADSI)
- Windows PowerShell
- Dados de ficheiros
- Windows Management Instrumentation (WMI)
- Ferramenta DCDIAG API
- Serviço de Replicação de Ficheiros (NTFRS) API
- Código C# personalizado

Os dados são recolhidos no controlador de domínio e encaminhados para o Monitor Azure de sete em sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender como é definida a prioridade das recomendações

Todas as recomendações feitas recebem um valor de ponderação que identifica a importância relativa da recomendação. Apenas são apresentadas as 10 recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Método de cálculo das ponderações

As ponderações são valores agregados com base em três factores-chave:

* A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
* O *impacto* do problema na sua organização se causar um problema. Um impacto maior equivale a uma pontuação global maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação para cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Segurança e Conformidade tiver uma pontuação de 5%, implementando essa recomendação aumenta a sua pontuação global de Segurança e Conformidade em 5%.

### <a name="focus-areas"></a>Áreas em foco

**Segurança e Conformidade** - Esta área de foco apresenta recomendações para potenciais ameaças e violações de segurança, políticas corporativas e requisitos técnicos, legais e regulamentares de conformidade.

**Disponibilidade e Continuidade de Negócios** - Esta área de foco mostra recomendações para disponibilidade de serviço, resiliência da sua infraestrutura e proteção empresarial.

**Desempenho e Escalabilidade** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI satisfaz os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implantação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar O Diretório Ativo para a sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?

Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas de clientes. No entanto, nenhuma infraestrutura de servidores é a mesma, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem recolha e reporte de dados ad hoc de baixa prioridade. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Você pode querer identificar quais as áreas de foco são as suas prioridades e, em seguida, olhar para como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Deve utilizar esta orientação para avaliar se a implementação da recomendação é adequada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Use recomendações de área de foco de verificação de saúde

Depois de instalado, pode ver o resumo das recomendações utilizando o azulejo Health Check na página de solução no portal Azure.

Consulte as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Ver recomendações para uma área de foco e tomar medidas corretivas

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na página **'Overview',** clique no azulejo **Ative Directy Health Check.**

2. Na página **Health Check,** reveja as informações sumárias numa das lâminas da área de foco e clique numa para ver recomendações para essa área de foco.

3. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.

    ![imagem das recomendações do Health Check](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido abordado, avalia ções posteriores que as ações recomendadas foram tomadas e a sua pontuação de conformidade aumentará. Itens corrigidos aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que deseja ignorar, pode criar um ficheiro de texto que o Azure Monitor utilizará para evitar que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações que irá ignorar

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Aqui está uma imagem mostrando a consulta de registo:<

![recomendações falhadas](media/ad-assessment/ad-failed-recommendations.png)

Escolha recomendações que queira ignorar. Utilizará os valores para Recomendação id no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro chamado Ignorrecommendations.txt.

2. Colar ou digitar cada Recomendação Id para cada recomendação que pretende que o Monitor Azure ignore numa linha separada e, em seguida, guarde e feche o ficheiro.

3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Monitor Azure ignore as recomendações.

   * Em computadores com o Microsoft Monitoring Agent (ligado diretamente ou através do Gestor de Operações) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * No servidor de gestão R2 Do Gestor de Operações 2012 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Gestor de Operações 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

Após o próximo exame de saúde programado, por padrão de sete em sete dias, as recomendações especificadas são marcadas *ignoradas* e não aparecerão no painel de instrumentos.

1. Pode utilizar as seguintes consultas de registo para listar todas as recomendações ignoradas.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer ficheiros IgnoreRecommendations.txt ou pode remover recomendações das mesmas.

## <a name="ad-health-check-solutions-faq"></a>FAQ sobre soluções de Verificação de Estado de Funcionamento do AD

*Que controlos são realizados pela solução de Avaliação de AD?*

* A seguinte consulta mostra uma descrição de todos os controlos atualmente realizados:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem então ser exportados para o Excel para posterior revisão.

*Com que frequência funciona um exame de saúde?*

* O cheque é de sete em sete dias.

*Existe uma maneira de configurar a frequência com que o exame de saúde funciona?*

* Neste momento, não.

*Se outro servidor for descoberto depois de eu adicionar uma solução de verificação de saúde, será verificado*

* Sim, uma vez descoberto que é verificado a partir daí, a cada sete dias.

*Se um servidor for desativado, quando será removido do exame de saúde?*

* Se um servidor não submeter dados durante 3 semanas, é removido.

*Qual é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora a recolher dados?*

* A recolha real de dados no servidor demora cerca de 1 hora. Pode demorar mais tempo em servidores que tenham um grande número de servidores de Diretório Ativo.

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Por que exibir apenas as 10 melhores recomendações?*

* Em vez de lhe dar uma lista exaustiva e esmagadora de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações usando uma consulta de log.

*Há alguma maneira de ignorar uma recomendação?*

* Sim, consulte a secção de [recomendações de ignorar](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes

Utilize consultas de [registo do Azure Monitor](../log-query/log-query-overview.md) para aprender a analisar dados e recomendações detalhadas do AD Health Check.
