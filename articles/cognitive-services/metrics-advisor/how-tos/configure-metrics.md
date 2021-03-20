---
title: Configure a sua instância Métrica Advisor usando o portal web
titleSuffix: Azure Cognitive Services
description: Como configurar a sua instância Métrica Advisor e afinar os resultados de deteção de anomalias.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048258"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Como: Configurar métricas e configuração de deteção de afinação

Utilize este artigo para começar a configurar a sua instância Métrica Advisor utilizando o portal web. Para navegar nas métricas para um feed de dados específico, vá à página **de feeds de dados** e selecione um dos feeds. Isto apresentará uma lista de métricas associadas a ele.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Selecione uma métrica" lightbox="../media/metrics/select-metric.png":::

Clique num dos nomes métricos para ver os seus detalhes. Nesta visão detalhada, pode mudar para outra métrica no mesmo feed de dados usando a lista de drop down no canto superior direito do ecrã.

Quando vê pela primeira vez os detalhes de uma métrica, pode carregar uma série de tempo, permitindo que o Metrics Advisor escolha um para si, ou especificando valores a incluir para cada dimensão. 

Também pode selecionar intervalos de tempo e alterar o layout da página.

> [!NOTE]
> - A hora de início é inclusiva.
> - O fim de hora é exclusivo. 

Pode clicar no separador **Incidentes** para ver anomalias e encontrar um link para o [centro de incidentes.](diagnose-incident.md)

## <a name="tune-the-detecting-configuration"></a>Sintonize a configuração de deteção

Uma métrica pode aplicar uma ou mais configurações de deteção. Existe uma configuração padrão para cada métrica, a que pode editar ou adicionar, de acordo com as suas necessidades de monitorização.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Sintonize a configuração de todas as séries na métrica atual

Esta configuração será aplicada a todas as séries desta métrica, com exceção das que têm uma configuração separada. Uma configuração de nível métrico é aplicada por padrão quando os dados são acedidos, e é mostrada no painel esquerdo. Os utilizadores podem editar diretamente o nível métrico config na página métrica. 

Existem parâmetros adicionais como **Direction**, e **Anomalia válida** que pode ser usada para afinar ainda mais a configuração. Também pode combinar diferentes métodos de deteção. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Combinação de configuração" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Sintonize a configuração de uma série ou grupo específico

Clique **na configuração avançada** abaixo das opções de configuração de nível métrico para ver a configuração do nível de grupo. Pode adicionar uma configuração para uma série individual ou grupo de séries clicando **+** no ícone nesta janela. Os parâmetros são semelhantes aos parâmetros de configuração de nível métrico, mas pode ser necessário especificar pelo menos um valor de dimensão para uma configuração de nível de grupo para identificar um grupo de séries. E especifique todos os valores de dimensão para configuração de nível de série para identificar uma série específica. 

Esta configuração será aplicada ao grupo de séries ou séries específicas em vez da configuração do nível métrico. Depois de definir as condições para este grupo, guarde-o.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Configuração avançada" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Métodos de deteção de anomalias

O Metrics Advisor oferece múltiplos métodos de deteção de anomalias. Pode utilizar um ou combiná-lo utilizando operadores lógicos clicando no **+** botão. 

**Deteção inteligente** 

A deteção inteligente é alimentada por machine learning que aprende padrões a partir de dados históricos, e usa-os para futura deteção. Ao utilizar este método, a **Sensibilidade** é o parâmetro mais importante para afinar os resultados da deteção. Pode arrastá-lo para um valor menor ou maior para afetar a visualização no lado direito da página. Escolha um que se encaixe nos seus dados e guarde-os. 


No modo de deteção inteligente, os parâmetros de versão de sensibilidade e de limite são utilizados para afinar o resultado da deteção de anomalias.

A sensibilidade pode afetar a largura da gama de valor esperada de cada ponto. Quando aumentado, o intervalo de valor esperado será mais apertado, e mais anomalias serão reportadas:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Deteção inteligente com alta sensibilidade":::

Quando a sensibilidade for recusada, o intervalo de valor esperado será mais largo e serão reportadas menos anomalias:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Deteção inteligente com baixa sensibilidade":::

**Limite de alteração** 

O limiar de alteração é normalmente utilizado quando os dados métricos geralmente permanecem em torno de um determinado intervalo. O limiar é definido de acordo com a **percentagem de Alteração.** O modo **limiar de alteração** é capaz de detetar anomalias nos cenários:

* Os seus dados são normalmente estáveis e suaves. Quer ser notificado quando houver flutuações.
* Os seus dados são normalmente bastante instáveis e flutuam muito. Quer ser notificado quando ficar demasiado estável ou plano.

Utilize os seguintes passos para utilizar este modo:

1. Selecione **Alterar o limiar** como o seu método de deteção de anomalias quando definir as configurações de deteção de anomalias para as suas métricas ou séries de tempo.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="limiar de alteração":::

2. Selecione o **fora do alcance** ou no parâmetro de **alcance** com base no seu cenário.

    Se pretender detetar flutuações, selecione **para fora do alcance**. Por exemplo, com as definições abaixo, qualquer dado que mude mais de 10% em comparação com o anterior será detetado como um outlier.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="fora do parâmetro de alcance":::

    Se pretender detetar linhas planas nos seus dados, selecione **no intervalo**. Por exemplo, com as definições abaixo, qualquer dado que se ajuste dentro de 0,01% em comparação com o anterior será detetado como um outlier. Como o limiar é tão pequeno (0,01%), deteta linhas planas nos dados como outliers.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="No parâmetro de alcance":::

3. Decrete a percentagem de alteração que contará como uma anomalia, e que os pontos de dados previamente capturados serão usados para comparação. Esta comparação é sempre entre o ponto de dados atual, e um único ponto de dados N aponta antes dele.
    
    **A direção** só é válida se estiver a utilizar o modo **fora do** alcance:
    
    * **Até** configurar a deteção apenas para detetar anomalias quando (ponto de dados atual) - (ponto de dados de comparação) > **+** percentagem de limiar.
    * **A deteção** configura apenas para detetar anomalias quando (ponto de dados atual) - (comparando ponto de dados) < **-** percentagem de limiar.
 
**Limiar rígido**

 O limiar rígido é um método básico para a deteção de anomalias. Pode definir um limite superior e/ou inferior para determinar o intervalo de valor esperado. Quaisquer pontos que caiam fora da fronteira serão identificados como uma anomalia. 


## <a name="preset-events"></a>Eventos predefinidos

Por vezes, eventos e ocorrências esperados (como feriados) podem gerar dados anómalos. Utilizando eventos predefinidos, pode adicionar bandeiras à saída de deteção de anomalias, durante os tempos especificados. Esta funcionalidade deve ser configurada após o feed de dados estar a bordo. Cada métrica só pode ter uma configuração de evento predefinida.

> [!Note]
> A configuração do evento predefinido terá em consideração as férias durante a deteção de anomalias e poderá alterar os seus resultados. Será aplicado aos pontos de dados ingeridos depois de guardar a configuração. 

Clique no botão **Configure Preset Event** ao lado da lista de métricas de queda em cada página de detalhes métricos.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="botão de evento predefinido":::

Na janela que aparece, configuure as opções de acordo com o seu uso. Certifique-se **de que o evento de férias** enable está selecionado para utilizar a configuração. 

A secção **de eventos Holiday** ajuda-o a suprimir anomalias desnecessárias detetadas durante as férias. Existem duas opções para a opção **Estratégia** que pode aplicar:

* **Suprimir as férias**: Suprime todas as anomalias e alertas nos resultados de deteção de anomalias durante o período de férias.
* **Feriado como fim de semana**: Calcula os valores médios esperados de vários fins de semana correspondentes antes do feriado, e baseia o estado de anomalia fora destes valores.

Existem vários outros valores que pode configurar:

|Opção  |Descrição  |
|---------|---------|
|**Escolha uma dimensão como país**     | Escolha uma dimensão que contenha informações do país. Por exemplo, um código de país.         |
|**Mapeamento de código de país**     | O mapeamento entre um [código de país](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)padrão e dados do país escolhidos.        |
|**Opções de férias**    | Se tomar em consideração todos os feriados, apenas férias PTO (Férias pagas) ou apenas feriados não-PTO.         |
|**Dias para expandir**    |  Os dias impactados antes e depois de um feriado.        |


A secção **de eventos Cycle** pode ser usada em alguns cenários para ajudar a reduzir alertas desnecessários usando padrões cíclicos nos dados. Por exemplo: 

- Métricas que têm múltiplos padrões ou ciclos, como um padrão semanal e mensal. 
- Métricas que não têm um padrão claro, mas os dados são comparáveis Ano após Ano (YoY), Mês em Mês (MoM), Week Over Week (WoW), ou Day Over Day (DoD).
 
Nem todas as opções são selecionáveis para cada granularidade. As opções disponíveis por granularidade são abaixo:

| Granularidade | Rio YoY | MoM | Rio WoW | DoD |
|:-|:-|:-|:-|:-|
| Anualmente | X | X | X | X |
| Mensalmente | X | X | X | X |
| Semanal | ✔ | X | X | X |
| Diário | ✔ | ✔ | ✔ | X |
| Hora a hora | ✔ | ✔ | ✔ | ✔ |
| Minuciosamente | X | X | X | X |
| Em segundo lugar | X | X | X | X |
| Personalizado* | ✔ | ✔ | ✔ | ✔ |

X - Indisponível.  
✔ - Disponível.
  
\* Quando utilizar uma granularidade personalizada em segundos, só disponível se a métrica for superior a uma hora e menos de um dia.

O evento de ciclo é usado para reduzir anomalias se seguirem um padrão cíclico, mas irá relatar uma anomalia se vários pontos de dados não seguirem o padrão. **O modo rigoroso** é usado para permitir a reporte de anomalias se mesmo um ponto de dados não seguir o padrão. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="configuração de evento predefinido":::

## <a name="view-recent-incidents"></a>Ver incidentes recentes

O Metrics Advisor deteta anomalias em todos os dados da série de tempo à medida que são ingeridos. No entanto, nem todas as anomalias precisam de ser escaladas, porque podem não ter um grande impacto. A agregação será realizada em anomalias para agrupar as relacionadas em incidentes. Pode ver estes incidentes a partir do separador **Incidente** na página de detalhes das métricas. 

Clique num incidente para ir à página **de análise de Incidentes** onde pode ver mais detalhes sobre o mesmo. Clique em **Gerir incidentes em novo centro de incidentes,** para encontrar a página [do centro de Incidentes](diagnose-incident.md) onde pode encontrar todos os incidentes sob a métrica específica. 

## <a name="subscribe-anomalies-for-notification"></a>Subscrever anomalias para notificação

Se quiser ser notificado sempre que for detetada uma anomalia, pode subscrever alertas para a métrica, utilizando um gancho. Consulte [alertas de configuração e obtenha notificações usando um gancho](alerts.md) para obter mais informações.


## <a name="next-steps"></a>Passos seguintes 
- [Configurar alertas e obter notificações através de um alerta](alerts.md)
- [Ajustar a deteção de anomalias através de comentários](anomaly-feedback.md)
- [Diagnosticar um incidente.](diagnose-incident.md)

