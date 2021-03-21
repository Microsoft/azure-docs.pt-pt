---
title: Otimize o seu ambiente de Diretório Ativo com | do Azure Monitor Microsoft Docs
description: Pode utilizar a solução Ative Directory Health Check para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723253"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimize your Active Directory environment with the Active Directory Health Check solution in Azure Monitor (Otimizar o ambiente do Active Directory com a solução Verificação de Estado de Funcionamento do Active Directory no Azure Monitor)

![Símbolo de verificação de saúde ad](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode utilizar a solução Ative Directory Health Check para avaliar o risco e a saúde dos ambientes do seu servidor num intervalo regular. Este artigo ajuda-o a instalar e a utilizar a solução para que possa tomar ações corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas à infraestrutura de servidores implementadas. As recomendações são categorizadas em quatro áreas de foco, que o ajudam a entender rapidamente o risco e a tomar medidas.

As recomendações baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas ao cliente. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de ter sido concluída uma verificação, são apresentadas informações sumárias para áreas de foco no painel de controlo de saúde da **AD** para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações no painel de controlo de saúde da **AD,** onde pode ver e, em seguida, tomar as ações recomendadas para a sua infraestrutura de servidor de Diretório Ativo.  

![imagem de azulejo de Verificação de Saúde AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do painel de verificação de saúde AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução Ative Directory Health Check requer uma versão suportada do Quadro .NET 4.6.2 ou superior instalado em cada computador que tenha o agente Log Analytics para windows (também designado por Agente de Monitorização da Microsoft (MMA)) instalado.  O agente é utilizado pelo System Center 2016 - Gestor de Operações, Gestor de Operações 2012 R2 e Monitor Azure.
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2, Windows Server 2016 e Windows Server 2019.
* Um espaço de trabalho Log Analytics para adicionar a solução Ative Directory Health Check do Azure Marketplace no portal Azure. Não é necessária nenhuma configuração adicional.

  > [!NOTE]
  > Depois de ter adicionado a solução, o ficheiro AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e depois enviados para o Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para efetuar a verificação de saúde contra os seus controladores de domínio que são membros do domínio a avaliar, cada controlador de domínio nesse domínio requer um agente e conectividade ao Azure Monitor utilizando um dos seguintes métodos suportados:

1. Instale o [agente Log Analytics para](../agents/agent-windows.md) o Windows se o controlador de domínio ainda não estiver monitorizado pelo System Center 2016 - Gestor de Operações ou Gestor de Operações 2012 R2.
2. Se for monitorizado com o System Center 2016 - Gestor de Operações ou Gestor de Operações 2012 R2 e o grupo de gestão não estiver integrado com o Azure Monitor, o controlador de domínio pode ser multi-acotado com o Azure Monitor para recolher dados e encaminhar para o serviço e ainda ser monitorizado pelo Gestor de Operações.  
3. Caso contrário, se o seu grupo de gestão de Gestor de Operações estiver integrado no serviço, tem de adicionar os controladores de domínio para recolha de dados pelo serviço seguindo os passos em [adicionar computadores geridos por agentes](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de ativar a solução no seu espaço de trabalho.  

O agente do seu controlador de domínio que reporta a um grupo de gestão de Gestores de Operações, recolhe dados, encaminha para o seu servidor de gestão atribuído e, em seguida, é enviado diretamente de um servidor de gestão para o Azure Monitor.  Os dados não são escritos nas bases de dados do Gestor de Operações.  

## <a name="active-directory-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do Active Directory

O Ative Directory Health Check recolhe dados das seguintes fontes utilizando o agente que ativou:

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

Os dados são recolhidos no controlador de domínio e encaminhados para o Azure Monitor de sete em sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender como é definida a prioridade das recomendações

Todas as recomendações feitas recebem um valor de ponderação que identifica a importância relativa da recomendação. Apenas são apresentadas as 10 recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Método de cálculo das ponderações

As ponderações são valores agregados baseados em três factores-chave:

* A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
* O *impacto* do problema na sua organização se isso causar um problema. Um impacto mais elevado equivale a uma pontuação global maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação de cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Segurança e Conformidade tiver uma pontuação de 5%, implementar essa recomendação aumenta a sua pontuação global de Segurança e Conformidade em 5%.

### <a name="focus-areas"></a>Áreas em foco

**Segurança e Conformidade** - Esta área de foco apresenta recomendações para potenciais ameaças à segurança e violações, políticas corporativas e requisitos técnicos, legais e regulamentares de conformidade.

**Disponibilidade e Continuidade empresarial** - Esta área de foco mostra recomendações para disponibilidade de serviços, resiliência da sua infraestrutura e proteção empresarial.

**Performance e Escalaability** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI cumpre os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implantação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar o Ative Directory para a sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?

Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas ao cliente. No entanto, não existem duas infraestruturas de servidores iguais, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem baixa prioridade a recolha e reporte de dados ad hoc. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Pode querer identificar quais as áreas de foco que são as suas prioridades e, em seguida, ver como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Deve utilizar esta orientação para avaliar se a implementação da recomendação é adequada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Use recomendações de área de foco de verificação de saúde

Depois de instalado, pode ver o resumo das recomendações utilizando o azulejo do Health Check na página de solução no portal Azure.

Veja as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver recomendações para uma área de foco e tomar medidas corretivas

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na página **'Visão Geral',** clique no azulejo **Ative Directory Health Check.**

2. Na página **Health Check,** reveja a informação do resumo numa das lâminas da área de foco e, em seguida, clique numa para ver recomendações para essa área de foco.

3. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.

    ![imagem de recomendações do Health Check](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o artigo tiver sido abordado, avalia mais tarde os registos que recomendaram que foram tomadas medidas recomendadas e a sua pontuação de conformidade aumentará. Os itens corrigidos aparecem como **Objetos Passados.**

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que o Azure Monitor utilizará para impedir que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações que irá ignorar

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Aqui está uma imagem mostrando a consulta de registo:<

![recomendações falhadas](media/ad-assessment/ad-failed-recommendations.png)

Escolha recomendações que queira ignorar. Usará os valores para RecomendaçãoId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro chamado IgnoreRecommendations.txt.

2. Cole ou digite cada RecomendaçãoId para cada recomendação que pretende que o Azure Monitor ignore numa linha separada e, em seguida, guarde e feche o ficheiro.

3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Azure Monitor ignore as recomendações.

   * Nos computadores com o Agente de Monitorização da Microsoft (ligado diretamente ou através do Gestor de Operações) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * No servidor de gestão R2 do Gestor de Operações 2012 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Gestor de Operações 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

Após a próxima verificação de saúde programada, por padrão a cada sete dias, as recomendações especificadas são marcadas *ignoradas* e não aparecerão no painel de instrumentos.

1. Pode utilizar as seguintes consultas de registo para listar todas as recomendações ignoradas.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer IgnoreRecommendations.txt ficheiros ou poderá remover os Recomendações dos mesmos.

## <a name="ad-health-check-solutions-faq"></a>FAQ sobre soluções de Verificação de Estado de Funcionamento do AD

*Que controlos são efetuados pela solução de Avaliação de AD?*

* A seguinte consulta mostra uma descrição de todas as verificações atualmente efetuadas:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem então ser exportados para o Excel para posterior revisão.

*Quantas vezes é que um exame de saúde é executado?*

* O cheque é de sete em sete dias.

*Existe uma maneira de configurar com que frequência o exame de saúde é executado?*

* Neste momento, não.

*Se outro servidor for descoberto depois de eu ter adicionado uma solução de verificação de saúde, será verificado*

* Sim, uma vez descoberto, é verificado a partir daí, a cada sete dias.

*Se um servidor for desativado, quando será removido do exame de saúde?*

* Se um servidor não submeter dados durante 3 semanas, é removido.

*Qual é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora a recolher os dados?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais tempo em servidores que tenham um grande número de servidores ative directory.

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Porquê exibir apenas as 10 melhores recomendações?*

* Em vez de lhe dar uma lista exaustiva de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações usando uma consulta de registo.

*Há alguma maneira de ignorar uma recomendação?*

* Sim, consulte a secção [de recomendações ignore](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes

Utilize [consultas de registo do Azure Monitor](../logs/log-query-overview.md) para aprender a analisar dados e recomendações detalhadas do AD Health Check.

