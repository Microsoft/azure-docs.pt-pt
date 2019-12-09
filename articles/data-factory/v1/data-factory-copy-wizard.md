---
title: Copiar dados facilmente com o assistente de cópia – Azure
description: Saiba mais sobre como usar o assistente de cópia de Data Factory para copiar dados de fontes de dados com suporte para coletores.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927057"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiar ou mover dados facilmente com Azure Data Factory assistente de cópia
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


O assistente de cópia do Azure Data Factory é facilitar o processo de ingestão de dados, que geralmente é a primeira etapa em um cenário de integração de dados de ponta a ponta. Ao passar pelo assistente de cópia de Azure Data Factory, você não precisa entender nenhuma definição de JSON para serviços vinculados, conjuntos de itens e pipelines. No entanto, depois de concluir todas as etapas do assistente, o assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o assistente de cópia ajuda a validar os dados que estão sendo ingeridos no momento da criação, o que economiza muito tempo, especialmente quando você está ingerindo dados pela primeira vez da fonte de dados. Para iniciar o assistente de cópia, clique no bloco **copiar dados** no Home Page de seu data Factory.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Um assistente intuitivo para copiar dados
Este assistente permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos. Depois de passar pelo assistente, um pipeline com uma atividade de cópia é criado automaticamente para você juntamente com entidades de Data Factory dependentes (serviços vinculados e conjuntos de valores). Nenhuma etapa adicional é necessária para criar o pipeline.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Confira o artigo [tutorial do assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções detalhadas para criar um pipeline de exemplo para copiar dados de um blob do Azure para uma tabela do banco de dados SQL do Azure. 
> 
> 

O assistente foi projetado com Big Data em mente desde o início. É simples e eficiente criar Data Factory pipelines que movem centenas de pastas, arquivos ou tabelas usando o assistente de Copiar Dados. O assistente dá suporte aos três recursos a seguir: visualização automática de dados, captura e mapeamento de esquema e filtragem de dados. 

## <a name="automatic-data-preview"></a>Visualização automática de dados
O assistente de cópia permite que você examine parte dos dados da fonte de dados selecionada para validar se os dados são os dados corretos que você deseja copiar. Além disso, se os dados de origem estiverem em um arquivo de texto, o assistente de cópia analisará o arquivo de texto para aprender automaticamente os delimitadores de linha e de coluna e o esquema. 

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de esquema
O esquema de dados de entrada pode não corresponder ao esquema de dados de saída em alguns casos. Nesse cenário, você precisa mapear colunas do esquema de origem para colunas do esquema de destino. 

O assistente de cópia mapeia automaticamente as colunas no esquema de origem para as colunas no esquema de destino. Você pode substituir os mapeamentos usando as listas suspensas (ou) especificar se uma coluna precisa ser ignorada ao copiar os dados.   

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar dados
O assistente permite que você filtre os dados de origem para selecionar apenas os dados que precisam ser copiados para o armazenamento de dados de destino/coletor. A filtragem reduz o volume dos dados a serem copiados para o armazenamento de dados do coletor e, portanto, melhora a taxa de transferência da operação de cópia. Ele fornece uma maneira flexível de filtrar dados em um banco de dado relacional usando arquivos de linguagem de consulta SQL (ou) em uma pasta de blob do Azure usando [funções e variáveis do data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados em um banco de dado
No exemplo, a consulta SQL usa a função `Text.Format` e `WindowStart` variável. 

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados em uma pasta de blob do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base em [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}** , **{mês}** , **{dia}** , **{hora}** , **{minuto}** e **{Custom}** . Exemplo: inputfolder/{ano}/{month}/{Day}.

Suponha que você tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **procurar** para **arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016-> 03-> 01-> 02) e clique em **escolher**. Você deve ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{year}** , **03** por **{month}** , **01** por **{Day}** e **02** por **{Hour}** e pressione Tab. Você deve ver as listas suspensas para selecionar o formato dessas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de tela a seguir, você também pode usar uma variável **personalizada** e quaisquer [cadeias de caracteres de formato com suporte](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, use o botão **procurar** primeiro. Em seguida, substitua um valor por **{Custom}** e pressione TAB para ver a caixa de texto onde você pode digitar a cadeia de caracteres de formato.     

![Usando a variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Suporte para diversos tipos de dados e objetos
Usando o assistente de cópia, você pode mover com eficiência centenas de pastas, arquivos ou tabelas.

![Selecionar tabelas das quais os dados são copiados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou em um agendamento (por hora, diariamente e assim por diante). Ambas as opções podem ser usadas para a amplitude dos conectores no local, na nuvem e na cópia da área de trabalho local.

Uma operação de cópia única permite a movimentação de dados de uma origem para um destino apenas uma vez. Ele se aplica a dados de qualquer tamanho e qualquer formato com suporte. A cópia agendada permite copiar dados em uma recorrência prescrita. Você pode usar configurações avançadas (como repetição, tempo limite e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para obter uma breve explicação do uso do assistente de Data Factory Copy para criar um pipeline com a atividade de cópia, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

