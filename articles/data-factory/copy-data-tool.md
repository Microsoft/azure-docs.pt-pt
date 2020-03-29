---
title: Ferramenta de dados de cópia Azure Data Factory
description: Fornece informações sobre a ferramenta Copy Data na Azure Data Factory UI
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: a6de5c28115d3a451256cc43d26552c269ba245a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927487"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Ferramenta Copiar Dados no Azure Data Factory
A ferramenta Azure Data Copy Copy Data facilita e otimiza o processo de ingestão de dados num lago de dados, que é geralmente um primeiro passo num cenário de integração de dados de ponta a ponta.  Poupa tempo, especialmente quando se utiliza a Azure Data Factory para ingerir dados a partir de uma fonte de dados pela primeira vez. Alguns dos benefícios de usar esta ferramenta são:

- Ao utilizar a ferramenta De dados de cópia de fábrica de dados Azure Data, não precisa de compreender as definições da Data Factory para serviços, conjuntos de dados, pipelines, atividades e gatilhos. 
- O fluxo da ferramenta Copy Data é intuitivo para carregar dados num lago de dados. A ferramenta cria automaticamente todos os recursos necessários para copiar dados da loja de dados de origem selecionada para o local de destino/sumo selecionado. 
- A ferramenta Copy Data ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria, o que o ajuda a evitar eventuais erros no início.
- Se precisar de implementar uma lógica de negócio complexa para carregar dados num lago de dados, ainda pode editar os recursos da Data Factory criados pela ferramenta Copy Data utilizando a autoria por atividade na Data Factory UI. 

O quadro seguinte fornece orientações sobre quando utilizar a ferramenta Copy Data vs. per-atividade, autoria em Data Factory UI: 

| Ferramenta Copiar Dados | Por atividade (copiar atividade) autoria |
| -------------- | -------------------------------------- |
| Pretende facilmente construir uma tarefa de carregamento de dados sem aprender sobre entidades da Azure Data Factory (serviços ligados, conjuntos de dados, oleodutos, etc.) | Você quer implementar uma lógica complexa e flexível para carregar dados no lago. |
| Você quer colocar rapidamente um grande número de artefactos de dados em um lago de dados. | Pretende emcadeiar a atividade da Cópia com atividades subsequentes para a limpeza ou tratamento de dados. |

Para iniciar a ferramenta Copy Data, clique no azulejo **Copy Data** na página inicial da sua fábrica de dados.

![Começar a página - link para copy Data tool](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Fluxo intuitivo para carregar dados em um lago de dados
Esta ferramenta permite-lhe facilmente mover dados de uma grande variedade de fontes para destinos em minutos com um fluxo intuitivo:  

1. Configurar as definições para a **fonte**.
2. Configure as definições para o **destino**. 
3. Configure **configurar configurações avançadas** para o funcionamento da cópia, tais como mapeamento de colunas, definições de desempenho e definições de tolerância à falha. 
4. Especifique um **horário** para a tarefa de carregamento de dados. 
5. Resumo **summary** de revisão das entidades da Fábrica de Dados a criar. 
6. **Editar** o pipeline para atualizar as definições para a atividade da cópia, conforme necessário. 

   A ferramenta foi projetada com grandes dados em mente desde o início, com suporte para diversos tipos de dados e objetos. Pode usá-lo para mover centenas de pastas, ficheiros ou tabelas. A ferramenta suporta a pré-visualização automática de dados, a captura de esquemas e o mapeamento automático, e a filtragem de dados também.

![Ferramenta Copiar Dados](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
Pode pré-visualizar parte dos dados a partir da loja de dados de origem selecionada, o que lhe permite validar os dados que estão a ser copiados. Além disso, se os dados de origem estiverem num ficheiro de texto, a ferramenta Copy Data analisa o ficheiro de texto para detetar automaticamente os delimitadores de linha e coluna, e esquema.

![Definições de ficheiros](./media/copy-data-tool/file-format-settings.png)

Após a deteção:

![Definições de ficheiros e pré-visualização detetadas](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura de Schema e mapeamento automático
O esquema da fonte de dados pode não ser o mesmo que o esquema do destino de dados em muitos casos. Neste cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino.

A ferramenta Copy Data monitoriza e aprende o seu comportamento quando está a mapear colunas entre as lojas de origem e destino. Depois de escolher uma ou algumas colunas da loja de dados de origem e mapeá-las para o esquema de destino, a ferramenta Copy Data começa a analisar o padrão para pares de colunas que escolheu de ambos os lados. Depois, aplica o mesmo padrão ao resto das colunas. Portanto, você vê que todas as colunas foram mapeadas para o destino de uma forma que você quer logo após vários cliques.  Se não estiver satisfeito com a escolha do mapeamento de colunas fornecido pela ferramenta Copy Data, pode ignorá-lo e continuar a mapear manualmente as colunas. Entretanto, a ferramenta Copy Data aprende e atualiza constantemente o padrão e, em última análise, atinge o padrão certo para o mapeamento de colunas que pretende alcançar. 

> [!NOTE]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL no Azure SQL Data Warehouse, se a tabela não existir na loja de destino, a ferramenta Copy Data suporta a criação da tabela automaticamente utilizando o esquema de origem. 

## <a name="filter-data"></a>Filtrar dados
Pode filtrar os dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados do lavatório. A filtragem reduz o volume dos dados a copiar para o depósito de dados do lavatório e, portanto, aumenta a produção da operação de cópia. A ferramenta Copy Data fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando o idioma de consulta SQL, ou ficheiros numa pasta de blob Azure. 

### <a name="filter-data-in-a-database"></a>Filtrar dados numa base de dados
A imagem seguinte mostra uma consulta SQL para filtrar os dados.

![Filtrar dados numa base de dados](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar dados numa pasta de blob Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta. As variáveis suportadas são: **{year}**{ **month}** **{ day}**{ **hour} e** **{minute}**. Por exemplo: inputfolder/{year}/{month}/{day}. 

Suponha que tenha pastas de entrada no seguinte formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **'Navegar'** para **Ficheiro ou pasta,** navegue numa destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Deve ver 2016/03/01/02 na caixa de texto. 

Em seguida, substitua **2016** por **{year}**, **03** por **{month}**, **01** por **{day}**, e **02** com **{hour}**, e prima a tecla **Tab.** Deve ver listas de drop-down para selecionar o formato para estas quatro variáveis:

![Ficheiro ou pasta de filtro](./media/copy-data-tool/filter-file-or-folder.png)

A ferramenta Copy Data gera parâmetros com expressões, funções e variáveis do sistema que podem ser usadas para representar {year}, {month}, {day}, {hour}e {minute} ao criar o pipeline.

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou em horário (de hora em hora, diariamente e assim por diante). Estas opções podem ser usadas para os conectores em diferentes ambientes, incluindo no local, nuvem e ambiente de trabalho local. 

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e qualquer formato suportado. A cópia agendada permite-lhe copiar dados sobre uma recorrência que especifica. Pode utilizar configurações ricas (como retry, timeout e alertas) para configurar a cópia programada.

![Opções de agendamento](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Passos seguintes
Experimente estes tutoriais que utilizam a ferramenta Copy Data:

- [Quickstart: criar uma fábrica de dados utilizando a ferramenta Copy Data](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: copiar dados em Azure utilizando a ferramenta Copy Data](tutorial-copy-data-tool.md) 
- [Tutorial: copiar dados no local para o Azure utilizando a ferramenta Copy Data](tutorial-hybrid-copy-data-tool.md)
