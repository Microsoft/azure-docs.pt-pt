---
title: 'Importar dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de importação de dados no serviço Azure Machine Learning para carregar dados para uma experimentação do machine learning dos serviços de dados de cloud existente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028309"
---
# <a name="import-data-module"></a>Módulo de importação de dados

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para carregar dados para uma experimentação do machine learning dos serviços de dados de cloud existente.  
O módulo agora inclui um Assistente para ajudar a escolher uma opção de armazenamento e de selecionar um existente de subscrições e contas para configurar rapidamente todas as opções. Tem de editar uma ligação de dados existente? Sem problemas. o assistente carrega todos os detalhes de configuração anteriores, para que não precisa começar novamente do zero. 
  
Depois de definir os dados que pretende e liga à origem de [importar dados](./import-data.md) infere o tipo de dados de cada coluna com base nos valores contém e carrega os dados para a área de trabalho do Azure Machine Learning. A saída de [importar dados](./import-data.md) é um conjunto de dados que pode ser utilizado com qualquer experimentação.

  
Se os dados de origem forem alterados, pode atualizar o conjunto de dados e adicionar novos dados executando novamente [importar dados](./import-data.md). No entanto, se não quiser ler novamente a partir da origem sempre que executar a experimentação, selecione o **resultados em cache de utilização** opção como TRUE. Quando esta opção está selecionada, o módulo verifica se a experimentação foi executado anteriormente utilizando a mesma origem e as mesmas opções de entrada. Se for encontrada uma execução anterior, o que os dados na cache são usados, em vez de recarregar os dados da origem.
 

## <a name="data-sources"></a>Origens de dados

O módulo importar dados suporta as seguintes origens de dados. Clique nos links para obter instruções detalhadas e exemplos de como utilizar cada origem de dados. 
 
Se não tiver a certeza como ou em que deve armazenar os seus dados, consulte este guia para cenários comuns de dados no processo de ciência de dados:  [Cenários de análises avançadas no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Origem de dados| Utilizar com o|
|-----------|-----------|  
|[URL de Web por meio de HTTP](./import-from-web-url-via-http.md)|Obter dados que estão hospedados numa URL da web que utiliza HTTP e que foi fornecido pelos formatos CSV, TSV, ARFF ou SvmLight|  
|[Importar do armazenamento de Blobs do Azure](./import-from-azure-blob-storage.md) |Obter dados que são armazenados no serviço de Blobs do Azure|  

## <a name="how-to-use-import-data"></a>Como utilizar a importar dados
 
1. Adicionar a **importar dados** módulo à sua experimentação. Pode encontrar este módulo na **dados de entrada e saída** categoria na interface.

2. Clique em **iniciar o Assistente de importação de dados** para configurar a origem de dados usando um assistente.

    O assistente obtém o nome da conta e as credenciais e o ajudam a configurar outras opções. Se estiver a editar uma configuração existente, ele carrega primeiro os valores atuais.

3. Se não pretender utilizar o assistente, clique em **origem de dados**e escolha o tipo de armazenamento com base na cloud que está a ler. 

    Definições adicionais dependem do tipo de armazenamento que escolher, e se o armazenamento está protegido ou não. Poderá ter de fornecer o nome da conta, tipo de ficheiro ou as credenciais. Algumas origens não necessitam de autenticação; para outros, poderá ter de saber o nome da conta, uma chave ou o nome do contentor.

4. Selecione o **resultados em cache de utilização** opção se pretende colocar em cache o conjunto de dados para reutilização em execuções sucessivas.

    Partindo do princípio de que não tem havido nenhuma outra alteração aos parâmetros do módulo, a experimentação carrega o tempo de dados apenas o primeiro o módulo é executado e, posteriormente, utiliza uma versão em cache do conjunto de dados.

    Desmarcar esta opção se precisar de recarregar os dados sempre que executar a experimentação.

5. Execute a experimentação.

    Quando importar dados carrega os dados para a interface, ele infere o tipo de dados de cada coluna com base nos valores nele contidos, numéricos ou categóricos.

    - Se um cabeçalho estiver presente, o cabeçalho é usado para nomear as colunas do conjunto de dados de saída.

    - Se não houver nenhum cabeçalho de coluna existente nos dados, novos nomes das colunas são gerados com o formato col1, col2,... , coln *.

## <a name="results"></a>Resultados

Quando a importação estiver concluída, clique com o conjunto de dados de saída e selecione **Visualize** para ver se os dados foi importados com êxito.

Se quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados sempre que a experimentação é executada, com o botão direito a saída e selecione **guardar como conjunto de dados**. Escolha um nome para o conjunto de dados. O conjunto de dados guardado preserva os dados no momento da economia e dados não são atualizados quando a experimentação será novamente executada, mesmo que altera o conjunto de dados na experimentação. Isso pode ser útil para obtenção de instantâneos de dados.

Depois de importar os dados, pode ter alguns preparativos adicionais para modelagem e análise:


- Uso [Editar metadados](./edit-metadata.md) para alterar os nomes das colunas, para lidar com uma coluna como um tipo de dados diferentes ou para indicar que algumas colunas são etiquetas ou funcionalidades.

- Utilizar [Select Columns in Dataset](./select-columns-in-dataset.md) para selecionar um subconjunto de colunas para transformar ou utilizar na modelagem. As colunas removidas ou transformadas podem facilmente ser reintegradas ao conjunto de dados original utilizando o [adicionar colunas](./add-columns.md) módulo.  

- Uso [partição e amostras](./partition-and-sample.md) para dividir o conjunto de dados, realizar a amostragem ou obter as primeiras n linhas.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 