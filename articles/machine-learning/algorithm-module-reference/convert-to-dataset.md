---
title: 'Converter em DataSet: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo converter para conjunto de dados no serviço de Azure Machine Learning para converter a entrada de dados no formato de conjunto de dado interno usado pelo Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516256"
---
# <a name="convert-to-dataset"></a>Converter em conjunto de um

Este artigo descreve como usar o módulo [converter para conjunto](convert-to-dataset.md) de dados no designer de Azure Machine Learning (versão prévia) para converter todos os dados de um pipeline para o formato interno usado pelo designer.
  
A conversão não é necessária na maioria dos casos porque Azure Machine Learning converte implicitamente os dados em seu formato de conjunto do dados nativo quando qualquer operação é executada nos dados. 

No entanto, salvar dados no formato do conjunto de dados é recomendado se você tiver realizado algum tipo de normalização ou limpeza em um conjunto e desejar garantir que as alterações sejam usadas em pipelines adicionais.  
  
> [!NOTE]
> [Converter em DataSet](convert-to-dataset.md) altera apenas o formato dos dados. Ele não salva uma nova cópia dos dados no espaço de trabalho. Para salvar o conjunto de resultados, clique duas vezes na porta de saída, selecione **salvar como conjunto de conjuntos**e digite um novo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Como usar Convert para DataSet  

Recomendamos que você use o módulo [Editar metadados](edit-metadata.md) para preparar o conjunto de um antes [de usar converter em conjunto de](convert-to-dataset.md)um.  Você pode adicionar ou alterar nomes de colunas, ajustar tipos de dados e assim por diante.

1.  Adicione o módulo [converter no conjunto](convert-to-dataset.md) de módulos ao seu pipeline. Você pode encontrar esse módulo na categoria **Data Transformation** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de um DataSet.   

    Contanto que os dados sejam [tabulares](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), você pode convertê-los em um DataSet. Isso inclui os dados carregados usando [importar dados](import-data.md), os dados criados usando [inserir dados manualmente](enter-data-manually.md)ou os DataSets transformados usando [aplicar transformação](apply-transformation.md).

3.  Na lista suspensa de **ações** , indique se você deseja fazer qualquer limpeza nos dados antes de salvar o DataSet:  
  
    - **Nenhum**: Use os dados como estão.  
  
    - **Setmissingvalue**: define um valor específico para o valor ausente no conjunto de informações. O espaço reservado padrão é o caractere de ponto de interrogação (?), mas você pode usar a opção **valor ausente personalizado** para digitar um valor diferente. Por exemplo, se você digitar "táxi" para o **valor ausente personalizado**, todo o "táxi" no conjunto de valores será alterado para valor ausente.
  
    - **ReplaceValues**: Use essa opção para especificar um único valor exato a ser substituído por qualquer outro valor exato.  Você pode substituir valores ausentes ou valores personalizados definindo o método de **substituição** :- **ausente**: escolha esta opção para substituir os valores ausentes no conjunto de dados de entrada. Para **novo valor**, digite o valor com o qual substituir os valores ausentes.
            - **personalizado**: escolha esta opção para substituir valores personalizados no conjunto de dados de entrada. Para **valor personalizado**, digite o valor que você deseja localizar. Por exemplo, supondo que seus dados contenham a cadeia de caracteres `obs` usada como um espaço reservado para valores ausentes, você digitaria `obs`. Para **novo valor**, digite o novo valor com o qual substituir a cadeia de caracteres original.
  
    Observe que a operação **ReplaceValues** se aplica somente a correspondências exatas. Por exemplo, essas cadeias de caracteres não seriam afetadas: `obs.`, `obsolete`.  
 
  
5.  Execute o pipeline ou clique com o botão direito do mouse no módulo [converter para conjunto](convert-to-dataset.md) de e selecione **executar selecionado**.  

## <a name="results"></a>Resultados

+  Para salvar o conjunto de resultados resultante com um novo nome, clique com o botão direito do mouse na saída de [Convert to DataSet](convert-to-dataset.md) e selecione **Save as DataSet**.  
  
## <a name="technical-notes"></a>Notas técnicas  

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

-   Qualquer módulo que usa um conjunto de dados como entrada também pode usar os dado no CSV ou TSV. Antes que qualquer código de módulo seja executado, o pré-processamento das entradas é executado, o que equivale a executar o módulo [converter para conjunto](convert-to-dataset.md) de dados na entrada.  
  
-   Não é possível converter do formato SVMLight em DataSet.  
  
-   Ao especificar uma operação de substituição personalizada, a operação de pesquisa e substituição aplica-se a valores completos; correspondências parciais não são permitidas. Por exemplo, você pode substituir um 3 por um-1 ou por 33, mas não pode substituir um 3 em um número de dois dígitos, como 35.  
  
-   Para operações de substituição personalizadas, a substituição falhará silenciosamente se você usar como uma substituição de qualquer caractere que não esteja de acordo com o tipo de dados atual da coluna.  

  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
