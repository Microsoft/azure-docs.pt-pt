---
title: 'Converter para conjunto de dados: referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Converte para Conjunto de Dados no Azure Machine Learning designer para converter a entrada de dados no formato de conjunto de dados interno.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3ba69a6ddf38083e5665e20081f5f909b7100024
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508491"
---
# <a name="convert-to-dataset"></a>Converter em Conjunto de Dados

Este artigo descreve como utilizar o módulo Converte para Conjunto de Dados no Azure Machine Learning designer para converter quaisquer dados para um pipeline para o formato interno do designer.
  
A conversão não é necessária na maioria dos casos. O Azure Machine Learning converte implicitamente os dados no seu formato de conjunto de dados nativo quando qualquer operação é realizada nos dados. 

Recomendamos guardar dados para o formato do conjunto de dados se tiver realizado algum tipo de normalização ou limpeza num conjunto de dados, e pretender garantir que as alterações são usadas em outros oleodutos.  
  
> [!NOTE]
> Converter para Conjunto de Dados altera apenas o formato dos dados. Não guarda uma nova cópia dos dados no espaço de trabalho. Para guardar o conjunto de dados, clique duas vezes na porta de saída, **selecione Guardar como conjunto de dados** e introduza um novo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Como utilizar Converter para conjunto de dados  

Recomendamos que utilize o módulo [editar metadados](edit-metadata.md) para preparar o conjunto de dados antes de utilizar o Converta para conjunto de dados. Pode adicionar ou alterar nomes de colunas, ajustar tipos de dados e fazer outras alterações conforme necessário.

1.  Adicione o módulo Convertendo-se ao conjunto de dados ao seu pipeline. Pode encontrar este módulo na categoria **de transformação de Dados** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   

    Enquanto os dados são [tabulares,](/python/api/azureml-core/azureml.data.tabulardataset)pode convertê-lo num conjunto de dados. Isto inclui dados carregados através [de Dados de Importação,](import-data.md)dados criados através de [Introduzir Dados Manualmente,](enter-data-manually.md)ou conjuntos de dados transformados através da [Apply Transformation](apply-transformation.md).

3.  Na lista de drop-down de **Ação,** indique se pretende fazer qualquer limpeza nos dados antes de guardar o conjunto de dados:  
  
    - **Nenhum:** Use os dados como está.  
  
    - **SetMissingValue**: Defina um valor específico para um valor em falta no conjunto de dados. O espaço reservado padrão é o personagem de ponto de interrogação (?), mas pode usar a opção  **de valor em falta** personalizada para introduzir um valor diferente. Por exemplo, se introduzir **Taxi** for **Custom missing value**, então todas as instâncias de **Taxi** no conjunto de dados serão alteradas para o valor em falta.
  
    - **Substituir Valores**: Utilize esta opção para especificar um único valor exato a ser substituído por qualquer outro valor exato. Pode substituir valores em falta ou valores personalizados definindo o método **Substituir:**

      - **Em falta**: Escolha esta opção para substituir os valores em falta no conjunto de dados de entrada. Para **Novo Valor,** insira o valor para substituir os valores em falta.
      - **Costume**: Escolha esta opção para substituir valores personalizados no conjunto de dados de entrada. Para **valor personalizado,** insira o valor que pretende encontrar. Por exemplo, se os seus dados contiverem a cadeia `obs` utilizada como espaço reservado para valores em falta, `obs` introduza . Para **novo valor,** insira o novo valor para substituir a cadeia original.
  
    Note que a operação **SubstituValues** se aplica apenas a correspondências exatas. Por exemplo, estas cordas não seriam afetadas: `obs.` `obsolete` . . .  
 
  
5.  Envie o oleoduto.  

## <a name="results"></a>Resultados

+  Para guardar o conjunto de dados resultante com um novo nome, selecione no **conjunto de dados** do registo do ícone sob o separador **Saídas** no painel direito do módulo.  
  
## <a name="technical-notes"></a>Notas técnicas  

-   Qualquer módulo que tenha um conjunto de dados como entrada também pode retirar dados no ficheiro CSV ou no ficheiro TSV. Antes de qualquer código do módulo ser executado, as entradas são pré-processadas. O préprocessamento equivale a executar o módulo Converte para Conjunto de Dados na entrada.  
  
-   Não é possível converter do formato SVMLight para um conjunto de dados.  
  
-   Quando está a especificar uma operação de substituição personalizada, a operação de busca e substituição aplica-se a valores completos. Fósforos parciais não são permitidos. Por exemplo, pode substituir um 3 por um -1 ou 33, mas não pode substituir um 3 num número de dois dígitos, como 35.  
  
-   Para operações de substituição personalizadas, a substituição falhará silenciosamente se utilizar como substituto qualquer personagem que não esteja em conformidade com o tipo de dados atual da coluna.  

  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.