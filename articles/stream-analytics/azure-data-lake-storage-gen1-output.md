---
title: Azure Data Lake Storage Gen 1 produção da Azure Stream Analytics
description: Este artigo descreve o Azure Data Lake Storage Gen 1 como uma opção de saída para a Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 59b4a04231df3b93b093750cc6c9d70982a418a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665349"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage Gen 1 produção da Azure Stream Analytics

Stream Analytics suporta saídas [Azure Data Lake Storage Gen 1.](../data-lake-store/data-lake-store-overview.md) Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para grandes cargas analíticas de dados. Pode utilizar o Armazenamento do Lago de Dados para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. Stream Analytics precisa ser autorizado a aceder ao armazenamento do Data Lake.

A produção de armazenamento de lagos de dados Azure da Stream Analytics não está disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes das propriedades e as suas descrições para configurar a sua saída do Data Lake Storage Gen 1.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para data lake store. |
| Subscrição | A subscrição que contém a sua conta de Armazenamento de Data Lake Azure. |
| Nome da conta | O nome da conta Data Lake Store para onde está a enviar a sua produção. É-lhe apresentada uma lista pendente de contas da Data Lake Store que estão disponíveis na sua subscrição. |
| Padrão de prefixo do caminho | O caminho do ficheiro que é usado para escrever os seus ficheiros dentro da conta especificada data lake store. Pode especificar uma ou mais instâncias das variáveis {date} e {time}:<br /><ul><li>Exemplo 1: pasta1/logs/{date}/{time}</li><li>Exemplo 2: pasta1/logs/{date}</li></ul><br />O carimbo de tempo da estrutura da pasta criada segue a UTC e não a hora local.<br /><br />Se o padrão do caminho do ficheiro não contiver um corte de fuga (/), o último padrão no caminho do ficheiro é tratado como um prefixo de nome de ficheiro. <br /><br />Nestas circunstâncias são criados novos ficheiros:<ul><li>Alteração do esquema de saída</li><li>Reinício externo ou interno de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for utilizado no caminho do prefixo, pode selecionar o formato de data em que os seus ficheiros estão organizados. Exemplo: YYYY/MM/DD |
|Formato de tempo | Opcional. Se o token de tempo for utilizado no caminho do prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados.|
| Encoding | Se estiver a utilizar o formato CSV ou JSON, deve ser especificado um codificamento. UTF-8 é o único formato de codificação suportado neste momento.|
| Delimitador | Aplicável apenas para serialização de CSV. Stream Analytics suporta uma série de delimiters comuns para serializar dados de CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.|
| Formato | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido.  **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Esta matriz só é fechada quando o trabalho parar ou stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar JSON separados de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito.|
| Modo de autenticação | Pode autorizar o acesso à sua conta de Armazenamento de Data Lake utilizando [identidade gerida](stream-analytics-managed-identities-adls.md) ou ficha de utilizador. Uma vez que você concede acesso, você pode revogar o acesso alterando a palavra-passe da conta de utilizador, eliminando a saída de Armazenamento de Data Lake para este trabalho, ou eliminando o trabalho stream Analytics. |

## <a name="partitioning"></a>Criação de partições

Para a tecla de partição, utilize fichas {date} e {time} no padrão de prefixo do caminho. Escolha um formato de data, como YYYY/MM/DD, DD/MM/YYYY, ou MM-DD-YYYY. Utilize HH para o formato de tempo. O número de autores de saída segue a partição de entrada para [consultas totalmente paralesivelizáveis.](stream-analytics-scale-jobs.md)

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte os [limites de armazenamento do Data Lake](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Para otimizar o tamanho do lote, utilize até 4 MB por operação de escrita.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)