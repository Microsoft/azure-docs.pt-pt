---
title: Ferramenta de copiar dados do Azure Data Factory | Documentos da Microsoft
description: Fornece informações sobre a ferramenta copiar dados na IU do Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101759"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Ferramenta copiar dados no Azure Data Factory
A ferramenta copiar dados do Azure Data Factory simplifica e otimiza o processo de ingerir dados num data lake, que é normalmente uma primeira etapa num cenário de integração de dados ponto a ponto.  Isso economiza tempo, especialmente quando utiliza a fábrica de dados do Azure para ingerir dados de uma origem de dados pela primeira vez. Algumas das vantagens de utilizar esta ferramenta são:

- Ao utilizar a ferramenta copiar dados do Azure Data Factory, não precisa compreender as definições de fábrica de dados para serviços ligados, conjuntos de dados, pipelines, atividades e acionadores. 
- O fluxo da ferramenta copiar dados é intuitivo para carregar dados para um data lake. A ferramenta cria automaticamente todos os recursos de fábrica de dados necessários para copiar dados do arquivo de dados de origem selecionado para o arquivo de dados de destino/sink selecionado. 
- A ferramenta copiar dados ajuda-o a validar os dados que está a ser ingeridos no momento da criação, que ajuda a evitar quaisquer potenciais erros no início em si.
- Se precisar de implementar a lógica de negócio complexa para carregar dados para um data lake, ainda pode editar os recursos de fábrica de dados criados pela ferramenta copiar dados com a criação de por atividade na IU do Data Factory. 

A tabela seguinte fornece orientações sobre quando utilizar a ferramenta copiar dados vs. criação de por atividade na IU do Data Factory: 

| Ferramenta Copiar Dados | Por atividade (atividade de cópia) de criação |
| -------------- | -------------------------------------- |
| Pretende criar facilmente um tarefa sem saber mais sobre entidades do Azure Data Factory (serviços ligados, conjuntos de dados, pipelines, etc.) o carregamento de dados | Pretende implementar a lógica complexa e flexível para carregar dados para o lake. |
| Deseja carregar rapidamente um grande número de artefactos de dados para um data lake. | Quer da cadeia de atividade de cópia com as atividades subsequentes para os dados de limpeza ou processamento. |

Para iniciar a ferramenta copiar dados, clique nas **copiar dados** mosaico na home page de sua fábrica de dados.

![Página Introdução - ligação para a ferramenta copiar dados](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Fluxo intuitivo para carregar dados para um data lake
Esta ferramenta permite-lhe mover facilmente dados de uma grande variedade de origens para destinos em minutos com um fluxo de intuitivo:  

1. Configurar as definições para o **origem**.
2. Configurar as definições para o **destino**. 
3. Configurar **definições avançadas** para a operação de cópia, como o mapeamento de colunas, as definições de desempenho e as definições de tolerância a falhas. 
4. Especifique um **agenda** para os tarefa de carregamento de dados. 
5. Revisão **resumo** de entidades da fábrica de dados a ser criada. 
6. **Editar** pipeline para atualizar as definições para a atividade de cópia conforme necessário. 

   A ferramenta foi criada com grandes volumes de dados em mente, desde o início, com o suporte de dados diversificado e tipos de objeto. Pode usá-lo de mover centenas de pastas, ficheiros ou tabelas. A ferramenta suporta a visualização de dados automática, captura de esquema e mapeamento automático e filtragem de dados também.

![Ferramenta Copiar Dados](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Pré-visualização de dados automática
Pode visualizar parte dos dados do arquivo de dados de origem selecionado, que permite-lhe validar os dados que estão a ser copiados. Além disso, se a origem de dados estiver num arquivo de texto, a ferramenta copiar dados analisa o arquivo de texto para detetar automaticamente os delimitadores de linha e coluna e o esquema.

![Definições do ficheiro](./media/copy-data-tool/file-format-settings.png)

Após a deteção:

![Definições de arquivo detectado e pré-visualização](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura de esquema e mapeamento automático
O esquema da origem de dados pode não ser o mesmo que o esquema de destino dos dados em muitos casos. Neste cenário, terá de mapear colunas do esquema de origem para colunas do esquema de destino.

A ferramenta copiar dados monitoriza e aprende o seu comportamento quando o mapeamento de colunas entre arquivos de origem e de destino. Depois de escolher uma ou algumas colunas a partir do arquivo de dados de origem e mapeá-los para o esquema de destino, começa a ferramenta copiar dados analisar o padrão para os pares de coluna que é detetado a partir de ambos os lados. Em seguida, aplica-se o mesmo padrão para o resto das colunas. Por conseguinte, verá todas as colunas foram mapeadas para o destino de uma forma que pretende que apenas depois de alguns cliques.  Se não estiver satisfeito com a escolha do mapeamento de colunas fornecida pela ferramenta copiar dados, pode ignorá-lo e continuar com o mapeamento manualmente as colunas. Enquanto isso, a ferramenta copiar dados constantemente aprende e atualiza o padrão e, por fim, atinge o padrão de certo para o mapeamento de colunas que pretende obter. 

> [!NOTE]
> Quando copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse, se a tabela não existe no arquivo de destino, ferramenta copiar dados suporta a criação da tabela automaticamente ao utilizar o esquema de origem. 

## <a name="filter-data"></a>Filtrar dados
Pode filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o arquivo de dados de sink. Filtragem reduz o volume dos dados a ser copiados para o arquivo de dados de sink e, portanto, melhora o débito de operação de cópia. Ferramenta de cópia de dados fornece uma maneira flexível para filtrar dados numa base de dados relacional utilizando a linguagem de consulta SQL ou arquivos numa pasta de Blobs do Azure. 

### <a name="filter-data-in-a-database"></a>Filtrar dados numa base de dados
Captura de ecrã seguinte mostra uma consulta SQL para filtrar os dados.

![Filtrar dados numa base de dados](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar dados numa pasta de Blobs do Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta. As variáveis suportadas são: **{year}**, **{month}**, **{day}**, **{hour}**, e **{minute}**. Por exemplo: inputfolder / {ano} / {month} / {day}. 

Suponha que tem a entrada pastas no seguinte formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique nas **navegue** botão para **ficheiro ou pasta**, procure uma dessas pastas (por exemplo, 2016 -> 03 -> 01-02 >) e clique em **escolha**. Deverá ver 2016/03/01/02 na caixa de texto. 

Em seguida, substitua **2016** com **{year}**, **03** com **{month}**, **01** com **{day}** , e **02** com **{hour}** e prima a **separador** chave. Deverá ver a lista pendente para selecionar o formato para este quatro variáveis:

![Filtro de ficheiro ou pasta](./media/copy-data-tool/filter-file-or-folder.png)

A ferramenta copiar dados gera parâmetros com expressões, funções e variáveis do sistema que podem ser usadas para representar {year}, {month}, {day}, {hour} e {minuto} ao criar o pipeline. Para obter mais informações, consulte a [como ler ou escrever particionados dados](how-to-read-write-partitioned-data.md) artigo.

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia de uma vez ou com base numa agenda (por hora, diariamente, e assim por diante). Estas opções podem ser utilizadas para os conectores ambientes diferentes, incluindo no local, a cloud e a área de trabalho local. 

Uma operação de cópia de uso individual permite o movimento de dados de uma origem para um destino apenas uma vez. Aplica-se aos dados de qualquer tamanho e qualquer formato suportado. A cópia agendada permite-lhe copiar dados numa periodicidade que especificar. Pode utilizar definições avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Opções de agendamento](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Passos Seguintes
Experimente estes tutoriais que utilizam a ferramenta copiar dados:

- [Início rápido: criar uma fábrica de dados com a ferramenta copiar dados](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: copiar dados no Azure com a ferramenta copiar dados](tutorial-copy-data-tool.md) 
- [Tutorial: copiar dados no local para o Azure com a ferramenta copiar dados](tutorial-hybrid-copy-data-tool.md)
