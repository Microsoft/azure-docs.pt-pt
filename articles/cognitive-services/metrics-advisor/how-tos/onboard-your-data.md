---
title: A bordo do feed de dados para o Advisor Métricas
titleSuffix: Azure Cognitive Services
description: Como começar com o embarque dos seus dados para o Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043218"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Como fazer: A bordo dos seus dados métricos ao Metrics Advisor

Utilize este artigo para aprender sobre o embarque dos seus dados ao Metrics Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Requisitos e configuração de esquemas de dados

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Evite carregar dados parciais

Os dados parciais são causados por inconsistências entre os dados armazenados no Metrics Advisor e a fonte de dados. Isto pode acontecer quando a fonte de dados for atualizada após o Metrics Advisor ter terminado de retirar dados. O Metrics Advisor só retira dados de uma determinada fonte de dados uma vez.

Por exemplo, se uma métrica tiver sido a bordo do Metrics Advisor para monitorização. O Metrics Advisor capta com sucesso dados métricos em vezes a hora amp A e executa a deteção de anomalias nele. No entanto, se os dados métricos desse determinado tempotamp A tão atualização forem atualizados após a ingestão dos dados. O novo valor dos dados não será recuperado.

Pode tentar [preencher](manage-data-feeds.md#backfill-your-data-feed) dados históricos (descritos posteriormente) para mitigar inconsistências, mas isso não desencadeará novos alertas de anomalias, se já tiverem sido desencadeados alertas para esses pontos de tempo. Este processo pode adicionar carga de trabalho adicional ao sistema, e não é automático.

Para evitar o carregamento de dados parciais, recomendamos duas abordagens:

* Gerar dados numa única transação:

    Certifique-se de que os valores métricos de todas as combinações de dimensões ao mesmo tempo são armazenados na fonte de dados numa única transação. No exemplo acima, aguarde até que os dados de todas as fontes de dados estejam prontos e, em seguida, carregue-os no Metrics Advisor numa única transação. O Metrics Advisor pode sondar o feed de dados regularmente até que os dados são recuperados com sucesso (ou parcialmente).

* Atrase a ingestão de dados definindo um valor adequado para o parâmetro **de compensação do tempo de ingestão:**

    Desafaça o parâmetro **de compensação do tempo de Ingestão** para o seu feed de dados para atrasar a ingestão até que os dados esteja totalmente preparado. Isto pode ser útil para algumas fontes de dados que não suportam transações como o Azure Table Storage. Consulte [as definições avançadas](manage-data-feeds.md#advanced-settings) para mais detalhes.

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Adicione um feed de dados usando o espaço de trabalho baseado na web

Depois de iniciar sessão no seu portal Metrics Advisor e de escolher o seu espaço de trabalho, clique **em Iniciar**. Em seguida, na página principal do espaço de trabalho, clique em **Adicionar feed de dados** a partir do menu esquerdo.

### <a name="add-connection-settings"></a>Adicionar definições de conexão

Em seguida, irá introduzir um conjunto de parâmetros para ligar a sua fonte de dados da série de tempo. 
* **Tipo de Origem**: O tipo de fonte de dados onde os dados da série horvas são armazenados.
* **Granularidade**: O intervalo entre pontos de dados consecutivos nos dados da série de tempo. Atualmente, o Metrics Advisor suporta: Anual, Monthly, Weekly, Daily, Hourly e Custom. O intervalo mais baixo O suporte da opção de personalização é de 60 segundos.
  * **Segundos**: O número de segundos quando *o nome de granularidade* está definido para *Personalizar*.
* **Ingerir dados desde (UTC)**: A hora de início de base para a ingestão de dados. *startOffsetInSeconds* é frequentemente usado para adicionar uma compensação para ajudar na consistência dos dados.

Em seguida, terá de especificar as informações de ligação para a fonte de dados e as consultas personalizadas utilizadas para converter os dados no esquema necessário. Para obter detalhes sobre os outros campos e ligar diferentes tipos de fontes de dados, consulte [adicionar feeds de dados de diferentes fontes de dados](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Verifique e obtenha o esquema

Depois de definido o fio de ligação e o fio de consulta, **selecione Verifique e obtenha esquema** para verificar a ligação e executar a consulta para obter o seu esquema de dados a partir da fonte de dados. Normalmente, demora alguns segundos dependendo da ligação à fonte de dados. Se houver um erro neste passo, confirme que:

* A sua ligação e consulta estão corretas.
* A sua instância Métrica Advisor é capaz de ligar à fonte de dados se houver definições de firewall.

### <a name="schema-configuration"></a>Configuração de Schema

Uma vez carregado o esquema de dados, selecione os campos apropriados.

Se o ponto de datação de um ponto de dados for omitido, o Metrics Advisor utilizará o tempotamétrico quando o ponto de dados for ingerido. Para cada feed de dados, pode especificar no máximo uma coluna como uma hora de pontuação. Se receber uma mensagem de que uma coluna não pode ser especificada como um timetamp, verifique a sua consulta ou fonte de dados e se existem vários picos de tempo no resultado da consulta - não apenas nos dados de pré-visualização. Ao realizar a ingestão de dados, o Metrics Advisor só pode consumir apenas um pedaço (por exemplo, um dia, uma hora - de acordo com a granularidade) de dados da série de tempo da fonte dada cada vez.

|Seleção  |Descrição  |Notas  |
|---------|---------|---------|
| **Nome do visor** | Nome a ser exibido no seu espaço de trabalho em vez do nome original da coluna. | |
|**Tempotamp**     | O tempo de datata de um ponto de dados. Se omitido, o Metrics Advisor utilizará a estamp de tempo quando o ponto de dados for ingerido. Para cada feed de dados, pode especificar no máximo uma coluna como se o tempotamp.        | Opcional. Deve ser especificado com no máximo uma coluna. Se obter uma coluna não pode ser especificada como erro **do Timestamp,** verifique a sua consulta ou fonte de dados para obter os tempos duplicados.      |
|**Medir**     |  Os valores numéricos no feed de dados. Para cada feed de dados, pode especificar várias medidas, mas pelo menos uma coluna deve ser selecionada como medida.        | Deve ser especificado com pelo menos uma coluna.        |
|**dimensão**     | Valores categóricos. Uma combinação de diferentes valores identifica uma série de tempo de dimensão única específica, por exemplo: país, língua, inquilino. Pode selecionar zero ou mais colunas como dimensões. Nota: tenha cuidado ao selecionar uma coluna de não-cordas como uma dimensão. | Opcional.        |
|**Ignorar**     | Ignore a coluna selecionada.        | Opcional. Veja o texto abaixo.       |

Se pretender ignorar as colunas, recomendamos atualizar a sua consulta ou fonte de dados para excluir essas colunas. Também pode ignorar colunas usando **colunas Ignore** e, em seguida, **ignorar** nas colunas específicas. Se uma coluna deve ser uma dimensão e é erroneamente definida como *ignorada,* o Metrics Advisor pode acabar por ingerir dados parciais. Por exemplo, assuma que os dados da sua consulta são os seguintes:

| ID de linha | CarimboDeDataEHora | País | Linguagem | Receita |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | China | ZH-CN | 10000 |
| 2 | 2019/11/10 | China | EN-US | 1000 |
| 3 | 2019/11/10 | EUA | ZH-CN | 12000 |
| 4 | 2019/11/11 | EUA | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Se *o País* é uma dimensão e a *linguagem* é definida como *ignorada,* então a primeira e segunda linhas terão as mesmas dimensões. O Metrics Advisor utilizará arbitrariamente um valor das duas linhas. O Advisor das Métricas não agregará as linhas neste caso.

### <a name="automatic-roll-up-settings"></a>Configurações de roll-up automática

> [!IMPORTANT]
> Se quiser ativar a análise da causa raiz e outras capacidades de diagnóstico, as **definições de enrolar automáticas** precisam de ser configuradas. Uma vez ativados, as definições de enrolar automáticas não podem ser alteradas.

O Metrics Advisor pode realizar automaticamente a agregação (por exemplo, SUM, MAX, MIN) em cada dimensão durante a ingestão, em seguida, constrói uma hierarquia que será usada na análise de casos de raiz e outras funcionalidades de diagnóstico. 

Pondere os seguintes cenários:

* *Não preciso de incluir a análise dos meus dados.*

    Não é necessário utilizar o roll-up Metrics Advisor.

* *Os meus dados já foram arregaçados e o valor da dimensão é representado por: NULO ou Vazio (Padrão), APENAS NU, Outros.*

    Esta opção significa que o Metrics Advisor não precisa de arregaçar os dados porque as linhas já estão resumidas. Por exemplo, se selecionar *apenas NU,* então a segunda linha de dados no exemplo abaixo será vista como uma agregação de todos os países e da linguagem *EN-US;* a quarta linha de dados que tem um valor vazio para *o País,* no entanto, será vista como uma linha ordinária que pode indicar dados incompletos.
    
    | País | Linguagem | Receita |
    |---------|----------|--------|
    | China   | ZH-CN    | 10000  |
    | (NU)  | EN-US    | 999999 |
    | EUA      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Preciso do Advisor métrico para arregaçar os meus dados calculando Sum/Max/Min/Avg/Count e representá-lo por <some string>*

    Algumas fontes de dados, tais como Cosmos DB ou Azure Blob Storage não suportam certos cálculos como *grupo por* ou *cubo*. O Metrics Advisor fornece a opção roll-up para gerar automaticamente um cubo de dados durante a ingestão.
    Esta opção significa que precisa do Metrics Advisor para calcular o roll-up usando o algoritmo selecionado e utilizar a cadeia especificada para representar o roll-up no Metrics Advisor. Isto não altera quaisquer dados na sua fonte de dados.
    Por exemplo, suponha que tem um conjunto de séries de tempo que significa métricas de vendas com a dimensão (País, Região). Para uma determinada hora, pode parecer o seguinte:


    | Country       | Region           | Sales |
    |---------------|------------------|-------|
    | Canadá        | Alberta          | 100   |
    | Canadá        | Colúmbia Britânica | 500   |
    | Estados Unidos da América | Montana          | 100   |


    Depois de permitir o Auto Roll Up com *Soma,* o Metrics Advisor calculará as combinações de dimensão e resumirá as métricas durante a ingestão de dados. O resultado pode ser:

    | Country       | Region           | Sales |
    | ------------ | --------------- | ---- |
    | Canadá        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Canadá        | Colúmbia Britânica | 500   |
    | NULL          | Colúmbia Britânica | 500   |
    | Estados Unidos da América | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Canadá        | NULL             | 600   |
    | Estados Unidos da América | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` significa que a soma das vendas no Canadá (todas as regiões) é de 600.

    Segue-se a transformação na linguagem SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Considere o seguinte antes de utilizar a função de enrolar automaticamente:

    * Se pretender utilizar *o SUM* para agregar os seus dados, certifique-se de que as suas métricas são aditivas em cada dimensão. Aqui estão alguns exemplos de métricas *não aditivas:*
      * Métricas baseadas em frações. Isto inclui rácio, percentagem, etc. Por exemplo, não se deve adicionar a taxa de desemprego de cada Estado para calcular a taxa de desemprego de todo o país.
      * Sobreposição em dimensão. Por exemplo, não se deve adicionar o número de pessoas em cada desporto para calcular o número de pessoas que gostam de desporto, porque há uma sobreposição entre eles, uma pessoa pode gostar de vários desportos.
    * Para garantir a saúde de todo o sistema, o tamanho do cubo é limitado. Atualmente, o limite é de 1.000.000. Se os seus dados excederem esse limite, a ingestão falhará para essa estampação de tempo.

## <a name="advanced-settings"></a>Definições avançadas

Existem várias configurações avançadas para permitir dados ingeridos de forma personalizada, tais como especificar a compensação de ingestão ou a concordância. Para mais informações, consulte a secção [de definições avançadas](manage-data-feeds.md#advanced-settings) no artigo de gestão de feed de dados.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Especifique um nome para o feed de dados e verifique o progresso da ingestão
 
Dê um nome personalizado para o feed de dados, que será exibido no seu espaço de trabalho. Em seguida, clique em **Enviar**. Na página de dados, pode utilizar a barra de progresso da ingestão para visualizar as informações sobre o estado.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Barra de progresso de ingestão" lightbox="../media/datafeeds/ingestion-progress.png":::


Para verificar detalhes da falha de ingestão: 

1. Clique **em Mostrar Detalhes**.
2. Clique em **Estado** de clique e escolha **Falha ou** **Erro**.
3. Paire sobre uma ingestão falhada, e veja a mensagem de detalhes que aparece.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Verifique a ingestão falhada":::

Um estado *falhado* indica que a ingestão desta fonte de dados será novamente julgada mais tarde.
Um *estado de erro* indica que o Metrics Advisor não voltará a tentar a fonte de dados. Para recarregar os dados, é necessário acionar manualmente um enchimento/recarga.

Também pode recarregar o progresso de uma ingestão clicando em **Refresh Progress**. Após a conclusão da ingestão de dados, pode clicar em métricas e verificar os resultados de deteção de anomalias.

## <a name="next-steps"></a>Passos seguintes
- [Gerencie os seus feeds de dados](manage-data-feeds.md)
- [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
