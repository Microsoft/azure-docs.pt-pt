---
title: Copie facilmente os dados com o Copy Wizard - Azure
description: Saiba como utilizar o Assistente de Cópia da Fábrica de Dados para copiar dados de fontes de dados suportadas para afundar.
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
ms.openlocfilehash: 4ce40d1a46a6448e678a8a86812d08e9013310d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086913"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiar ou mover facilmente dados com o Assistente de Cópia da Fábrica de Dados Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


O Assistente de Cópia da Fábrica de Dados Azure é para facilitar o processo de ingestão de dados, que normalmente é um primeiro passo num cenário de integração de dados de ponta a ponta. Ao passar pelo Azure Data Factory Copy Wizard, não precisa de compreender quaisquer definições JSON para serviços ligados, conjuntos de dados e oleodutos. No entanto, depois de completar todos os passos no assistente, o assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Copy Wizard ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria, o que poupa grande parte do seu tempo, especialmente quando está a ingerir dados pela primeira vez a partir da fonte de dados. Para iniciar o Copy Wizard, clique no azulejo **de dados copy** na página inicial da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Um assistente intuitivo para copiar dados
Este assistente permite-lhe mover facilmente dados de uma grande variedade de fontes para destinos em minutos. Depois de passar pelo assistente, um pipeline com uma atividade de cópia é automaticamente criado para si juntamente com entidades dependentes da Data Factory (serviços e conjuntos de dados ligados). Não são necessários passos adicionais para criar o gasoduto.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Consulte o artigo [tutorial copy Wizard](data-factory-copy-data-wizard-tutorial.md) para obter instruções passo a passo para criar um pipeline de amostra para copiar dados de uma bolha Azure para uma tabela de base de dados Azure SQL. 
> 
> 

O assistente é projetado com grandes dados em mente desde o início. É simples e eficiente para autor de pipelines data factory que movem centenas de pastas, ficheiros ou tabelas usando o assistente de Dados de Cópia. O assistente suporta as seguintes três funcionalidades: Pré-visualização automática de dados, captura de esquemas e mapeamento e dados de filtragem. 

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
O assistente de cópia permite-lhe rever parte dos dados a partir da fonte de dados selecionada para validar se os dados são os dados certos que pretende copiar. Além disso, se os dados de origem estiverem num ficheiro de texto, o assistente de cópia analisa o ficheiro de texto para aprender delimiters de linha e coluna, e esquema automaticamente. 

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de esquemas
O esquema dos dados de entrada pode não corresponder ao esquema dos dados de saída em alguns casos. Neste cenário, é necessário mapear colunas do esquema de origem para colunas a partir do esquema de destino. 

O assistente de cópia mapeia automaticamente as colunas no esquema de origem para colunas no esquema de destino. Pode anular os mapeamentos utilizando as listas de drop-down (ou) especificar se uma coluna precisa de ser ignorada enquanto copia os dados.   

![Mapeamento de Schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar dados
O assistente permite filtrar dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados destino/pia. A filtragem reduz o volume dos dados a copiar para a loja de dados da pia e, portanto, aumenta o rendimento da operação de cópia. Fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando ficheiros de linguagem de consulta SQL (ou) numa pasta de blob Azure utilizando [funções e variáveis da Data Factory.](data-factory-functions-variables.md)   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
No exemplo, a consulta SQL utiliza a `Text.Format` função e `WindowStart` a variável. 

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de bolhas Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base em [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis suportadas são: **{year}**, **{month}** **{day}** **,{hour}**, **{minute}**, e **{custom}**. Exemplo: inputfolder/{year}/{month}/{day}.

Suponha que tenha pastas de entrada no seguinte formato:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **procurar** **ficheiro ou pasta,** navegue por uma destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Devia ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{day}** e **02** por **{hora}**, e prima a Guia. Deve ver listas de drop-down para selecionar o formato destas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como mostrado na imagem seguinte, também pode utilizar uma variável **personalizada** e quaisquer [cordas de formato suportados](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com esta estrutura, utilize primeiro o botão **Procurar.** Em seguida, substitua um valor por **{custom}** e pressione o Separador para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Suporte para dados e tipos de objetos diversos
Ao utilizar o Copy Wizard, pode mover eficientemente centenas de pastas, ficheiros ou tabelas.

![Selecione tabelas para copiar dados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou num horário (de hora em hora, diariamente e assim por diante). Ambas as opções podem ser usadas para a amplitude dos conectores em todos os locais, nuvem e cópia de ambiente de trabalho local.

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e de qualquer formato suportado. A cópia agendada permite-lhe copiar dados sobre uma recorrência prescrita. Pode utilizar configurações ricas (como retrasar, cronometragem e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para uma rápida passagem da utilização do Assistente de Cópia da Fábrica de Dados para criar um oleoduto com a Atividade de Cópia, consulte [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

