---
title: Utilizar aplicações de composição - Azure Batch
description: Como utilizar aplicações de composição com o Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 6714d8d2a086980fce37cd2912f8ff6360558d15
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542776"
---
# <a name="rendering-applications"></a>Aplicações de composição

Aplicações de composição são utilizadas com a criação de tarefas e trabalhos de lote. A propriedade de linha de comandos de tarefas Especifica os parâmetros e de linha de comandos adequada.  A maneira mais fácil para criar as tarefas de trabalho é usar os modelos do Batch Explorer conforme especificado nas [este artigo](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Os modelos podem ser visualizados e modificadas versões criadas se necessário.

Este artigo fornece uma breve descrição de como executar cada aplicação de composição.

## <a name="rendering-with-autodesk-3ds-max"></a>Composição com o Autodesk 3ds Max

### <a name="renderer-support"></a>Suporte de processador

Além de compositores integrados do 3ds Max, os compositores seguintes estão disponíveis nas imagens VM de renderização e pode ser referenciadas pelo ficheiro de cena Max 3ds:

* Autodesk Arnold
* V-Ray da Chaos Group

### <a name="task-command-line"></a>Linha de comandos da tarefa

Invocar o `3dsmaxcmdio.exe` aplicação para efetuar a renderização de linha de comandos num nó no conjunto.  Esta aplicação está no caminho quando a tarefa é executada. O `3dsmaxcmdio.exe` aplicativo tem os mesmos parâmetros disponíveis, como o `3dsmaxcmd.exe` aplicativo, o que está documentado na [3ds Max ajuda documentação](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Renderização | Secção de Renderização da linha de comandos).

Por exemplo:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Notas:

* Tem de se tomar muito cuidado para garantir que os ficheiros de recursos são encontrados.  Certifique-se de que os caminhos são relativos e correto a utilizar o **Asset de controlo** janela ou utilize o `-bitmapPath` parâmetro na linha de comandos.
* Ver se existem problemas com a composição, por exemplo, incapacidade para localizar os recursos, verificando o `stdout.txt` ficheiro escrito por 3ds Max quando a tarefa é executada.

### <a name="batch-explorer-templates"></a>Explorador de modelos do batch

Modelos de conjunto e o trabalho podem ser acedidos a partir da **galeria** no Explorador do Batch.  Os ficheiros de origem do modelo estão disponíveis no [repositório de dados do Explorador do Batch no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Composição com o Autodesk Maya

### <a name="renderer-support"></a>Suporte de processador

Além dos compositores incorporadas no Maya, os compositores seguintes estão disponíveis nas imagens VM de renderização e podem ser referenciadas pelo ficheiro de cena Max 3ds:

* Autodesk Arnold
* V-Ray da Chaos Group

### <a name="task-command-line"></a>Linha de comandos da tarefa

O `renderer.exe` compositor da linha de comandos é utilizado na linha de comandos de tarefas. O processador da linha de comandos está documentado no [Maya ajuda](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

No exemplo a seguir, uma tarefa de preparação é usada para copiar os ficheiros de cenas e recursos para o diretório de trabalho de preparação de trabalho, uma pasta de saída é utilizada para armazenar a imagem de composição e estrutura 10 for processada.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

A opção pela composição de V-Ray, o ficheiro de cena Maya normalmente especifique V-Ray como processador.  Também pode ser especificado na linha de comando:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para Arnold composição, o ficheiro de cena Maya normalmente especifique Arnold como processador.  Também pode ser especificado na linha de comando:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Explorador de modelos do batch

Modelos de conjunto e o trabalho podem ser acedidos a partir da **galeria** no Explorador do Batch.  Os ficheiros de origem do modelo estão disponíveis no [repositório de dados do Explorador do Batch no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Passos Seguintes

Utilizar os modelos de conjunto e o trabalho do [repositório de dados no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) utilizando o Explorador do Batch.  Quando for necessário, crie novos modelos ou modifique um dos modelos fornecidos.