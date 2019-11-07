---
title: Data Factory o assistente de cópia do Azure
description: Saiba mais sobre como usar o Data Factory o assistente de cópia do Azure para copiar dados de fontes de dados com suporte para coletores.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b2ec8e2cc09b6deb236bd83cb5cca99fead97279
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683219"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de cópia de Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O assistente de cópia de Azure Data Factory facilita o processo de ingestão de dados, que geralmente é a primeira etapa em um cenário de integração de dados de ponta a ponta. Ao passar pelo assistente de cópia de Azure Data Factory, você não precisa entender nenhuma definição de JSON para serviços vinculados, conjuntos de dados e pipelines. O assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o assistente de cópia ajuda a validar os dados que estão sendo ingeridos no momento da criação. Isso poupa tempo, especialmente quando você está ingerindo dados pela primeira vez da fonte de dados. Para iniciar o assistente de cópia, clique no bloco **copiar dados** no Home Page de seu data Factory.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Projetado para Big Data
Este assistente permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos. Depois de percorrer o assistente, um pipeline com uma atividade de cópia é criado automaticamente para você, juntamente com entidades de Data Factory dependentes (serviços vinculados e conjuntos de dados). Nenhuma etapa adicional é necessária para criar o pipeline.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter instruções detalhadas sobre como criar um pipeline de exemplo para copiar dados de um blob do Azure para uma tabela do banco de dados SQL do Azure, consulte o [tutorial do assistente de cópia](data-factory-copy-data-wizard-tutorial.md).
>
>

O assistente foi projetado com Big Data em mente desde o início, com suporte para diversos tipos de dados e de objeto. Você pode criar Data Factory pipelines que movem centenas de pastas, arquivos ou tabelas. O assistente dá suporte à visualização automática de dados, captura e mapeamento de esquema e filtragem de dados.

## <a name="automatic-data-preview"></a>Visualização automática de dados
Você pode visualizar parte dos dados da fonte de dados selecionada para validar se os dados são o que você deseja copiar. Além disso, se os dados de origem estiverem em um arquivo de texto, o assistente de cópia analisará o arquivo de texto para aprender automaticamente os delimitadores de linha e coluna e o esquema.

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de esquema
O esquema de dados de entrada pode não corresponder ao esquema de dados de saída em alguns casos. Nesse cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino.

> [!TIP]
> Ao copiar dados de SQL Server ou de um Azure SQL Database para o Azure SQL Data Warehouse, se a tabela não existir no repositório de destino, Data Factory suporte à criação de tabela automática usando o esquema de origem. Saiba mais em [mover dados de e para o Azure SQL data warehouse usando Azure data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Use uma lista suspensa para selecionar uma coluna do esquema de origem para mapear para uma coluna no esquema de destino. O assistente de cópia tenta entender o padrão para o mapeamento de coluna. Ele aplica o mesmo padrão ao restante das colunas, para que você não precise selecionar cada uma das colunas individualmente para concluir o mapeamento do esquema. Se preferir, você pode substituir esses mapeamentos usando as listas suspensas para mapear as colunas uma a uma. O padrão torna-se mais preciso à medida que você mapeia mais colunas. O assistente de cópia atualiza constantemente o padrão e, por fim, atinge o padrão correto para o mapeamento de coluna que você deseja obter.     

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrando dados
Você pode filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o armazenamento de dados do coletor. A filtragem reduz o volume dos dados a serem copiados para o armazenamento de dados do coletor e, portanto, melhora a taxa de transferência da operação de cópia. Ele fornece uma maneira flexível de filtrar dados em um banco de dado relacional usando a linguagem de consulta SQL, ou arquivos em uma pasta de blob do Azure usando [funções e variáveis do data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados em um banco de dado
A captura de tela a seguir mostra uma consulta SQL usando a função `Text.Format` e `WindowStart` variável.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados em uma pasta de blob do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base em [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}** , **{mês}** , **{dia}** , **{hora}** , **{minuto}** e **{Custom}** . Por exemplo: inputfolder/{ano}/{month}/{Day}.

Suponha que você tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **procurar** para **arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016-> 03-> 01-> 02) e clique em **escolher**. Você deve ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{year}** , **03** por **{month}** , **01** por **{Day}** e **02** por **{Hour}** e pressione a tecla **Tab** . Você deve ver as listas suspensas para selecionar o formato dessas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de tela a seguir, você também pode usar uma variável **personalizada** e quaisquer [cadeias de caracteres de formato com suporte](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, use o botão **procurar** primeiro. Em seguida, substitua um valor por **{Custom}** e pressione a tecla **Tab** para ver a caixa de texto onde você pode digitar a cadeia de caracteres de formato.     

![Usando a variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou em um agendamento (por hora, diariamente e assim por diante). Essas duas opções podem ser usadas para a amplitude dos conectores entre ambientes, incluindo a cópia local, na nuvem e na área de trabalho locais.

Uma operação de cópia única permite a movimentação de dados de uma origem para um destino apenas uma vez. Ele se aplica a dados de qualquer tamanho e qualquer formato com suporte. A cópia agendada permite copiar dados em uma recorrência prescrita. Você pode usar configurações avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para obter uma breve explicação do uso do assistente de Data Factory Copy para criar um pipeline com a atividade de cópia, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).
