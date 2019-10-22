---
title: Importar do banco de dados SQL do Azure
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo importar dados em Azure Machine Learning interface visual para obter dados de um banco de dado SQL do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694613"
---
# <a name="import-from-azure-sql-database"></a>Importar do banco de dados SQL do Azure

Este artigo descreve como usar o módulo [importar dados](import-data.md) em Azure Machine Learning interface visual para obter dados de um banco de dado SQL do Azure.  

Para importar dados de um banco de dados do, você deve especificar o nome do servidor e o nome do banco e uma instrução SQL que define a tabela, exibição ou consulta.  

Em geral, o armazenamento de dados em bancos de dados do Azure é mais caro do que usar tabelas ou BLOBs no Azure. Também pode haver limites na quantidade de dados que você pode armazenar em um banco de dado, dependendo do tipo de assinatura. No entanto, não há nenhuma taxa de transação em relação ao banco de dados SQL Azure, portanto, essa opção é ideal para acesso rápido a quantidades menores de informações usadas com frequência, como tabelas de pesquisa de dados ou dicionários de dados.

O armazenamento de dados em um banco de dado do Azure também é preferível se você precisar filtrar os dados antes de lê-los ou se quiser salvar previsões ou métricas de volta no banco de dados para relatórios.

## <a name="how-to-import-data-from-azure-sql-database"></a>Como importar dados do banco de dados SQL do Azure

1. Adicione o módulo [importar dados](import-data.md) ao seu pipeline. Você pode encontrar esse módulo na interface visual, na categoria entrada e saída de dados.

1. Para **fonte de dados**, selecione **Azure SQL Database**.

1. Defina as seguintes opções específicas para o banco de dados SQL do Azure.

    **Nome do servidor de banco de dados**: digite o nome do servidor que é gerado pelo Azure. Normalmente, ele tem a forma `<generated_identifier>.database.windows.net`.

    **Nome do banco de dados**: digite o nome de um banco de dados existente no servidor especificado.

    **Nome da conta de usuário do servidor**: digite o nome de usuário de uma conta que tenha permissões de acesso para o banco de dados.

    **Senha da conta de usuário do servidor**: forneça a senha para a conta de usuário especificada.

    **Consulta de banco de dados**: digite ou cole uma instrução SQL que descreva os dados que você deseja ler. Sempre valide a instrução SQL e verifique os resultados da consulta com antecedência, usando uma ferramenta como o Visual Studio Gerenciador de Servidores ou SQL Server Data Tools.

1. Se o conjunto de resultados que você lê em Azure Machine Learning não se espera alterar entre as execuções do pipeline, selecione a opção **usar resultados em cache** .

    Quando selecionado, se não houver nenhuma outra alteração nos parâmetros do módulo, o pipeline carregará os dados na primeira vez em que o módulo for executado e depois usará uma versão em cache do conjunto de dados.

    Se você quiser recarregar o conjunto de uma em cada iteração do pipeline, desmarque essa opção. O conjunto de dados é recarregado a partir da origem sempre que todos os parâmetros são alterados em [Import data](import-data.md).

1. Executar o pipeline.

    À medida que os [dados de importação](import-data.md) carregam os dados na interface visual, alguma conversão implícita de tipo também pode ser executada, dependendo dos tipos de dados usados no banco de dado de origem.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com êxito.

Opcionalmente, você pode alterar o conjunto de e seus metadados usando as ferramentas na interface visual:

- Use [Editar metadados](edit-metadata.md) para alterar nomes de coluna, converter uma coluna em um tipo de dados diferente ou para indicar quais colunas são rótulos ou recursos.

- Use [selecionar colunas no conjunto](select-columns-in-dataset.md) de linhas para selecionar um subconjunto de colunas.

- Use [Partition e Sample](partition-and-sample.md) para separar o conjunto de registros por critérios ou obter as n primeiras linhas.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
