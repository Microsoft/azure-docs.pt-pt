---
title: Composição de capacidades - Azure Batch
description: Como utilizar as capacidades de composição do Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2dff44f0b5b4b02c39c4c63f23ff64d55ca9d833
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789641"
---
# <a name="using-azure-batch-rendering"></a>Usando o processamento do Azure Batch

Existem várias formas de utilizar a composição do Azure Batch:

* APIs:
  * Escreva código usando qualquer uma das APIs do Batch.  Os desenvolvedores podem integrar capacidades do Azure Batch em seus aplicativos existentes ou o fluxo de trabalho, quer na cloud ou com base no local.
* Ferramentas de linha de comandos:
  * O [linha de comandos do Azure](https://docs.microsoft.com/cli/azure/) ou [PowerShell](https://docs.microsoft.com/powershell/azure/overview) pode ser utilizado para a utilização de Batch do script.
  * Em particular, o [suporte de modelo de CLI do Batch](https://docs.microsoft.com/azure/batch/batch-cli-templates) torna muito mais fácil criar agrupamentos e submeter tarefas.
* Explorador do batch da interface do Usuário:
  * [Explorador do batch](https://github.com/Azure/BatchLabs) é uma ferramenta de cliente para várias plataformas, que também permite que as contas do Batch a ser geridos e monitorizados.
  * Para cada um dos aplicativos de composição, um número de modelos de conjunto e o trabalho é fornecidas que podem ser utilizados para facilmente criar agrupamentos e submeter tarefas.  Um conjunto de modelos é listado no aplicativo de interface do Usuário, com os arquivos de modelo que está sendo acessados a partir do GitHub.
  * Modelos personalizados podem ser criados a partir do zero ou os modelos fornecidos do GitHub, podem ser copiados e modificados.
* Aplicação de cliente plug-ins:
  * Plug-ins estão disponíveis que permitem a composição do Batch para ser utilizada a partir diretamente do design de cliente e aplicações de modelos.  Os plug-ins principalmente invocar a aplicação do Explorador do Batch com informações contextuais sobre o atual modelo 3D e inclui funcionalidades para ajudar a gerir ativos.

A melhor forma de experimentar o Azure Batch rendering e da forma mais simples para os utilizadores finais, que não são programadores e especialistas não do Azure, está a utilizar a aplicação do Explorador do Batch, ou diretamente ou invocado a partir de uma aplicação de cliente Plug-in.

## <a name="using-batch-explorer"></a>Utilizar o Explorador do Batch

Para obter um tutorial passo a passo para utilizar o Explorador do Batch para realizar o processamento consulte a [Blender tutorial](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Transferir e instalar

Explorador do batch [downloads estão disponíveis](https://azure.github.io/BatchExplorer/) para Windows, OSX e Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Utilizar modelos para criar agrupamentos e executar tarefas

Um conjunto abrangente de modelos está disponível para utilização com o Explorador do Batch que torna mais fácil criar agrupamentos e submeter tarefas para os vários aplicativos de processamento sem ter de especificar todas as propriedades necessárias para criar conjuntos, trabalhos e tarefas diretamente com Batch.  Os modelos disponíveis no Explorador do Batch são armazenadas e visíveis na [um repositório do GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galeria de Explorador do batch](./media/batch-rendering-using/batch-explorer-gallery.png)

Os modelos são fornecidos que sirva para todos os aplicativos presentes em imagens de VM de renderização do Marketplace.  Para cada aplicativo existirem vários modelos, incluindo modelos de agrupamento para se adaptar para os conjuntos de CPU e GPU, Windows e Linux conjuntos; modelos de trabalho incluem o quadro completo ou lado a lado composição Blender e processamento distribuído de V-Ray. O conjunto de modelos fornecidos será expandido ao longo do tempo para se adaptar para outros recursos do Batch, como o dimensionamento automático do conjunto.

Também é possível para modelos personalizados ser produzido, do zero ou modificando os modelos fornecidos. Pode utilizar os modelos personalizados, selecionando o item "Modelos de Local" na seção "Galeria" do Explorador do Batch.

### <a name="file-system-and-data-movement"></a>Movimento de dados e de sistema de ficheiros

A seção de "Dados" no Explorador do Batch permite que os arquivos copiados entre um sistema de arquivos local e contas de armazenamento do Azure.

## <a name="client-application-plug-ins"></a>Aplicação de cliente plug-ins

Plug-ins estão disponíveis para algumas das aplicações cliente.  Os plug-ins permitem que os conjuntos e tarefas a ser criado diretamente a partir da aplicação ou invocar o Explorador do Batch.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Passos Seguintes

Para exemplos de composição do Batch, experimente os dois tutoriais:

* [Composição com a CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Composição com o Explorador do Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)