---
title: Resolver problemas de faturação do Azure CSP com tabelas dinâmicas de ficheiros de utilização
description: Este artigo ajuda-o a resolver problemas de faturação do Fornecedor de Soluções Cloud do Azure (CSP) com tabelas dinâmicas criadas a partir dos seus ficheiros de utilização CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026876"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Resolver problemas de faturação do CSP com tabelas dinâmicas de ficheiros de utilização

Este artigo ajuda-o a resolver problemas de faturação do Fornecedor de Soluções Cloud (CSP) com tabelas dinâmicas criadas nos seus ficheiros de reconciliação (utilização) do Centro de Parceiros. Os ficheiros de utilização do Azure contêm todas as suas informações de consumo e utilização do Azure. As informações no ficheiro podem ajudá-lo a:

- Compreender como as reservas do Azure estão a ser utilizadas e aplicadas
- Reconciliar as informações no Azure Cost Management com a fatura cobrada
- Resolver problemas de um aumento de custo
- Calcular um valor de reembolso para um contrato de nível de serviço

Ao utilizar as informações dos ficheiros de utilização, pode obter uma melhor compreensão dos problemas de utilização e diagnosticá-los. Os ficheiros de utilização são gerados no formato delimitado por vírgulas (CSV). Como os ficheiros de utilização podem ser ficheiros CSV grandes, são mais fáceis de manipular e ver como tabelas dinâmicas numa aplicação de folha de cálculo, como o Excel. Os exemplos neste artigo utilizam o Excel, mas pode utilizar qualquer aplicação de folha de cálculo que desejar.

Apenas os administradores de Faturação e Globais têm acesso para transferir ficheiros de reconciliação. Para obter mais informações, veja [Saiba como ler os itens de linha nos ficheiros de reconciliação do Centro de Parceiros](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Obter os dados e formatá-los

Como os ficheiros de utilização do Azure estão no formato CSV, tem de preparar os dados para utilização no Excel. Utilize os passos seguintes para formatar os dados como uma tabela.

1. Transfira o ficheiro de utilização através das instruções em [Encontrar a sua fatura](/partner-center/read-your-bill#find-your-bill).
1. Abra um ficheiro no Excel.
1. Os dados não formatados são semelhantes ao exemplo seguinte.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Selecione o primeiro campo na tabela **PartnerID**.
1. Prima Ctrl + Shift + Seta para baixo e, em seguida, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **A minha tabela tem cabeçalhos** e, em seguida, selecione **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo que mostra dados não formatados no Excel" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e, em seguida, selecione **OK**. A ação cria uma nova folha de cálculo no ficheiro e leva-o para a área da tabela dinâmica no lado direito da folha de cálculo.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A área Campos da Tabela Dinâmica é uma área de arrastar e largar. Avance para a secção seguinte para criar a tabela dinâmica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Criar tabela dinâmica para ver os custos do Azure por recursos

Nesta secção, irá criar uma tabela dinâmica na qual poderá resolver problemas gerais de utilização do Azure. A tabela de exemplo pode ajudá-lo a investigar o serviço que consome a maioria dos recursos. Também pode ver os recursos que incorrem mais custos e como um serviço está a ser faturado.

1. Na área Campos da Tabela Dinâmica, arraste **Nome do Serviço** e **Recurso** para a área **Linhas**. Coloque **Recurso** abaixo de **Nome do Serviço**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Em seguida, coloque **Custo Depois dos Impostos** na área **Valores**. Também pode utilizar a coluna Quantidade Consumida em vez de obter informações sobre as unidades de consumo e as transações. Por exemplo, GB e Horas. Ou ainda transações em vez de custo em moedas diferentes, como USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Agora, tem um dashboard para investigação do consumo generalizado. Pode filtrar um serviço específico com as opções de filtragem na tabela dinâmica.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar um segundo nível numa tabela dinâmica, por exemplo, um recurso, selecione um item de segundo nível na tabela.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Para filtros adicionais, pode adicionar **SubscriptionID** e **Nome da Empresa do Cliente** na área **Filtros** e selecione o âmbito desejado.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Criar uma tabela dinâmica para ver a utilização do Azure por data

Nesta secção, irá criar uma tabela dinâmica na qual poderá resolver problemas gerais de utilização do Azure por Quantidade Consumida e data. É útil identificar os picos de faturação por data e serviço. Também pode ver os recursos que incorrem mais custos e como um serviço está a ser faturado.

O ficheiro de reconciliação tem duas tabelas. Uma está na parte superior (a tabela principal) e outra na parte inferior do documento. Esta segunda tabela inclui grande parte das mesmas informações. No entanto, não inclui detalhes de preços ou custos. Tem a data de utilização e a quantidade consumida.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Siga os mesmos passos da secção [Obter os dados e formatá-los](#get-the-data-and-format-it) para criar uma tabela do Excel com as informações na parte inferior do ficheiro de reconciliação.
1. Quando a tabela estiver pronta e tiver uma folha de tabela dinâmica, siga os mesmos passos da secção create-pivot-table-to-view-azure-costs-by-resources para preparar o dashboard. Em vez de utilizar o Custo Depois dos Impostos, coloque **Quantidade consumida** na área **Valores**.
1. Adicione **Data de Utilização** à secção Colunas. A tabela dinâmica deve ser semelhante ao exemplo seguinte.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Agora, tem um dashboard que mostra a utilização por data. Pode expandir cada mês ao selecionar o símbolo **+** .

O dashboard mostra a quantidade consumida em unidades, como GB, Horas e Transferências.

Para ver o preço por dia, pode adicionar **GUID do Recurso** à área **Linhas**. Na tabela superior, adicione o preço unitário (**ListPrice**) do recurso. Multiplique **ListPrice** pela **Quantidade consumida** para calcular os custos antes do imposto. Os valores devem corresponder.

Alguns recursos (serviços) têm preços dimensionados por quantidade consumida. Por exemplo, alguns recursos têm um preço mais alto para os primeiros 100 GB consumidos e um preço mais baixo para os GB utilizados posteriormente. Tenha em mente os preços dimensionados quando calcular os custos manualmente.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Criar uma tabela dinâmica para ver o custo de um recurso específico

Um único recurso pode incorrer em vários custos para diferentes serviços. Por exemplo, uma máquina virtual pode incorrer em custos de Computação, licenciamento do SO, Largura de Banda (transferências de dados), utilização de RI e armazenamento de instantâneos. Sempre que quiser rever a utilização geral de recursos específicos, o guia de passos seguinte mostra como criar um dashboard para ver a utilização geral nos seus ficheiros de utilização.

Os ficheiros de reconciliação não contêm detalhes específicos do recurso. Assim, utiliza o ficheiro de utilização agregado. Contacte o [suporte de Faturação do Azure](https://go.microsoft.com/fwlink/?linkid=2083458) para que lhes forneçam o ficheiro de utilização agregado da sua subscrição. Os ficheiros agregados são gerados ao nível da subscrição. Os dados não formatados são semelhantes ao exemplo seguinte.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

O ficheiro contém as colunas seguintes.

- **UsageStart** e **UsageEnd** – Data de cada item de linha (cada unidade de utilização). Por exemplo, todos os dias.
- **MeteredResourceID** – No Azure, corresponde ao ID do medidor.
- **Propriedades** – Contém o ID da Instância (nome do recurso) com outros detalhes, como a localização.
- **Quantidade** – A quantidade consumida no ficheiro de reconciliação.

1. Selecione o primeiro campo na tabela **PartnerID**.  
1. Prima Ctrl + Shift + Seta para baixo e, em seguida, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **A minha tabela tem cabeçalhos** e, em seguida, selecione **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo que mostra dados não formatados no Excel" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e, em seguida, selecione **OK**. A ação cria uma nova folha de cálculo no ficheiro e leva-o para a área da tabela dinâmica no lado direito da folha de cálculo.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Em seguida, adicione **MeteredResourceID** à área **Linhas** e **Quantidade** a **Valores**. Os resultados mostram as informações de utilização geral. Para obter detalhes adicionais, coloque **UsageEndDateTime** na área **Colunas**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Exemplo que mostra dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Para ver um relatório geral, adicione **Propriedades** a **Linhas** em **MeteredResourceID**. Mostra um dashboard completo para sua utilização.
1. Para filtrar por um recurso específico, adicione **Propriedades** à área **Filtros** e selecione a utilização desejada. Pode utilizar Pesquisar para localizar um nome de recurso.
    Para ver o custo do recurso, localize a quantidade total consumida e multiplique o valor pelo preço de tabela. O preço de tabela é específico de cada GUID do Recurso (MeteredResourceID). Se um recurso estiver a consumir vários MeteredResourceIDs, terá de anotar o valor total de cada ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Passos seguintes

- [Começar a utilizar o Azure Cost Management para parceiros](../costs/get-started-partners.md).