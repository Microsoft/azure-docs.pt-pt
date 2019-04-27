---
title: Assistente de cópia do Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como utilizar o Assistente para copiar dados de fábrica do Azure para copiar dados de origens de dados suportadas para sinks.
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
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567678"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de cópia de fábrica de dados do Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O Assistente de cópia de fábrica de dados do Azure simplifica o processo de ingerir dados, o que é normalmente uma primeira etapa num cenário de integração de dados ponto a ponto. Ao percorrer o Assistente de cópia de fábrica de dados do Azure, não é necessário compreender quaisquer definições de JSON para os serviços ligados, conjuntos de dados e pipelines. O assistente cria automaticamente um pipeline para copiar dados de origem de dados selecionada para o destino selecionado. Além disso, o Assistente de cópia ajuda-o a validar os dados a ser ingeridos no momento da criação. Isto poupa tempo, especialmente quando que está a ingerir dados pela primeira vez da origem de dados. Para iniciar o Assistente de cópia, clique nas **copiar dados** mosaico na home page de sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Concebido para grandes volumes de dados
Este assistente permite-lhe mover facilmente dados de uma grande variedade de origens para destinos em minutos. Depois de percorrer o assistente, um pipeline com uma atividade de cópia é criado automaticamente para si, juntamente com dependentes entidades do Data Factory (serviços ligados e conjuntos de dados). Não existem passos adicionais são necessários para criar o pipeline.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter instruções passo a passo Criar um exemplo de pipeline para copiar dados de um Azure blob para uma tabela de base de dados do Azure SQL, consulte a [tutorial do Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).
>
>

O assistente foi desenvolvido com grandes volumes de dados em mente, desde o início, com o suporte de dados diversificado e tipos de objeto. Pode criar pipelines do Data Factory que mover centenas de pastas, ficheiros ou tabelas. O assistente suporta a visualização de dados automática, captura de esquema e mapeamento e a filtragem de dados.

## <a name="automatic-data-preview"></a>Pré-visualização de dados automática
Pode visualizar parte dos dados da origem de dados selecionadas para validar se os dados são o que deseja copiar. Além disso, se a origem de dados estiver num arquivo de texto, o Assistente para copiar analisa o arquivo de texto para saber os delimitadores de linha e coluna e o esquema automaticamente.

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura de esquema e de mapeamento
O esquema de dados de entrada não pode corresponder ao esquema de dados de saída em alguns casos. Neste cenário, terá de mapear colunas do esquema de origem para colunas do esquema de destino.

> [!TIP]
> Ao copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse, se a tabela não existe no arquivo de destino, o Data Factory suporta a criação de tabelas de automática com o esquema da origem. Saiba mais a partir da [mover dados para e do armazém de dados SQL do Azure com o Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Utilize uma lista pendente para selecionar uma coluna do esquema de origem para mapear a uma coluna no esquema de destino. O Assistente de cópia tenta entender no seu padrão para mapeamento de colunas. Aplica-se o mesmo padrão para o resto das colunas, para que não é necessário selecionar cada uma das colunas individualmente para concluir o mapeamento de esquema. Se preferir, pode substituir estes mapeamentos ao utilizar as listas pendentes para mapear as colunas individualmente. O padrão torna-se mais preciso como mapear o mais colunas. O Assistente para copiar atualiza constantemente o padrão e, por fim, atingir o padrão de certo para o mapeamento de colunas que pretende obter.     

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar os dados
Pode filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o arquivo de dados de sink. Filtragem reduz o volume dos dados a ser copiados para o arquivo de dados de sink e, portanto, melhora o débito de operação de cópia. Ele fornece uma maneira flexível para filtrar dados numa base de dados relacional utilizando a linguagem de consulta SQL ou os ficheiros numa pasta de Blobs do Azure utilizando [variáveis e funções de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
Captura de ecrã seguinte mostra uma consulta SQL utilizando o `Text.Format` função e `WindowStart` variável.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de Blobs do Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base na [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis suportadas são: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{personalizado}**. Por exemplo: inputfolder / {ano} / {month} / {day}.

Suponha que tem a entrada pastas no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique nas **navegue** botão para **ficheiro ou pasta**, procure uma dessas pastas (por exemplo, 2016 -> 03 -> 01-02 >) e clique em **escolha**. Deverá ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** com **{year}**, **03** com **{month}**, **01** com **{day}** , e **02** com **{hour}** e prima a **separador** chave. Deverá ver a lista pendente para selecionar o formato para este quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de ecrã seguinte, também pode utilizar um **personalizados** variável e qualquer [suportados cadeias de caracteres de formato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, utilize o **procurar** botão pela primeira vez. Em seguida, substitua um valor com **{personalizado}** e prima a **separador** chave para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando a variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia de uma vez ou com base numa agenda (por hora, diariamente, e assim por diante). As duas opções podem ser utilizadas para a amplitude dos conectores ambientes, incluindo no local, a cloud e a cópia de área de trabalho local.

Uma operação de cópia de uso individual permite o movimento de dados de uma origem para um destino apenas uma vez. Aplica-se aos dados de qualquer tamanho e qualquer formato suportado. A cópia agendada permite-lhe copiar dados numa recorrência prescrita. Pode utilizar definições avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos Seguintes
Para instruções rápidas de usar o Assistente de cópia do Data Factory para criar um pipeline com atividade de cópia, consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).
