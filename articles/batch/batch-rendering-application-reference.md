---
title: Utilizar aplicações de renderização
description: Como utilizar aplicações de renderização com o Azure Batch. Este artigo fornece uma breve descrição de como executar cada aplicação de renderização.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964877"
---
# <a name="rendering-applications"></a>Aplicações de renderização

As aplicações de renderização são utilizadas através da criação de empregos e tarefas do Batch. A propriedade da linha de comando de tarefa especifica a linha de comando e os parâmetros apropriados.  A forma mais fácil de criar as tarefas de trabalho é utilizar os modelos do Batch Explorer, conforme especificado [neste artigo.](./batch-rendering-using.md#using-batch-explorer)  Os modelos podem ser visualizados e modificados versões criadas se necessário.

Este artigo fornece uma breve descrição de como executar cada aplicação de renderização.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderização com Autodesk 3ds Max

### <a name="renderer-support"></a>Suporte de renderizador

Além dos renderizadores incorporados em 3ds Max, os seguintes renderizadores estão disponíveis nas imagens VM de renderização e podem ser referenciados pelo ficheiro de cena 3ds Max:

* Autodesk Arnold
* Grupo Caos V-Ray

### <a name="task-command-line"></a>Linha de comando de tarefa

Invoque a `3dsmaxcmdio.exe` aplicação para executar a renderização da linha de comando num nó de piscina.  Esta aplicação está no caminho quando a tarefa é executada. A `3dsmaxcmdio.exe` aplicação tem os mesmos parâmetros disponíveis que a `3dsmaxcmd.exe` aplicação, que está documentada na [documentação de ajuda 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (renderização / Command-Line renderização).

Por exemplo:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Notas:

* Há que ter muito cuidado para garantir que os ficheiros de ativos são encontrados.  Certifique-se de que os caminhos estão corretos e relativos utilizando a janela **de rastreio** de ativos ou utilize o parâmetro na linha `-bitmapPath` de comando.
* Veja se existem problemas com o render, como a incapacidade de encontrar ativos, verificando o `stdout.txt` ficheiro escrito por 3ds Max quando a tarefa é executada.

### <a name="batch-explorer-templates"></a>Modelos de explorador de lote

Os modelos de piscina e de trabalho podem ser acedidos a partir da **Galeria** no Batch Explorer.  Os ficheiros de origem do modelo estão disponíveis no [repositório de dados](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)do Batch Explorer no GitHub .

## <a name="rendering-with-autodesk-maya"></a>Renderização com Autodesk Maya

### <a name="renderer-support"></a>Suporte de renderizador

Além dos renderizadores incorporados em Maya, os seguintes renderizadores estão disponíveis nas imagens VM de renderização e podem ser referenciados pelo ficheiro de cena 3ds Max:

* Autodesk Arnold
* Grupo Caos V-Ray

### <a name="task-command-line"></a>Linha de comando de tarefa

O `renderer.exe` renderizador da linha de comando é utilizado na linha de comando de tarefa. O renderizador de linha de comando está documentado na [ajuda maia.](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4)

No exemplo seguinte, uma tarefa de preparação de emprego é usada para copiar os ficheiros de cena e os ativos para o diretório de trabalho de preparação de trabalho, uma pasta de saída é usada para armazenar a imagem de renderização, e o quadro 10 é renderizado.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização de V-Ray, o ficheiro da cena maia normalmente especificaria o Raio V como o renderizador.  Também pode ser especificado na linha de comando:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização de Arnold, o ficheiro da cena maia normalmente especificaria Arnold como o renderizador.  Também pode ser especificado na linha de comando:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Modelos de explorador de lote

Os modelos de piscina e de trabalho podem ser acedidos a partir da **Galeria** no Batch Explorer.  Os ficheiros de origem do modelo estão disponíveis no [repositório de dados](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)do Batch Explorer no GitHub .

## <a name="next-steps"></a>Passos seguintes

Utilize os modelos de piscina e de trabalho a partir do [repositório de dados no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) utilizando o Batch Explorer.  Quando necessário, crie novos modelos ou modifique um dos modelos fornecidos.
