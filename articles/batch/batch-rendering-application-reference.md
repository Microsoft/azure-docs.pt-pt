---
title: Usar aplicativos de renderização – lote do Azure
description: Como usar aplicativos de renderização com o lote do Azure. Este artigo fornece uma breve descrição de como executar cada aplicativo de renderização.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390488"
---
# <a name="rendering-applications"></a>Renderizando aplicativos

Os aplicativos de renderização são usados pela criação de trabalhos e tarefas em lotes. A propriedade da linha de comando da tarefa especifica a linha de comando e os parâmetros apropriados.  A maneira mais fácil de criar as tarefas de trabalho é usar os modelos de Batch Explorer, conforme especificado neste [artigo](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Os modelos podem ser exibidos e versões modificadas criadas se necessário.

Este artigo fornece uma breve descrição de como executar cada aplicativo de renderização.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderizando com o Autodesk 3ds Max

### <a name="renderer-support"></a>Suporte a processador

Além dos renderizadores criados no 3ds Max, os seguintes renderizadores estão disponíveis nas imagens de VM de renderização e podem ser referenciados pelo arquivo de cena do 3ds Max:

* Autodesk Arnold
* V-Ray da Chaos Group

### <a name="task-command-line"></a>Linha de comando da tarefa

Invoque o aplicativo `3dsmaxcmdio.exe` para executar a renderização de linha de comando em um nó de pool.  Esse aplicativo está no caminho quando a tarefa é executada. O aplicativo `3dsmaxcmdio.exe` tem os mesmos parâmetros disponíveis que o aplicativo `3dsmaxcmd.exe`, que está documentado na [documentação de ajuda do 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (renderização | Seção de renderização da linha de comando).

Por exemplo:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Notas:

* É preciso tomar muito cuidado para garantir que os arquivos de ativo sejam encontrados.  Verifique se os caminhos estão corretos e relativos usando a janela de **acompanhamento de ativos** ou use o parâmetro `-bitmapPath` na linha de comando.
* Veja se há problemas com a renderização, como a incapacidade de localizar ativos, verificando o arquivo de `stdout.txt` gravado por 3ds Max quando a tarefa é executada.

### <a name="batch-explorer-templates"></a>Modelos de Batch Explorer

Os modelos de pool e de trabalho podem ser acessados por meio da **Galeria** no batch Explorer.  Os arquivos de origem do modelo estão disponíveis no [repositório de dados de batch Explorer no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Renderizando com Autodesk Maya

### <a name="renderer-support"></a>Suporte a processador

Além dos renderizadores criados no Maya, os seguintes renderizadores estão disponíveis no renderizando imagens de VM e podem ser referenciados pelo arquivo de cena do 3ds Max:

* Autodesk Arnold
* V-Ray da Chaos Group

### <a name="task-command-line"></a>Linha de comando da tarefa

O processador de linha de comando `renderer.exe` é usado na linha de comando da tarefa. O processador de linha de comando está documentado na [ajuda do Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

No exemplo a seguir, uma tarefa de preparação de trabalho é usada para copiar os arquivos de cena e ativos para o diretório de trabalho de preparação do trabalho, uma pasta de saída é usada para armazenar a imagem de renderização e o quadro 10 é renderizado.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização de x-ray, o arquivo de cena Maya normalmente especificaria o V-Ray como o renderizador.  Ele também pode ser especificado na linha de comando:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização Arnold, o arquivo de cena Maya normalmente especificaria Arnold como o renderizador.  Ele também pode ser especificado na linha de comando:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Modelos de Batch Explorer

Os modelos de pool e de trabalho podem ser acessados por meio da **Galeria** no batch Explorer.  Os arquivos de origem do modelo estão disponíveis no [repositório de dados de batch Explorer no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Passos seguintes

Use o pool e os modelos de trabalho do [repositório de dados no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) usando batch Explorer.  Quando necessário, crie novos modelos ou modifique um dos modelos fornecidos.