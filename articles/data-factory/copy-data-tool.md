---
title: Ferramenta de dados de cópia Azure Data Factory
description: Fornece informações sobre a ferramenta Dados de Cópia na UI da Fábrica de Dados Azure
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372999"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Ferramenta Copiar Dados no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A ferramenta Azure Data Factory Copy Data facilita e otimiza o processo de ingestão de dados num lago de dados, o que é geralmente um primeiro passo num cenário de integração de dados de ponta a ponta.  Poupa tempo, especialmente quando utiliza a Azure Data Factory para ingerir dados a partir de uma fonte de dados pela primeira vez. Alguns dos benefícios da utilização desta ferramenta são:

- Ao utilizar a ferramenta Azure Data Factory Copy Data, não necessita de compreender as definições da Data Factory para serviços ligados, conjuntos de dados, oleodutos, atividades e gatilhos. 
- O fluxo da ferramenta Copy Data é intuitivo para carregar dados num lago de dados. A ferramenta cria automaticamente todos os recursos necessários para a Data Factory para copiar dados da loja de dados de origem selecionada para a loja de dados destino/pia selecionada. 
- A ferramenta Dados de Cópia ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria, o que o ajuda a evitar eventuais erros no início.
- Se precisar de implementar uma lógica de negócio complexa para carregar dados num lago de dados, ainda pode editar os recursos da Data Factory criados pela ferramenta Dados de Cópia, utilizando a autoria por atividade na UI da Data Factory. 

O quadro a seguir fornece orientações sobre quando utilizar a ferramenta Dados de Cópia vs. autoria por atividade na UI da Fábrica de Dados: 

| Ferramenta Copiar Dados | Por atividade (atividade de cópia) autoria |
| -------------- | -------------------------------------- |
| Pretende construir facilmente uma tarefa de carregamento de dados sem aprender sobre as entidades da Azure Data Factory (serviços ligados, conjuntos de dados, oleodutos, etc.) | Você quer implementar uma lógica complexa e flexível para carregar dados no lago. |
| Você quer carregar rapidamente um grande número de artefactos de dados em um lago de dados. | Pretende emcorrê-la a atividade copy com atividades subsequentes para limpeza ou processamento de dados. |

Para iniciar a ferramenta Dados de Cópia, clique no azulejo **de Dados** de Cópia na página inicial da sua fábrica de dados.

![Começar página - link para ferramenta De Copiar Dados](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Fluxo intuitivo para carregar dados em um lago de dados
Esta ferramenta permite-lhe mover facilmente dados de uma grande variedade de fontes para destinos em minutos com um fluxo intuitivo:  

1. Configurar as definições para a **fonte**.
2. Configurar as definições para o **destino**. 
3. Configurar **definições avançadas** para a operação de cópia, tais como mapeamento de colunas, definições de desempenho e definições de tolerância a falhas. 
4. Especifique um **calendário** para a tarefa de carregamento de dados. 
5. Resumo de **revisão** das entidades da Data Factory a criar. 
6. **Edite** o pipeline para atualizar as definições para a atividade da cópia, conforme necessário. 

   A ferramenta é projetada com grandes dados em mente desde o início, com suporte para diversos tipos de dados e objetos. Pode usá-lo para mover centenas de pastas, ficheiros ou tabelas. A ferramenta suporta a pré-visualização automática de dados, captura de esquemas e mapeamento automático, e filtragem de dados também.

![Ferramenta Copiar Dados](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
Pode pré-visualizar parte dos dados da loja de dados de origem selecionada, o que lhe permite validar os dados que estão a ser copiados. Além disso, se os dados de origem estiverem num ficheiro de texto, a ferramenta Dados de Cópia analisa o ficheiro de texto para detetar automaticamente os delimiters de linha e colunas e o esquema.

![Definições de ficheiros](./media/copy-data-tool/file-format-settings.png)

Após a deteção:

![Definições de ficheiros detetadas e pré-visualização](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura de esquema e mapeamento automático
O esquema da fonte de dados pode não ser o mesmo que o esquema do destino dado em muitos casos. Neste cenário, é necessário mapear colunas do esquema de origem para colunas a partir do esquema de destino.

A ferramenta Dados de Cópia monitoriza e aprende o seu comportamento quando está a mapear colunas entre as lojas de origem e destino. Depois de escolher uma ou algumas colunas da loja de dados de origem e mapeá-las para o esquema de destino, a ferramenta Copy Data começa a analisar o padrão para pares de colunas que escolheu de ambos os lados. Depois, aplica o mesmo padrão ao resto das colunas. Portanto, você vê todas as colunas foram mapeadas para o destino de uma forma que você deseja apenas após vários cliques.  Se não estiver satisfeito com a escolha do mapeamento de colunas fornecido pela ferramenta Copy Data, pode ignorá-lo e continuar a mapear manualmente as colunas. Enquanto isso, a ferramenta Dados de Cópia aprende e atualiza constantemente o padrão e, em última análise, atinge o padrão certo para o mapeamento da coluna que pretende alcançar. 

> [!NOTE]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL para a Azure Synapse Analytics, se a tabela não existir na loja de destino, a ferramenta Copy Data suporta a criação da tabela automaticamente utilizando o esquema de origem. 

## <a name="filter-data"></a>Filtrar dados
Pode filtrar dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados da pia. A filtragem reduz o volume dos dados a copiar para a loja de dados da pia e, portanto, aumenta o rendimento da operação de cópia. A ferramenta Copy Data fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando o idioma de consulta SQL, ou ficheiros numa pasta de blob Azure. 

### <a name="filter-data-in-a-database"></a>Filtrar dados numa base de dados
A imagem que se segue mostra uma consulta SQL para filtrar os dados.

![Filtrar dados numa base de dados](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar dados numa pasta de bolhas Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta. As variáveis suportadas são: **{ano}**, **{mês}** **{dia}** **{hora}** e **{minute}**. Por exemplo: inputfolder/{year}/{month}/{day}. 

Suponha que tenha pastas de entrada no seguinte formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **procurar** **ficheiro ou pasta,** navegue por uma destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Deverá ver 2016/03/01/02 na caixa de texto. 

Em seguida, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{day}** e **02** por **{hora}**, e prima a tecla **Do separador.** Deve ver listas de drop-down para selecionar o formato destas quatro variáveis:

![Filtro ou pasta](./media/copy-data-tool/filter-file-or-folder.png)

A ferramenta Dados de Cópia gera parâmetros com expressões, funções e variáveis de sistema que podem ser usadas para representar {year}, {month}, {day}, {hour}, e {minute} ao criar o pipeline.

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou num horário (de hora em hora, diariamente e assim por diante). Estas opções podem ser usadas para os conectores em diferentes ambientes, incluindo no local, nuvem e ambiente de trabalho local. 

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e de qualquer formato suportado. A cópia agendada permite-lhe copiar dados sobre uma recorrência que especifique. Pode utilizar configurações ricas (como retrasar, cronometragem e alertas) para configurar a cópia agendada.

![Opções de agendamento](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Passos seguintes
Experimente estes tutoriais que utilizam a ferramenta Dados de Cópia:

- [Quickstart: criar uma fábrica de dados utilizando a ferramenta Dados de Cópia](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: copiar dados em Azure utilizando a ferramenta Dados de Cópia](tutorial-copy-data-tool.md) 
- [Tutorial: copiar dados no local para a Azure utilizando a ferramenta Dados de Cópia](tutorial-hybrid-copy-data-tool.md)
