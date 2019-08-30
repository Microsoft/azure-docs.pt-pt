---
title: 'Importar dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo importar dados no serviço de Azure Machine Learning para carregar dados em um experimento de Machine Learning de serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e8522c759efb0cc599bbcf8e7569f6045da571a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128791"
---
# <a name="import-data-module"></a>Importar módulo de dados

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para carregar dados em um experimento de Machine Learning de serviços de dados de nuvem existentes.  
Agora, o módulo apresenta um assistente para ajudá-lo a escolher uma opção de armazenamento e selecionar entre assinaturas existentes e contas para configurar rapidamente todas as opções. Precisa editar uma conexão de dados existente? Sem problemas; o assistente carrega todos os detalhes de configuração anteriores para que você não precise recomeçar do zero. 
  
Depois de definir os dados desejados e conectar-se à origem, [importar dados](./import-data.md) infere o tipo de dados de cada coluna com base nos valores que ele contém e carrega os dados em seu espaço de trabalho Azure Machine Learning. A saída de [Import data](./import-data.md) é um conjunto de dados que pode ser usado com qualquer experimento.

  
Se os dados de origem forem alterados, você poderá atualizar o conjunto de dados e adicionar novos data executando novamente os [dados de importação](./import-data.md). No entanto, se você não quiser ler novamente a origem toda vez que executar o experimento, selecione a opção **usar resultados em cache** para verdadeiro. Quando essa opção é selecionada, o módulo verifica se o experimento foi executado anteriormente usando a mesma origem e as mesmas opções de entrada. Se uma execução anterior for encontrada, os dados no cache serão usados, em vez de recarregar os dados da origem.
 

## <a name="data-sources"></a>Origens de dados

O módulo importar dados dá suporte às seguintes fontes de dados. Clique nos links para obter instruções detalhadas e exemplos de como usar cada fonte de dados. 
 
Se você não tiver certeza de como ou onde deve armazenar seus dados, consulte este guia para cenários de dados comuns no processo de ciência de dados:  [Cenários para análise avançada no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Origem de dados| Usar com|
|-----------|-----------|  
|[URL da Web via HTTP](./import-from-web-url-via-http.md)|Obter dados hospedados em uma URL da Web que usa HTTP e que foi fornecido nos formatos CSV, TSV, ARFF ou SvmLight|  
|[Importar do armazenamento de BLOBs do Azure](./import-from-azure-blob-storage.md) |Obter dados armazenados no serviço blob do Azure|  

## <a name="how-to-use-import-data"></a>Como usar os dados de importação
 
1. Adicione o módulo **importar dados** ao seu experimento. Você pode encontrar esse módulo na categoria **entrada e saída de dados** na interface.

2. Clique em **iniciar o assistente de importação de dados** para configurar a fonte de dados usando um assistente.

    O assistente Obtém o nome e as credenciais da conta e ajuda você a configurar outras opções. Se você estiver editando uma configuração existente, ela carregará os valores atuais primeiro.

3. Se você não quiser usar o assistente, clique em **fonte de dados**e escolha o tipo de armazenamento baseado em nuvem do qual você está lendo. 

    As configurações adicionais dependem do tipo de armazenamento escolhido e se o armazenamento está protegido ou não. Talvez seja necessário fornecer o nome da conta, o tipo de arquivo ou as credenciais. Algumas fontes não exigem autenticação; para outros, talvez seja necessário saber o nome da conta, uma chave ou um nome de contêiner.

4. Selecione a opção **usar resultados em cache** se você quiser armazenar em cache o conjunto de armazenamento para reutilização em execuções sucessivas.

    Supondo que não houvesse nenhuma outra alteração nos parâmetros do módulo, o experimento carrega os dados somente na primeira vez em que o módulo é executado e depois usa uma versão armazenada em cache do conjunto de dados.

    Desmarque essa opção se precisar recarregar os dados cada vez que executar o experimento.

5. Execute a experimentação.

    Quando os dados de importação carregam os dados na interface, ele infere o tipo de dados de cada coluna com base nos valores que ele contém, numéricos ou categóricos.

    - Se um cabeçalho estiver presente, o cabeçalho será usado para nomear as colunas do conjunto de resultados de saída.

    - Se não houver nenhum cabeçalho de coluna existente nos dados, novos nomes de coluna serão gerados usando o formato Col1, Col2,... , coln*.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com êxito.

Se você quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados cada vez que o experimento for executado, clique com o botão direito do mouse na saída e selecione **salvar como DataSet**. Escolha um nome para o conjunto de um. O DataSet salvo preserva os dados no momento do salvamento, e os dados não são atualizados quando o experimento é executado novamente, mesmo se o conjunto do dados no experimento for alterado. Isso pode ser útil para tirar instantâneos de dados.

Depois de importar os dados, talvez sejam necessários alguns preparativos adicionais para modelagem e análise:


- Use [Editar metadados](./edit-metadata.md) para alterar nomes de coluna, para manipular uma coluna como um tipo de dados diferente ou para indicar que algumas colunas são rótulos ou recursos.

- Use [selecionar colunas no conjunto](./select-columns-in-dataset.md) de linhas para selecionar um subconjunto de colunas para transformar ou usar na modelagem. As colunas transformadas ou removidas podem ser facilmente reassociadas ao conjunto de os originais usando o módulo [adicionar colunas](./add-columns.md) .  

- Use [Partition e Sample](./partition-and-sample.md) para dividir o conjunto de registros, executar amostragem ou obter as n primeiras linhas.

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 