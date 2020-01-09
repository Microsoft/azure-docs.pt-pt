---
title: 'Converter em DataSet: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo converter para conjunto de dados em Azure Machine Learning para converter a entrada de dados para o formato de DataSet interno usado pelo Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a004fdf0ed01c97fe5c21cc9011c46f5e4c51648
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428579"
---
# <a name="convert-to-dataset"></a>Converter em Conjunto de Dados

Este artigo descreve como usar o módulo converter para conjunto de dados no designer de Azure Machine Learning (versão prévia) para converter todos os dados de um pipeline para o formato interno do designer.
  
A conversão não é necessária na maioria dos casos. Azure Machine Learning converte dados implicitamente em seu formato de conjunto de dado nativo quando qualquer operação é executada nos dados. 

É recomendável salvar dados no formato do conjunto de dados se você tiver realizado algum tipo de normalização ou limpeza em um conjunto e desejar garantir que as alterações sejam usadas em outros pipelines.  
  
> [!NOTE]
> Converter em DataSet altera apenas o formato dos dados. Ele não salva uma nova cópia dos dados no espaço de trabalho. Para salvar o conjunto de resultados, clique duas vezes na porta de saída, selecione **salvar como conjunto de conjuntos**e insira um novo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Como usar Convert para DataSet  

Recomendamos que você use o módulo [Editar metadados](edit-metadata.md) para preparar o conjunto de um antes de usar converter em conjunto de um. Você pode adicionar ou alterar nomes de colunas, ajustar tipos de dados e fazer outras alterações conforme necessário.

1.  Adicione o módulo converter no conjunto de módulos ao seu pipeline. Você pode encontrar esse módulo na categoria **Data Transformation** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de um DataSet.   

    Contanto que os dados sejam [tabulares](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), você pode convertê-los em um DataSet. Isso inclui os dados carregados por meio de [dados de importação](import-data.md), dados criados por meio de [dados de inserção manual](enter-data-manually.md)ou os DataSets transformados por meio da [transformação aplicar](apply-transformation.md).

3.  Na lista suspensa **ação** , indique se você deseja fazer qualquer limpeza nos dados antes de salvar o conjunto:  
  
    - **Nenhum**: Use os dados como estão.  
  
    - **Setmissingvalue**: define um valor específico para um valor ausente no conjunto de informações. O espaço reservado padrão é o caractere de ponto de interrogação (?), mas você pode usar a opção **valor ausente personalizado** para inserir um valor diferente. Por exemplo, se você inserir **táxi** para **valor ausente personalizado**, todas as instâncias de **táxi** no conjunto de valores serão alteradas para o valor ausente.
  
    - **ReplaceValues**: Use essa opção para especificar um único valor exato a ser substituído por qualquer outro valor exato. Você pode substituir valores ausentes ou personalizados definindo o método **replace** :

      - **Ausente**: escolha esta opção para substituir os valores ausentes no conjunto de dados de entrada. Para **novo valor**, insira o valor com o qual substituir os valores ausentes.
      - **Personalizado**: escolha esta opção para substituir valores personalizados no conjunto de dados de entrada. Para **valor personalizado**, insira o valor que você deseja localizar. Por exemplo, se seus dados contiverem a cadeia de caracteres `obs` usada como um espaço reservado para valores ausentes, você inserirá `obs`. Para **novo valor**, insira o novo valor com o qual substituir a cadeia de caracteres original.
  
    Observe que a operação **ReplaceValues** se aplica somente a correspondências exatas. Por exemplo, essas cadeias de caracteres não seriam afetadas: `obs.`, `obsolete`.  
 
  
5.  Execute o pipeline ou clique com o botão direito do mouse no módulo converter para conjunto de e selecione **executar selecionado**.  

## <a name="results"></a>Resultados

+  Para salvar o conjunto de resultados resultante com um novo nome, clique com o botão direito do mouse na saída de Convert to DataSet e selecione **Save as DataSet**.  
  
## <a name="technical-notes"></a>Notas técnicas  

-   Qualquer módulo que usa um conjunto de dados como entrada também pode pegar os arquivos no arquivo CSV ou no arquivo TSV. Antes de qualquer código de módulo ser executado, as entradas são pré-processados. O pré-processamento é equivalente a executar o módulo converter para conjunto de dados na entrada.  
  
-   Não é possível converter do formato SVMLight em um DataSet.  
  
-   Quando você está especificando uma operação de substituição personalizada, a operação de pesquisa e substituição aplica-se a valores completos. Correspondências parciais não são permitidas. Por exemplo, você pode substituir um 3 por um-1 ou por 33, mas não pode substituir um 3 em um número de dois dígitos, como 35.  
  
-   Para operações de substituição personalizadas, a substituição falhará silenciosamente se você usar como uma substituição de qualquer caractere que não esteja de acordo com o tipo de dados atual da coluna.  

  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
