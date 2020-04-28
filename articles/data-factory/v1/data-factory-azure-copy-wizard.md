---
title: Data Factory Azure Copy Wizard
description: Saiba como utilizar o Data Factory Azure Copy Wizard para copiar dados de fontes de dados suportadas para afundar.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74930123"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de cópia da fábrica de dados azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O Assistente de Cópia de Cópia de Fábrica de Dados Azure facilita o processo de ingestão de dados, que é geralmente um primeiro passo num cenário de integração de dados de ponta a ponta. Ao passar pelo Assistente de Cópia da Fábrica de Dados Azure, não precisa de compreender quaisquer definições jSON para serviços ligados, conjuntos de dados e oleodutos. O assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionadas para o destino selecionado. Além disso, o Copy Wizard ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria. Isto poupa tempo, especialmente quando está a ingerir dados pela primeira vez a partir da fonte de dados. Para iniciar o Copy Wizard, clique no azulejo de **dados da Cópia** na página inicial da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Projetado para big data
Este assistente permite-lhe mover facilmente dados de uma grande variedade de fontes para destinos em minutos. Depois de passar pelo assistente, é criado automaticamente um pipeline com uma atividade de cópia, juntamente com entidades dependentes da Data Factory (serviços ligados e conjuntos de dados). Não são necessários passos adicionais para criar o gasoduto.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para instruções passo a passo para criar um pipeline de amostra para copiar dados de uma bolha Azure para uma tabela de bases de dados Azure SQL, consulte o [tutorial copy wizard](data-factory-copy-data-wizard-tutorial.md).
>
>

O assistente é projetado com grandes dados em mente desde o início, com suporte para diversos tipos de dados e objetos. Pode ser autor de pipelines data Factory que movem centenas de pastas, ficheiros ou tabelas. O assistente suporta a pré-visualização automática de dados, a captura e o mapeamento do esquema e a filtragem de dados.

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
Pode pré-visualizar parte dos dados a partir da fonte de dados selecionada para validar se os dados são o que pretende copiar. Além disso, se os dados de origem estiverem num ficheiro de texto, o Copy Wizard analisa automaticamente o ficheiro de texto para aprender os delimitadores de linha e coluna e o esquema.

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de schema
O esquema dos dados de entrada não pode corresponder ao esquema dos dados de saída em alguns casos. Neste cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino.

> [!TIP]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL no Azure SQL Data Warehouse, se a tabela não existir na loja de destino, a Data Factory suporta a criação de tabelas automáticas utilizando o esquema da fonte. Saiba mais a partir [de Move data de e para o Azure SQL Data Warehouse usando a Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Utilize uma lista de lançamentos para selecionar uma coluna do esquema de origem para mapear para uma coluna no esquema de destino. O Assistente de Cópia tenta compreender o seu padrão de mapeamento de colunas. Aplica o mesmo padrão ao resto das colunas, para que não seja necessário selecionar cada uma das colunas individualmente para completar o mapeamento do esquema. Se preferir, pode anular estes mapeamentos utilizando as listas de abandono para mapear as colunas uma a uma. O padrão torna-se mais preciso à medida que mapeia mais colunas. O Copy Wizard atualiza constantemente o padrão e, em última análise, atinge o padrão certo para o mapeamento da coluna que pretende alcançar.     

![Mapeamento de schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar dados
Pode filtrar os dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados do lavatório. A filtragem reduz o volume dos dados a copiar para o depósito de dados do lavatório e, portanto, aumenta a produção da operação de cópia. Fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando a linguagem de consulta SQL, ou ficheiros numa pasta de blob Azure utilizando [funções e variáveis da Fábrica](data-factory-functions-variables.md)de Dados.   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
A imagem seguinte mostra uma consulta `Text.Format` SQL utilizando a função e `WindowStart` a variável.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de blob Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada no tempo de execução com base em [variáveis](data-factory-functions-variables.md#data-factory-system-variables)do sistema . As variáveis suportadas são: **{year}**, **{month}** **{ day}** **{ hour}**{ **minute} { minute}** e **{custom}**. Por exemplo: inputfolder/{year}/{month}/{day}.

Suponha que tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **'Navegar'** para **Ficheiro ou pasta,** navegue numa destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Devia ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{year}**, **03** por **{month}**, **01** por **{day}**, e **02** com **{hour}**, e prima a tecla **Tab.** Deve ver listas de drop-down para selecionar o formato para estas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como mostra a seguinte imagem, também pode usar uma variável **personalizada** e quaisquer [cordas de formato suportadas](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, utilize primeiro o botão **Browse.** Em seguida, substitua um valor por **{custom}**, e prima a tecla **Tab** para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou em horário (de hora em hora, diariamente e assim por diante). Ambas as opções podem ser usadas para a amplitude dos conectores em ambientes, incluindo no local, nuvem e cópia local do ambiente de trabalho.

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e qualquer formato suportado. A cópia programada permite-lhe copiar dados sobre uma recorrência prescrita. Pode utilizar configurações ricas (como retry, timeout e alertas) para configurar a cópia programada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para uma rápida passagem pela utilização do Assistente de Cópia de Fábrica de Dados para criar um pipeline com copy activity, consulte [Tutorial: Crie um pipeline utilizando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).
