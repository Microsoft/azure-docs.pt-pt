---
title: Resolver problemas de faturação do Azure EA com tabelas dinâmicas de ficheiros de utilização
description: Este artigo ajuda-o a resolver problemas de faturação do Contrato Enterprise (EA) com tabelas dinâmicas criadas a partir dos seus ficheiros de utilização CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: a30f85ae1b65321a25ee93239374d2a8aae26769
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026686"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>Resolver problemas de faturação do EA com tabelas dinâmicas de ficheiros de utilização

Este artigo ajuda-o a resolver problemas de faturação do EA com tabelas dinâmicas nos seus ficheiros de utilização. Os ficheiros de utilização do Azure contêm todas as suas informações de consumo e utilização do Azure. As informações no ficheiro podem ajudá-lo a:

- Compreender como as reservas do Azure estão a ser utilizadas e aplicadas
- Reconciliar as informações no Azure Cost Management com a fatura cobrada
- Resolver problemas de um aumento de custo
- Calcular um valor de reembolso para um contrato de nível de serviço

Ao utilizar as informações dos ficheiros de utilização, pode obter uma melhor compreensão dos problemas de utilização e diagnosticá-los. Os ficheiros de utilização são gerados no formato delimitado por vírgulas (CSV). Como os ficheiros de utilização podem ser ficheiros CSV grandes, são mais fáceis de manipular e ver como tabelas dinâmicas numa aplicação de folha de cálculo, como o Excel. Os exemplos neste artigo utilizam o Excel, mas pode utilizar qualquer aplicação de folha de cálculo que desejar.

Apenas os administradores EA, Proprietários de Conta e Administradores de Departamento têm acesso para transferir ficheiros de utilização.

## <a name="get-the-data-and-format-it"></a>Obter os dados e formatá-los

Como os ficheiros de utilização do Azure estão no formato CSV, tem de preparar os dados para utilização no Excel. Utilize os passos seguintes para formatar os dados como uma tabela.

1. Transfira o ficheiro Usage Details Version 2 with All Charges (utilização e compras) através das instruções em [Transferir a utilização para clientes do EA](https://docs.microsoft.com/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date#download-usage-for-ea-customers).
1. Abra um ficheiro no Excel.
1. Os dados não formatados são semelhantes ao exemplo seguinte.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Selecione o primeiro campo na tabela, o que contém o título da primeira coluna, **BillingAccountID**.
1. Prima Ctrl + Shift + Seta para baixo e, em seguida, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **A minha tabela tem cabeçalhos** e, em seguida, selecione **OK**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo que mostra dados não formatados no Excel" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e, em seguida, selecione **OK**. A ação cria uma nova folha no ficheiro. É direcionado para a área da tabela dinâmica à direita da folha.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A área Campos da Tabela Dinâmica é uma área de arrastar e largar. Avance para a secção seguinte para criar a tabela dinâmica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Criar tabela dinâmica para ver os custos do Azure por recursos

Nesta secção, irá criar uma tabela dinâmica na qual poderá resolver problemas gerais de utilização do Azure. A tabela de exemplo pode ajudá-lo a investigar o serviço que consome a maioria dos recursos. Também pode ver os recursos que incorrem mais custos e como um serviço está a ser faturado.

1. Na área Campos da Tabela Dinâmica, arraste **Categoria do Medidor** e **Produto** para a secção **Linhas**. Coloque **Produto** abaixo de **Categoria do Medidor**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Em seguida, adicione a coluna **Custo** à secção **Valores**. Também pode utilizar a coluna Quantidade Consumida em vez de obter informações sobre as unidades de consumo e as transações. Por exemplo, GB e Horas. Ou ainda transações em vez de custo em moedas diferentes, como USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Agora, tem um dashboard para investigação do consumo generalizado. Pode filtrar um serviço específico com as opções de filtragem na tabela dinâmica.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar um segundo nível numa tabela dinâmica, por exemplo, um recurso, selecione um item de segundo nível na tabela.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Arraste a coluna **ResourceID** para a área **Linhas** em **Produto** para ver o custo de cada serviço por recurso. Para ver informações detalhadas sobre preços, veja o UnitPrice da sua organização e pesquise **Produto** na primeira coluna da lista de preços.
1. Adicione a coluna **Data** à área **Colunas** para ver o consumo diário do produto.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Expanda e feche os meses com os símbolos **+** para a coluna de cada mês.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    Adicionar as colunas **Custo** e **Quantidade** na área **Valores** é opcional. Ao fazê-lo, são criadas duas colunas para cada secção de dados abaixo de cada mês e dia quando a coluna Data está na secção Colunas da tabela dinâmica.
1. Para filtros adicionais, pode adicionar as colunas SubscriptionID, Department, ResourceGroup, Tags ou Cost Center à área **Filtros** e selecionar o item desejado.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Criar uma tabela dinâmica para ver o custo de um recurso específico

Um único recurso pode incorrer em vários custos para diferentes serviços. Por exemplo, uma máquina virtual pode incorrer em custos de Computação, licenciamento do SO, Largura de Banda (transferências de dados), utilização de RI e armazenamento de instantâneos. Sempre que quiser rever a utilização geral de recursos específicos, o guia de passos seguinte mostra como criar um dashboard para ver a utilização geral nos seus ficheiros de utilização.

1. No menu à direita, arraste **ResourceID** para a secção **Filtro** no menu da tabela dinâmica.
1. Selecione o recurso para o qual deseja ver o custo. Escreva na caixa **Pesquisa** para localizar um nome de recurso.
1. Adicione **Categoria do Medidor** e **Produto** à secção Linhas. Coloque **Produto** abaixo de **Categoria do Medidor**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Em seguida, adicione a coluna **Custo** à secção **Valores**. Também pode utilizar a coluna Quantidade Consumida em vez de obter informações sobre as unidades de consumo e as transações. Por exemplo, GB e Horas. Ou ainda transações em vez de custo em moedas diferentes, como USD, EUR e INR. Agora, tem um dashboard que mostra todos os serviços consumidos pelo recurso.
1. Adicione a coluna **Data** à secção **Colunas**. Mostra o consumo diário.
1. Pode expandir e reduzir com os símbolos **+** na coluna de cada mês.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exemplo que mostra dados não formatados no Excel" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explorar e analisar os custos com a análise de custos](../costs/quick-acm-cost-analysis.md).