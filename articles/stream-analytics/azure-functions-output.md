---
title: Saída de Funções Azure da Azure Stream Analytics
description: Este artigo descreve as funções de Azure como saída para a Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ffaf66cbb5d9fa0b294f5749a1923684f16c2979
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488109"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Saída de Funções Azure da Azure Stream Analytics

Azure Functions é um serviço de computação sem servidor que pode utilizar para executar código a pedido sem ter de provisões explícitas ou gerir infraestruturas. Permite implementar código que é desencadeado por eventos que ocorrem em Azure ou serviços de parceiros. Esta capacidade de Azure Functions para responder aos gatilhos torna-a uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite que os utilizadores conectem stream Analytics a Funções Azure e execute um script ou peça de código em resposta a uma variedade de eventos.

A produção de Funções Azure da Stream Analytics não está disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International). A ligação às funções do Azure dentro de uma rede virtual (VNet) a partir de um trabalho stream Analytics que está a decorrer num cluster multi-inquilino também não é suportada.

A Azure Stream Analytics invoca funções Azure através de triggers HTTP. O adaptador de saída Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Description |
| --- | --- |
| Aplicação de funções |O nome da sua aplicação Azure Functions. |
| Função |O nome da função na sua aplicação Azure Functions. |
| Chave |Se pretender utilizar uma Função Azure a partir de outra subscrição, pode fazê-lo fornecendo a chave para aceder à sua função. |
| Tamanho do lote máximo |Uma propriedade que permite definir o tamanho máximo para cada lote de saída que é enviado para a sua função Azure. A unidade de entrada está em bytes. Por padrão, este valor é de 262.144 bytes (256 KB). |
| Contagem de lotes max  |Uma propriedade que permite especificar o número máximo de eventos em cada lote que é enviado para As Funções Azure. O valor predefinido é 100. |

O Azure Stream Analytics espera o estado HTTP 200 da aplicação Funções para lotes que foram processados com sucesso.

Quando o Azure Stream Analytics recebe uma exceção 413 ("http Request Entity Too Large") de uma função Azure, reduz o tamanho dos lotes que envia para as Funções Azure. No seu código de função Azure, utilize esta exceção para se certificar de que o Azure Stream Analytics não envia lotes de grandes dimensões. Além disso, certifique-se de que a contagem máxima de lotes e os valores de tamanho utilizados na função são consistentes com os valores introduzidos no portal Stream Analytics.

> [!NOTE]
> Durante a ligação ao teste, o Stream Analytics envia um lote vazio para as Funções Azure para testar se a ligação entre os dois funciona. Certifique-se de que a sua aplicação Funções lida com pedidos de lote vazios para garantir que a ligação de teste passa.

Além disso, numa situação em que não há eventos a aterrar numa janela temporal, não se gera saída. Como resultado, a função **computeResult** não é chamada. Este comportamento é consistente com as funções agregadas en janeladas incorporadas.

## <a name="partitioning"></a>Criação de partições

A chave de partição baseia-se na cláusula PARTITION BY na consulta. O número de autores de saída segue a partição de entrada para [consultas totalmente paralelas.](stream-analytics-scale-jobs.md)

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho do lote predefinido é de 262.144 bytes (256 KB). A contagem de eventos predefinidos por lote é de 100. O tamanho do lote é configurável e pode ser aumentado ou diminuído nas opções de saída stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)