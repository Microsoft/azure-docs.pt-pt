---
title: Copiar dados facilmente com Copy Wizard - Azure
description: Saiba como usar o Data Factory Copy Wizard para copiar dados de fontes de dados suportadas para afundar.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74927057"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiar ou mover dados facilmente com o Assistente de Cópia da Fábrica de Dados Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


O Assistente de Cópia da Fábrica de Dados Azure é facilitar o processo de ingestão de dados, que é geralmente um primeiro passo num cenário de integração de dados de ponta a ponta. Ao passar pelo Assistente de Cópia da Fábrica de Dados Azure, não precisa de compreender quaisquer definições jSON para serviços, conjuntos de dados e oleodutos ligados. No entanto, depois de completar todos os passos do assistente, o assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Copy Wizard ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria, o que poupa grande parte do seu tempo, especialmente quando está a ingerir dados pela primeira vez a partir da fonte de dados. Para iniciar o Copy Wizard, clique no azulejo de **dados da Cópia** na página inicial da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Um assistente intuitivo para copiar dados
Este assistente permite-lhe mover facilmente dados de uma grande variedade de fontes para destinos em minutos. Depois de passar pelo assistente, é criado automaticamente um pipeline com uma atividade de cópia, juntamente com entidades dependentes da Data Factory (serviços e conjuntos de dados ligados). Não são necessários passos adicionais para criar o gasoduto.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Consulte o artigo [tutorial do Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para obter instruções passo a passo para criar um pipeline de amostra para copiar dados de uma bolha Azure para uma tabela de bases de dados Azure SQL. 
> 
> 

O assistente foi concebido com grandes dados em mente desde o início. É simples e eficiente para o autor de pipelines data Factory que movem centenas de pastas, ficheiros ou tabelas usando o assistente de dados de cópia. O assistente suporta as seguintes três funcionalidades: Pré-visualização automática de dados, captura e mapeamento de esquemas e filtragem de dados. 

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
O assistente de cópia permite-lhe rever parte dos dados a partir da fonte de dados selecionada para que validar se os dados são os dados certos que pretende copiar. Além disso, se os dados de origem estiverem num ficheiro de texto, o assistente de cópia analisa o ficheiro de texto para aprender delimitadores de linha e coluna, e esquema automaticamente. 

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de schema
O esquema dos dados de entrada não pode corresponder ao esquema dos dados de saída em alguns casos. Neste cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino. 

O assistente de cópia mapeia automaticamente as colunas no esquema de origem às colunas no esquema de destino. Pode anular os mapeamentos utilizando as listas de abandono (ou) especificar se uma coluna precisa de ser ignorada enquanto copia os dados.   

![Mapeamento de schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar dados
O assistente permite-lhe filtrar os dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados destino/pia. A filtragem reduz o volume dos dados a copiar para o depósito de dados do lavatório e, portanto, aumenta a produção da operação de cópia. Fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando ficheiros de linguagem de consulta SQL (ou) numa pasta de blob Azure utilizando [funções e variáveis da Fábrica](data-factory-functions-variables.md)de Dados .   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
No exemplo, a consulta SQL `Text.Format` utiliza `WindowStart` a função e a variável. 

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de blob Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada no tempo de execução com base em [variáveis](data-factory-functions-variables.md#data-factory-system-variables)do sistema . As variáveis suportadas são: **{year}**, **{month}** **{ day}** **{ hour}**{ **minute} { minute}** e **{custom}**. Exemplo: inputfolder/{year}/{month}/{day}.

Suponha que tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **'Navegar'** para **Ficheiro ou pasta,** navegue numa destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Devia ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{year}**, **03** por **{month}**, **01** por **{day}**, e **02** com **{hour}**, e prima Tab. Deve ver listas de drop-down para selecionar o formato para estas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como mostra a seguinte imagem, também pode usar uma variável **personalizada** e quaisquer [cordas de formato suportadas](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, utilize primeiro o botão **Browse.** Em seguida, substitua um valor por **{custom}**, e prima O separador para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Suporte para dados diversos e tipos de objetos
Ao utilizar o Assistente de Cópia, pode mover eficientemente centenas de pastas, ficheiros ou tabelas.

![Selecione tabelas a partir das quais copiar dados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou em horário (de hora em hora, diariamente e assim por diante). Ambas as opções podem ser usadas para a amplitude dos conectores através das instalações, nuvem e cópia local do ambiente de trabalho.

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e qualquer formato suportado. A cópia programada permite-lhe copiar dados sobre uma recorrência prescrita. Pode utilizar configurações ricas (como retry, timeout e alertas) para configurar a cópia programada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para uma rápida passagem pela utilização do Assistente de Cópia de Fábrica de Dados para criar um pipeline com copy activity, consulte [Tutorial: Crie um pipeline utilizando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

