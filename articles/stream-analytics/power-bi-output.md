---
title: Saída de Power BI da Azure Stream Analytics
description: Este artigo descreve como obter dados de produção do Azure Stream Analytics para Power BI.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: cc48d26c217f52dfa52ac6cd3d7f18a806c93927
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740985"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Saída de Power BI da Azure Stream Analytics

Pode utilizar [o Power BI](https://powerbi.microsoft.com/) como uma saída para um trabalho stream Analytics para proporcionar uma experiência de visualização rica dos resultados da análise. Pode utilizar esta capacidade para dashboards operacionais, geração de relatórios e relatórios baseados em métricas.

A produção de Power BI do Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes dos imóveis e as suas descrições para configurar a sua saída Power BI.

| Nome da propriedade | Description |
| --- | --- |
| Alias de saída |Forneça um nome amigável que seja usado em consultas para direcionar a saída de consulta para esta saída Power BI. |
| Espaço de trabalho em grupo |Para ativar a partilha de dados com outros utilizadores do Power BI, pode selecionar grupos dentro da sua conta Power BI ou escolher **o My Workspace** se não quiser escrever para um grupo. A atualização de um grupo existente requer a renovação da autenticação Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que pretende que a saída Power BI utilize. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída Power BI. Atualmente, a produção de Power BI a partir de trabalhos stream Analytics pode ter apenas uma tabela num conjunto de dados. |
| Autorizar a ligação | Tem de autorizar com o Power BI a configurar as definições de saída. Assim que conceder este acesso de saída ao seu dashboard Power BI, pode revogar o acesso alterando a palavra-passe da conta de utilizador, eliminando a saída do trabalho ou eliminando o trabalho stream Analytics. | 

Para uma passagem de configuração de uma saída de Power BI e painel de instrumentos, consulte o tutorial [Azure Stream Analytics e Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel Power BI. O conjunto de dados e a tabela são automaticamente povoados quando o trabalho é iniciado e o trabalho começa a bombear a saída para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Se o Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome que o fornecido neste trabalho stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema

O Azure Stream Analytics cria um conjunto de dados power BI e esquema de tabela para o utilizador se já não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, apenas uma tabela pode existir dentro de um conjunto de dados. 

Power BI utiliza a política de retenção de primeira e primeira saída (FIFO). Os dados serão recolhidos numa tabela até atingirem as 200.000 filas.

> [!NOTE]
> Não recomendamos a utilização de várias saídas para escrever para o mesmo conjunto de dados, pois pode causar vários problemas. Cada saída tenta criar o conjunto de dados Power BI de forma independente, o que pode resultar em múltiplos conjuntos de dados com o mesmo nome. Além disso, se as saídas não tiverem esquemas consistentes, o conjunto de dados altera o esquema em cada escrita, o que leva a muitos pedidos de alteração de esquemas. Mesmo que estas questões sejam evitadas, as saídas múltiplas serão menos performantes do que uma única saída fundida.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para Power BI

O Azure Stream Analytics atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações no nome da coluna, alterações do tipo de coluna e a adição ou remoção de colunas são todas rastreadas.

Esta tabela cobre as conversões do tipo de dados do [Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics) para os tipos de Data [Model (EDM)](/dotnet/framework/data/adonet/entity-data-model)da Power BIEntity Data Model , se não existir um conjunto de dados e tabela power BI.

De Stream Analytics | Para Power BI
-----|-----
bigint | Int64
nvarchar(máx) | String
datetime | Datetime
float | Double (Duplo)
Matriz de registos | Tipo de corda, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema

O Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Mais tarde, se necessário, o esquema do modelo de dados é atualizado para acomodar eventos que possam não se encaixar no esquema original.

Evite a `SELECT *` consulta para evitar uma atualização dinâmica do esquema através de linhas. Além das potenciais implicações de desempenho, pode resultar na incerteza do tempo que os resultados demoram. Selecione os campos exatos que precisam de ser mostrados no painel Power BI. Além disso, os valores dos dados devem estar em conformidade com o tipo de dados escolhido.

Anterior/corrente | Int64 | String | Datetime | Double (Duplo)
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia | Cadeia | Double (Duplo)
Double (Duplo) | Double (Duplo) | Cadeia | Cadeia | Double (Duplo)
Cadeia | Cadeia | Cadeia | Cadeia | Cadeia 
Datetime | Cadeia | Cadeia |  Datetime | String

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para o tamanho do lote de saída, consulte [os limites da API de repouso Power BI](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Passos seguintes

* [Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI (pré-visualização)](powerbi-output-managed-identity.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)