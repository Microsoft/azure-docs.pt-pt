---
title: 'Quickstart: Portal web Metrics Advisor'
titleSuffix: Azure Cognitive Services
description: Saiba como começar a usar o portal web Metrics Advisor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 65542ca88d055af2c537907d7ae801250943ed0b
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930378"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Quickstart: Monitorize a sua primeira métrica utilizando o portal web

Ao providenciar uma instância Metrics Advisor, pode utilizar as APIs e o espaço de trabalho baseado na web para trabalhar com o serviço. O espaço de trabalho baseado na web pode ser usado como uma forma simples de começar rapidamente com o serviço. Também fornece uma forma visual de configurar definições, personalizar o seu modelo e realizar análises de causa de raiz. 

* A bordo dos seus dados métricos
* Veja as suas métricas e visualizações
* Configurações de deteção de afinação
* Explore insights de diagnóstico
* Criar e subscrever alertas de anomalias

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a sua subscrição Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Crie um recurso Metrics Advisor crie um "  target="_blank"> recurso Metrics Advisor no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para implementar a sua instância Metrics Advisor.  

    
> [!TIP]
> * Pode ser de 10 a 30 minutos para o seu recurso Metrics Advisor para implementar. Clique **em Ir para o recurso** assim que implementar com sucesso.
> * Se quiser utilizar a API REST para interagir com o serviço, precisará da chave e ponto final a partir do recurso que criar. Pode encontrá-los no separador  **Chaves e pontos finais** no recurso criado.

Este documento utiliza uma Base de Dados SQL como exemplo para criar o seu primeiro monitor.

## <a name="sign-in-to-your-workspace"></a>Inscreva-se no seu espaço de trabalho

Após a criação do seu recurso, inscreva-se no [portal Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2143774). Selecione o seu espaço de trabalho para começar a monitorizar as suas métricas. 
 
Atualmente pode criar um recurso Metrics Advisor em cada região disponível. Pode trocar espaços de trabalho no portal Metrics Advisor a qualquer momento.


## <a name="onboard-time-series-data"></a>Dados da série de tempo a bordo

O Metrics Advisor fornece conectores para diferentes fontes de dados, tais como BASE de Dados SQL, Azure Data Explorer e Azure Table Storage. Os passos para a ligação dos dados são semelhantes para diferentes conectores, embora alguns parâmetros de configuração possam variar. Consulte [os seus dados de diferentes fontes](../data-feeds-from-different-sources.md) para os parâmetros necessários para fontes de dados específicas.

Este quickstart utiliza uma Base de Dados SQL como exemplo. Também pode ingerir os seus próprios dados, seguir os mesmos passos.

Para começar, inscreva-se no seu espaço de trabalho Metrics Advisor, com a sua conta Ative Directory. A partir da página de aterragem, selecione o seu **Diretório,** **Subscrição** e **Espaço de Trabalho** que acaba de criar e, em seguida, clique em **Iniciar**. Depois da página principal das cargas de trabalho, selecione Adicionar feed de **dados** do menu esquerdo.

### <a name="data-schema-requirements-and-configuration"></a>Requisitos e configuração de esquemas de dados

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Configurar definições de ligação

> [!TIP]
> Veja [como adicionar feeds de dados](../how-tos/onboard-your-data.md) para obter detalhes sobre os parâmetros disponíveis.

Adicione o feed de dados ligando-o à sua fonte de dados da série de tempo. Comece por selecionar os seguintes parâmetros:

* **Tipo de Origem**: O tipo de fonte de dados onde os dados da série horvas são armazenados.
* **Granularidade**: O intervalo entre pontos de dados consecutivos nos dados da série de tempo, por exemplo, Anual, Mensalmente, Diariamente. Os suportes de personalização de intervalo mais baixos são 60 segundos.
* **Ingerir dados desde (UTC)**: A hora de início para a primeira hora de tempo a ser ingerida. 


Em seguida, especifique a **cadeia de ligação** com as credenciais para a sua fonte de dados e uma **consulta**personalizada . A consulta é usada para especificar os dados a ingerir e convertido no esquema necessário.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Definições de ligação" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Verifique a ligação e carregue o esquema de dados

Após a criação do string de ligação e do fio de consulta, **selecione Verifique e obtenha esquema** para verificar a ligação e executar a consulta para obter o seu esquema de dados a partir da fonte de dados. Normalmente, demora alguns segundos dependendo da ligação à fonte de dados. Se houver um erro neste passo, confirme que:

1. A sua ligação e consulta estão corretas.
2. A sua instância Métrica Advisor é capaz de ligar à fonte de dados se houver definições de firewall.

### <a name="schema-configuration"></a>Configuração de Schema

Uma vez carregado e mostrado o esquema de dados como abaixo, selecione os campos apropriados.


|Seleção  |Descrição  |Notas  |
|---------|---------|---------|
|**Timestamp**     | O tempo de datata de um ponto de dados. Se omitido, o Metrics Advisor utilizará a estamp de tempo quando o ponto de dados for ingerido. Para cada feed de dados, pode especificar no máximo uma coluna como se o timetamp.        | Opcional. Deve ser especificado com no máximo uma coluna.       |
|**Medir**     |  Os valores numéricos no feed de dados. Para cada feed de dados, pode especificar várias medidas, mas pelo menos uma coluna deve ser selecionada como medida.        | Deve ser especificado com pelo menos uma coluna.        |
|**Dimensão**     | Valores categóricos. Uma combinação de diferentes valores identifica uma série de tempo de dimensão única específica, por exemplo: país, língua, inquilino. Pode selecionar nenhum número ou número arbitrário de colunas como dimensões. Nota: se estiver a selecionar uma coluna não-string como dimensão, tenha cuidado com a explosão de dimensão. | Opcional.        |
|**Ignorar**     | Ignore a coluna selecionada.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Definições de ligação" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Configurações de roll-up automática

> [!IMPORTANT]
> Se quiser ativar a análise da **causa raiz** e outras capacidades de diagnóstico, a "configuração automática do roll up" tem de ser configurada. Uma vez ativados, as definições de enrolar automáticas não podem ser alteradas.

O Metrics Advisor pode efetuar automaticamente a agregação (SUM/MAX/MIN...) em cada dimensão durante a ingestão, e depois constrói uma hierarquia que será usada na análise de casos de raiz e outras funcionalidades de diagnóstico. Consulte [as definições de enrolar automáticas](../how-tos/onboard-your-data.md#automatic-roll-up-settings) para obter mais detalhes.

Dê um nome personalizado para o feed de dados, que será exibido no seu espaço de trabalho. Clique em **Enviar**. 

## <a name="tune-detection-configuration"></a>Configuração de deteção de sintonização

Após a adição do feed de dados, o Metrics Advisor tentará ingerir dados métricos a partir da data de início especificada. Levará algum tempo para que os dados sejam totalmente ingeridos, e você pode ver o estado de ingestão clicando no progresso de **Ingestão** no topo da página de feed de dados. Se os dados forem ingeridos, o Metrics Advisor aplicará a deteção e continuará a monitorizar a fonte para novos dados.

Quando a deteção for aplicada, clique numa das métricas listadas no feed de dados para encontrar a **página de detalhe métrica** para: 
- Ver visualizações de todas as fatias de séries de tempo sob esta métrica
- Atualizar a configuração de deteção para atender os resultados esperados
- Configurar notificação para anomalias detetadas

:::image type="content" source="../media/metric-details.png" alt-text="Definições de ligação" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Ver os insights de diagnóstico

Após afinação da configuração de deteção, as anomalias encontradas devem refletir anomalias reais nos seus dados. Metrics Advisor realiza análises em métricas multidimensionais, como agrupamento de anomalias, correlação de incidentes e análise de causa de raiz. Utilize estas funcionalidades para analisar e diagnosticar incidentes nos seus dados.

Para visualizar os conhecimentos de diagnóstico, clique nos pontos vermelhos nas visualizações da série de tempo, que representam anomalias detetadas. Aparecerá uma janela com uma página de análise de incidentes. 

:::image type="content" source="../media/incident-link.png" alt-text="Definições de ligação" lightbox="../media/incident-link.png":::

Depois de clicar no link, você será apostado na página de análise de incidentes que analisa sobre a anomalia correspondente, com um monte de insights de diagnóstico. No topo, haverá estatísticas sobre o incidente, como **Severidade,** **Anomalias envolvidas,** e cronometrado **hora de início** e **fim.** 

Em seguida, verá a anomalia ancestral do incidente, e conselhos automatizados de causa-raiz. Este aconselhamento de causa de raiz automatizada é gerado através da análise da árvore incidente de todas as anomalias relacionadas, incluindo: desvio, distribuição e contribuição para as anomalias dos pais. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Definições de ligação" lightbox="../media/incident-diagnostic.png":::

Com base nestes, já pode ter uma visão direta do que está a acontecer e do impacto do incidente, bem como da causa de raiz mais potencial. Para que se tomassem medidas imediatas para resolver o incidente o mais rapidamente possível. 

Mas também pode girar através de mais insights de diagnóstico aproveitando características adicionais para perfurar anomalias por dimensão, ver anomalias semelhantes e fazer comparação entre métricas. Por favor, encontre mais sobre [Como: diagnosticar um incidente.](../how-tos/diagnose-incident.md) 

## <a name="get-notified-when-new-anomalies-are-found"></a>Ser notificado quando novas anomalias forem encontradas

Se quiser ser alertado quando for detetada uma anomalia nos seus dados, pode criar uma subscrição para uma ou mais das suas métricas. O Conselheiro de Métricas usa ganchos para enviar alertas. Três tipos de ganchos são suportados: gancho de e-mail, web hook e Azure DevOps. Usaremos o web hook como exemplo. 

### <a name="create-a-web-hook"></a>Criar um gancho web

Um gancho web é o ponto de entrada para obter anomalias notadas por uma forma programática do serviço Metrics Advisor, que chama uma API fornecida pelo utilizador quando um alerta é ativado. Para obter mais informações sobre como criar um gancho, consulte a secção **Criar um gancho** em [Como-a-: Configurar alertas e receber notificações usando um gancho](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configurar as definições de alertas

Após a criação de um gancho, uma definição de alerta determina como e quais as notificações de alerta que devem ser enviadas. Pode definir várias definições de alerta para cada métrica. duas definições importantes são **Alert para** as quais especifica as anomalias a incluir e **opções de anomalia de filtro** que definem quais anomalias a incluir no alerta. Consulte a secção **de definições** de alerta Add ou Edit em [Como-a:Configurar alertas e obter notificações usando um gancho](../how-tos/alerts.md#add-or-edit-alert-settings) para mais detalhes.


## <a name="next-steps"></a>Passos seguintes

- [A bordo dos feeds dos seus dados](../how-tos/onboard-your-data.md)
    - [Gerir feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Utilize as bibliotecas REST API ou Cliente](rest-api.md)
- [Configurar métricas e otimizar a configuração da deteção](../how-tos/configure-metrics.md)
