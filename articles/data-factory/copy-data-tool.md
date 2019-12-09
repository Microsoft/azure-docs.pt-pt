---
title: Azure Data Factory ferramenta de Copiar Dados
description: Fornece informações sobre a ferramenta de Copiar Dados na interface do usuário do Azure Data Factory
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927487"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Copiar Dados ferramenta no Azure Data Factory
A ferramenta de Copiar Dados Azure Data Factory facilita e otimiza o processo de ingestão de dados em um data Lake, que geralmente é uma primeira etapa em um cenário de integração de dados de ponta a ponta.  Ele economiza tempo, especialmente quando você usa Azure Data Factory para ingerir dados de uma fonte de dados pela primeira vez. Alguns dos benefícios de usar essa ferramenta são:

- Ao usar a ferramenta de Copiar Dados de Azure Data Factory, você não precisa entender as definições de Data Factory para serviços vinculados, conjuntos de itens, pipelines, atividades e gatilhos. 
- O fluxo da ferramenta de Copiar Dados é intuitivo para carregar dados em um data Lake. A ferramenta cria automaticamente todos os recursos de Data Factory necessários para copiar dados do armazenamento de dados de origem selecionado para o armazenamento de dados de destino/coletor selecionado. 
- A ferramenta de Copiar Dados ajuda a validar os dados que estão sendo ingeridos no momento da criação, o que ajuda a evitar possíveis erros no início.
- Se você precisar implementar uma lógica de negócios complexa para carregar dados em um data Lake, ainda poderá editar os Data Factory recursos criados pela ferramenta de Copiar Dados usando a criação por atividade na interface do usuário do Data Factory. 

A tabela a seguir fornece orientação sobre quando usar a ferramenta de Copiar Dados vs. criação por atividade na interface do usuário do Data Factory: 

| Ferramenta Copiar Dados | Criação por atividade (atividade de cópia) |
| -------------- | -------------------------------------- |
| Você deseja criar facilmente uma tarefa de carregamento de dados sem saber mais sobre entidades de Azure Data Factory (serviços vinculados, DataSets, pipelines, etc.) | Você deseja implementar uma lógica complexa e flexível para carregar dados no Lake. |
| Você deseja carregar rapidamente um grande número de artefatos de dados em um data Lake. | Você deseja encadear a atividade de cópia com atividades subsequentes para limpeza ou processamento de dados. |

Para iniciar a ferramenta de Copiar Dados, clique no bloco **copiar dados** na home page do data Factory.

![Página de introdução-link para a ferramenta de Copiar Dados](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Fluxo intuitivo para carregar dados em um data Lake
Essa ferramenta permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos com um fluxo intuitivo:  

1. Defina as configurações para a **origem**.
2. Defina as configurações para o **destino**. 
3. Defina **as configurações avançadas** para a operação de cópia, como mapeamento de coluna, configurações de desempenho e configurações de tolerância a falhas. 
4. Especifique um **agendamento** para a tarefa de carregamento de dados. 
5. Examine o **Resumo** das entidades de data Factory a serem criadas. 
6. **Edite** o pipeline para atualizar as configurações da atividade de cópia, conforme necessário. 

   A ferramenta foi projetada com Big Data em mente desde o início, com suporte para diversos tipos de dados e objetos. Você pode usá-lo para mover centenas de pastas, arquivos ou tabelas. A ferramenta dá suporte à visualização automática de dados, captura de esquema e mapeamento automático e filtragem de dados também.

![Ferramenta Copiar Dados](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Visualização automática de dados
Você pode visualizar parte dos dados do armazenamento de dados de origem selecionado, o que permite validar os dados que estão sendo copiados. Além disso, se os dados de origem estiverem em um arquivo de texto, a ferramenta de Copiar Dados analisará o arquivo de texto para detectar automaticamente os delimitadores de linha e de coluna e o esquema.

![Configurações de arquivo](./media/copy-data-tool/file-format-settings.png)

Após a detecção:

![Configurações e visualização de arquivo detectados](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura de esquema e mapeamento automático
O esquema de fonte de dados pode não ser o mesmo que o esquema de destino de dados em muitos casos. Nesse cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino.

A ferramenta Copiar Dados monitora e aprende seu comportamento quando você está mapeando colunas entre repositórios de origem e de destino. Depois de escolher uma ou algumas colunas do armazenamento de dados de origem e mapeá-las para o esquema de destino, a ferramenta de Copiar Dados começará a analisar o padrão para pares de colunas que você escolheu de ambos os lados. Em seguida, ele aplica o mesmo padrão ao restante das colunas. Portanto, você vê que todas as colunas foram mapeadas para o destino de uma maneira que você deseja apenas após vários cliques.  Se você não estiver satisfeito com a escolha do mapeamento de coluna fornecido pela ferramenta Copiar Dados, poderá ignorá-lo e continuar com o mapeamento manual das colunas. Enquanto isso, a ferramenta de Copiar Dados aprende e atualiza constantemente o padrão e, por fim, atinge o padrão certo para o mapeamento de coluna que você deseja obter. 

> [!NOTE]
> Ao copiar dados de SQL Server ou de um Azure SQL Database para o Azure SQL Data Warehouse, se a tabela não existir no repositório de destino, Copiar Dados ferramenta dará suporte à criação da tabela automaticamente usando o esquema de origem. 

## <a name="filter-data"></a>Filtrar dados
Você pode filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o armazenamento de dados do coletor. A filtragem reduz o volume dos dados a serem copiados para o armazenamento de dados do coletor e, portanto, melhora a taxa de transferência da operação de cópia. Copiar Dados ferramenta fornece uma maneira flexível de filtrar dados em um banco de dado relacional usando a linguagem de consulta SQL ou os arquivos em uma pasta de blob do Azure. 

### <a name="filter-data-in-a-database"></a>Filtrar dados em um banco de dado
A captura de tela a seguir mostra uma consulta SQL para filtrar os dados.

![Filtrar dados em um banco de dado](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar dados em uma pasta de blob do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta. As variáveis com suporte são: **{year}** , **{mês}** , **{Day}** , **{Hour}** e **{minute}** . Por exemplo: inputfolder/{ano}/{month}/{Day}. 

Suponha que você tenha pastas de entrada no seguinte formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **procurar** para **arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016-> 03-> 01-> 02) e clique em **escolher**. Você deve ver 2016/03/01/02 na caixa de texto. 

Em seguida, substitua **2016** por **{year}** , **03** por **{month}** , **01** por **{Day}** e **02** por **{Hour}** e pressione a tecla **Tab** . Você deve ver as listas suspensas para selecionar o formato dessas quatro variáveis:

![Filtrar arquivo ou pasta](./media/copy-data-tool/filter-file-or-folder.png)

A ferramenta Copiar Dados gera parâmetros com expressões, funções e variáveis do sistema que podem ser usadas para representar {year}, {mês}, {Day}, {hour} e {minute} durante a criação do pipeline.

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou em um agendamento (por hora, diariamente e assim por diante). Essas opções podem ser usadas para os conectores em ambientes diferentes, incluindo a área de trabalho local, na nuvem e na empresa. 

Uma operação de cópia única permite a movimentação de dados de uma origem para um destino apenas uma vez. Ele se aplica a dados de qualquer tamanho e qualquer formato com suporte. A cópia agendada permite copiar dados em uma recorrência que você especificar. Você pode usar configurações avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Opções de agendamento](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Passos seguintes
Experimente estes tutoriais que usam a ferramenta de Copiar Dados:

- [Início rápido: criar um data factory usando a ferramenta de Copiar Dados](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: copiar dados no Azure usando a ferramenta de Copiar Dados](tutorial-copy-data-tool.md) 
- [Tutorial: copiar dados locais para o Azure usando a ferramenta de Copiar Dados](tutorial-hybrid-copy-data-tool.md)
