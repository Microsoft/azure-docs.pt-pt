---
title: 'Converter para Dataset: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Converte to Dataset no Azure Machine Learning para converter a entrada de dados no formato interno de conjunto de dados utilizado pelo Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456595"
---
# <a name="convert-to-dataset"></a>Converter em Conjunto de Dados

Este artigo descreve como usar o módulo Converte to Dataset em Azure Machine Learning designer (pré-visualização) para converter quaisquer dados para um pipeline para o formato interno do designer.
  
A conversão não é necessária na maioria dos casos. O Azure Machine Learning converte implicitamente os dados para o seu formato de conjunto de dados nativo quando qualquer operação é realizada nos dados. 

Recomendamos que guarde dados para o formato dataset se tiver realizado algum tipo de normalização ou limpeza num conjunto de dados, e pretende-se garantir que as alterações são usadas noutros oleodutos.  
  
> [!NOTE]
> Converter para Dataset muda apenas o formato dos dados. Não guarda uma nova cópia dos dados no espaço de trabalho. Para guardar o conjunto de dados, clique duas vezes na porta de saída, selecione **Guardar como conjunto de dados**e introduza um novo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Como utilizar converter para Dataset  

Recomendamos que utilize o módulo [Dedados editar](edit-metadata.md) para preparar o conjunto de dados antes de utilizar o Converte para dataset. Pode adicionar ou alterar nomes de colunas, ajustar tipos de dados e fazer outras alterações conforme necessário.

1.  Adicione o módulo Converte ao Dataset no seu pipeline. Pode encontrar este módulo na categoria de **transformação** de Dados no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   

    Desde que os dados sejam [tabular,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)pode convertê-lo num conjunto de dados. Isto inclui dados carregados através de [Dados de Importação,](import-data.md)dados criados [manualmente](enter-data-manually.md)através do Enter Data , ou conjuntos de dados transformados através da [Transformação de Aplicação.](apply-transformation.md)

3.  Na lista de drop-down da **Action,** indique se pretende fazer qualquer limpeza dos dados antes de guardar o conjunto de dados:  
  
    - **Nenhum**: Utilize os dados como está.  
  
    - **SetMissingValue**: Desloque um valor específico para um valor em falta no conjunto de dados. O espaço reservado predefinido é o personagem do ponto de interrogação (?), mas pode utilizar a opção **de valor em falta Personalizada** para introduzir um valor diferente. Por exemplo, se introduzir **taxi** for **Custom faltando valor,** então todos os casos de **Taxi** no conjunto de dados serão alterados para o valor em falta.
  
    - **Substitua Valores:** Utilize esta opção para especificar um único valor exato a substituir por qualquer outro valor exato. Pode substituir valores em falta ou valores personalizados definindo o método **de substituição:**

      - **Falta**: Escolha esta opção para substituir os valores em falta no conjunto de dados de entrada. Para **Novo Valor,** insira o valor para substituir os valores em falta.
      - **Personalizado**: Escolha esta opção para substituir valores personalizados no conjunto de dados de entrada. Para **obter valor personalizado,** insira o valor que pretende encontrar. Por exemplo, se os `obs` seus dados contenham a corda `obs`utilizada como espaço reservado para valores em falta, introduza . Para **novo valor,** insira o novo valor para substituir a cadeia original.
  
    Note que a operação **ReplaceValues** se aplica apenas aos fósforos exatos. Por exemplo, estas cordas não `obs.`seriam `obsolete`afetadas: .  
 
  
5.  Submeta o oleoduto.  

## <a name="results"></a>Resultados

+  Para guardar o conjunto de dados resultante com um novo nome, selecione no conjunto de **dados do** ícone Registar sob o separador **Saídas** no painel direito do módulo.  
  
## <a name="technical-notes"></a>Notas técnicas  

-   Qualquer módulo que tome um conjunto de dados como entrada também pode levar dados no ficheiro CSV ou no ficheiro TSV. Antes de qualquer código de módulo ser executado, as inputs são pré-processadas. O pré-processamento é equivalente a executar o módulo Convert e Dataset na entrada.  
  
-   Não é possível converter do formato SVMLight para um conjunto de dados.  
  
-   Quando está a especificar uma operação de substituição personalizada, a operação de busca e substituição aplica-se a valores completos. Não são permitidos fósforos parciais. Por exemplo, pode substituir um 3 por um -1 ou por 33, mas não pode substituir um 3 num número de dois dígitos como 35.  
  
-   Para operações de substituição personalizadas, a substituição falhará silenciosamente se utilizar como substituição qualquer personagem que não esteja em conformidade com o tipo de dados atual da coluna.  

  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
