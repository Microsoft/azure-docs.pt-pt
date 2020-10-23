---
title: incluir ficheiro
description: incluir ficheiro
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026733"
---
## <a name="transform-data-before-using-large-usage-files"></a>Transformar os dados antes de utilizar ficheiros de utilização grandes

Por vezes, o ficheiro de utilização ou reconciliação é demasiado grande para ser aberto. Também poderá precisar de apenas uma parte das informações para resolver um problema. Por exemplo, pode querer apenas informações de um recurso específico ou o consumo de alguns serviços ou grupos de recursos. Pode transformar os dados para os resumir antes de criar tabelas dinâmicas.

1. Abra um livro em branco no Excel.
1. No menu superior, selecione **Dados** > **De Texto/CSV**, selecione o ficheiro de utilização e, em seguida, selecione **Importar**.
1. Na parte inferior da janela, selecione **Transformar Dados**. Uma nova janela mostra um resumo dos dados.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Exemplo que mostra os dados resumidos" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Se tiver um Contrato de Cliente Microsoft, ignore este passo e avance para o seguinte, uma vez que os ficheiros de utilização do MCA geralmente têm os títulos de coluna nas primeiras linhas. Prepare os dados ao criar a tabela. Remova as linhas superiores, deixando apenas os títulos. Selecione **Remover Linhas** > **Remover Linhas Superiores**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Exemplo que mostra os dados resumidos" :::
1. Na janela Remover Linhas Superiores, introduza o número de linhas a remover na parte superior. Normalmente 2 para EA e 1 para CSP. Selecione **OK**.
1. Selecione **Utilizar Primeira Linha como Cabeçalhos**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Exemplo que mostra os dados resumidos" :::
    
    A vista de tabela mostra os títulos de coluna na parte superior.
1. Em seguida, adicione um filtro. Utilize as setas do seletor à direita de cada título de coluna para filtrar. Os filtros sugeridos são ID de Subscrição, Nome do Serviço (Categoria do Medidor), ID da Instância e grupo de recursos. Pode utilizar vários filtros no mesmo documento. Recomendamos que aplique todos os filtros possíveis para reduzir o tamanho do documento e ajudar o trabalho posterior.
1. Depois de aplicar os filtros, selecione **Fechar e Carregar**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Exemplo que mostra os dados resumidos" :::

O filtro é carregado e mostra uma tabela com dados de utilização filtrados. Agora, pode criar uma nova tabela dinâmica para resolver problemas de utilização.