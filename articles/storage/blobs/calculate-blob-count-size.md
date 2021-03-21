---
title: Calcular a contagem e o tamanho das bolhas utilizando o inventário de armazenamento Azure
description: Saiba como calcular a contagem e o tamanho total das bolhas por recipiente.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102638677"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Calcular a contagem de bolhas e o tamanho total por recipiente utilizando o inventário de armazenamento Azure

Este artigo utiliza a funcionalidade de inventário Azure Blob Storage e a Azure Synapse para calcular a contagem de bolhas e o tamanho total das bolhas por recipiente. Estes valores são úteis para otimizar o uso do blob por recipiente.

Os metadados blob não estão incluídos neste método. A funcionalidade de inventário de armazenamento Azure Blob usa a [Lista Blobs](/rest/api/storageservices/list-blobs) REST API com parâmetros padrão. Assim, o exemplo não suporta instantâneos, contentores '$', e assim por diante.

> [!IMPORTANT]
> O inventário blob está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="enable-inventory-reports"></a>Ativar relatórios de inventário

O primeiro passo neste método é [ativar relatórios de inventário](blob-inventory.md#enable-inventory-reports) na sua conta de armazenamento. Poderá ter de esperar até 24 horas depois de permitir que os relatórios de inventário para o seu primeiro relatório sejam gerados.

Quando tiver um relatório de inventário para analisar, conceda-se a si mesmo, leia o acesso ao recipiente onde reside o ficheiro CSV do relatório.

1. Navegue para o recipiente com o ficheiro de relatório CSV de inventário.
1. Selecione **Access Control (IAM)** e, em seguida, **adicione atribuições de funções**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Selecione adicionar atribuições de funções":::

1. Selecione **Storage Blob Data Reader** da lista de dropdown **role.**

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Adicione o papel de Leitor de Dados blob de armazenamento a partir do dropdown":::

1. Insira o endereço de e-mail da conta que está a usar para executar o relatório no campo **Select.**

## <a name="create-an-azure-synapse-workspace"></a>Criar uma área de trabalho do Azure Synapse

Em seguida, [crie um espaço de trabalho Azure Synapse](/azure/synapse-analytics/get-started-create-workspace) onde executará uma consulta SQL para reportar os resultados do inventário.

## <a name="create-the-sql-query"></a>Criar a consulta SQL

Depois de criar o seu espaço de trabalho Azure Synapse, faça os seguintes passos.

1. Navegue para [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Selecione o **separador Desenvolver** na borda esquerda.
1. Selecione o sinal grande mais (+) para adicionar um item.
1. Selecione **o script SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Selecione o script SQL para criar uma nova consulta":::

## <a name="run-the-sql-query"></a>Executar a consulta SQL

1. Adicione a seguinte consulta SQL no seu espaço de trabalho Azure Synapse para [ler o ficheiro CSV de inventário](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file).

    Para o `bulk` parâmetro, utilize o URL do ficheiro CSV do relatório de inventário que pretende analisar.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Nomeie a sua consulta SQL no painel de propriedades à direita.

1. Publique a sua consulta SQL premindo CTRL+S ou selecionando o botão **Publicar todos.**

1. Selecione o botão **Executar** para executar a consulta SQL. A contagem de bolhas e o tamanho total por recipiente são relatados no painel **de resultados.**

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Saída de executar o script para calcular a contagem de bolhas e o tamanho total.":::

## <a name="next-steps"></a>Passos seguintes

- [Use o inventário de bolhas de armazenamento Azure para gerir dados blob](blob-inventory.md)
- [Calcular o tamanho total da faturação de um recipiente de bolhas](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
