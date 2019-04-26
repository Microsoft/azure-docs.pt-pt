---
title: Copie facilmente os dados com o Assistente para copiar - Azure | Documentos da Microsoft
description: Saiba mais sobre como utilizar o Assistente de cópia do Data Factory para copiar dados de origens de dados suportadas para sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90f78428601d7b039d00d39c1ca8339ab3ace9ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487988"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiar ou mover dados facilmente com o Assistente de cópia do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


O Assistente de cópia de fábrica de dados do Azure é facilitar o processo de ingerir dados, o que é normalmente uma primeira etapa num cenário de integração de dados ponto a ponto. Ao percorrer o Assistente de cópia de fábrica de dados do Azure, não é necessário compreender quaisquer definições de JSON para os serviços ligados, conjuntos de dados e pipelines. No entanto, depois de concluir todos os passos no assistente, o assistente cria automaticamente um pipeline para copiar dados de origem de dados selecionada para o destino selecionado. Além disso, o Assistente de cópia ajuda-o a validar os dados a ser ingeridos no momento da criação, poupa muito tempo, especialmente quando que está a ingerir dados pela primeira vez da origem de dados. Para iniciar o Assistente de cópia, clique nas **copiar dados** mosaico na home page de sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Um assistente intuitivo para copiar dados
Este assistente permite-lhe mover facilmente dados de uma grande variedade de origens para destinos em minutos. Depois de passar através do assistente, um pipeline com uma atividade de cópia é criado automaticamente para, juntamente com dependentes entidades do Data Factory (serviços ligados e conjuntos de dados). Não existem passos adicionais são necessários para criar o pipeline.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Ver [tutorial do Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) artigo para obter instruções passo a passo Criar um exemplo de pipeline para copiar dados de um Azure blob para uma tabela de base de dados do Azure SQL. 
> 
> 

O assistente foi desenvolvido com grandes volumes de dados em mente, desde o início. É simples e eficiente Criar pipelines do Data Factory que mover centenas de pastas, ficheiros ou tabelas usando o Assistente para copiar dados. O assistente suporta as três funcionalidades seguintes: Pré-visualização de dados automática, captura de esquema e mapeamento e filtrar os dados. 

## <a name="automatic-data-preview"></a>Pré-visualização de dados automática
O Assistente para copiar permite-lhe rever parte dos dados da origem de dados selecionadas para que possa validar se os dados forem os dados adequados que pretende copiar. Além disso, se a origem de dados estiver num arquivo de texto, o Assistente para copiar analisa o arquivo de texto para obter automaticamente o esquema e os delimitadores de coluna e linha. 

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura de esquema e de mapeamento
O esquema de dados de entrada não pode corresponder ao esquema de dados de saída em alguns casos. Neste cenário, terá de mapear colunas do esquema de origem para colunas do esquema de destino. 

O Assistente para copiar mapeia automaticamente colunas no esquema de origem para colunas no esquema de destino. Pode substituir os mapeamentos, utilizando as listas pendentes (ou) especificar se uma coluna tem de ser ignorada ao copiar os dados.   

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar os dados
O assistente permite-lhe filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o arquivo de dados de destino/sink. Filtragem reduz o volume dos dados a ser copiados para o arquivo de dados de sink e, portanto, melhora o débito de operação de cópia. Fornece uma forma flexível para filtrar dados numa base de dados relacional usando SQL query language (ou) arquivos numa pasta de Blobs do Azure usando [variáveis e funções de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
No exemplo, a consulta SQL utiliza a `Text.Format` função e `WindowStart` variável. 

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de Blobs do Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base na [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis suportadas são: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{personalizado}**. Exemplo: inputfolder / {ano} / {month} / {day}.

Suponha que tem a entrada pastas no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique nas **navegue** botão para **ficheiro ou pasta**, procure uma dessas pastas (por exemplo, 2016 -> 03 -> 01-02 >) e clique em **escolha**. Deverá ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** com **{year}**, **03** com **{month}**, **01** com **{day}** , e **02** com **{hour}**, e pressione Tab. Deverá ver a lista pendente para selecionar o formato para este quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de ecrã seguinte, também pode utilizar um **personalizados** variável e qualquer [suportados cadeias de caracteres de formato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, utilize o **procurar** botão pela primeira vez. Em seguida, substitua um valor com **{personalizado}**, e pressione Tab para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando a variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Suporte de dados diversificado e tipos de objeto
Ao utilizar o Assistente para copiar, com eficiência pode mover centenas de pastas, ficheiros ou tabelas.

![Selecionar tabelas a partir das quais copiar dados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia de uma vez ou com base numa agenda (por hora, diariamente, e assim por diante). As duas opções podem ser utilizadas para a amplitude dos conectores no local, na cloud e cópia de área de trabalho local.

Uma operação de cópia de uso individual permite o movimento de dados de uma origem para um destino apenas uma vez. Aplica-se aos dados de qualquer tamanho e qualquer formato suportado. A cópia agendada permite-lhe copiar dados numa recorrência prescrita. Pode utilizar definições avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos Seguintes
Para instruções rápidas de usar o Assistente de cópia do Data Factory para criar um pipeline com atividade de cópia, consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

