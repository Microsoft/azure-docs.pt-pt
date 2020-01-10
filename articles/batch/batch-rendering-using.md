---
title: Usando funcionalidades de renderização-lote do Azure
description: Como usar os recursos de renderização do lote do Azure. Tente usar o aplicativo Batch Explorer, seja diretamente ou invocado de um plug-in de aplicativo cliente.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d181b9b2495ebd106bb068b78131fa90c2f6d62c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449669"
---
# <a name="using-azure-batch-rendering"></a>Usando a renderização de lote do Azure

Há várias maneiras de usar a renderização de lote do Azure:

* APIs:
  * Escreva código usando qualquer uma das APIs do lote.  Os desenvolvedores podem integrar os recursos do lote do Azure em seus aplicativos ou fluxo de trabalho existentes, seja na nuvem ou com base no local.
* Ferramentas de linha de comando:
  * A [linha de comando do Azure](https://docs.microsoft.com/cli/azure/) ou o [PowerShell](https://docs.microsoft.com/powershell/azure/overview) pode ser usado para gerar scripts de uso em lote.
  * Em particular, o [suporte ao modelo da CLI do lote](https://docs.microsoft.com/azure/batch/batch-cli-templates) torna muito mais fácil criar pools e enviar trabalhos.
* Interface do usuário do Batch Explorer:
  * O [batch Explorer](https://github.com/Azure/BatchLabs) é uma ferramenta de cliente de plataforma cruzada que também permite que contas do lote sejam gerenciadas e monitoradas.
  * Para cada um dos aplicativos de renderização, são fornecidos vários modelos de pool e de trabalho que podem ser usados para criar facilmente pools e para enviar trabalhos.  Um conjunto de modelos é listado na interface do usuário do aplicativo, com os arquivos de modelo que estão sendo acessados do GitHub.
  * Os modelos personalizados podem ser criados do zero ou os modelos fornecidos do GitHub podem ser copiados e modificados.
* Plug-ins de aplicativo cliente:
  * Os plug-ins estão disponíveis para permitir que a renderização em lotes seja usada diretamente dentro dos aplicativos de design e modelagem do cliente.  Os plug-ins invocam principalmente o aplicativo Batch Explorer com informações contextuais sobre o modelo 3D atual e inclui recursos para ajudar a gerenciar ativos.

A melhor maneira de experimentar a renderização do lote do Azure e a maneira mais simples para os usuários finais, que não são desenvolvedores e especialistas do Azure, é usar o aplicativo Batch Explorer, diretamente ou invocado de um plug-in de aplicativo cliente.

## <a name="using-batch-explorer"></a>Utilizar o Explorador do Batch

Para obter um tutorial passo a passo sobre como usar Batch Explorer para executar a renderização, consulte o [tutorial do Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>baixe e instale

Batch Explorer [downloads estão disponíveis](https://azure.github.io/BatchExplorer/) para Windows, OSX e Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Usando modelos para criar pools e executar trabalhos

Um conjunto abrangente de modelos está disponível para uso com Batch Explorer que facilita a criação de pools e o envio de trabalhos para os vários aplicativos de renderização sem a necessidade de especificar todas as propriedades necessárias para criar pools, trabalhos e tarefas diretamente com Lote.  Os modelos disponíveis em Batch Explorer são armazenados e visíveis em [um repositório do GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galeria de Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

São fornecidos modelos que atendem a todos os aplicativos presentes no Marketplace renderizando imagens de VM.  Para cada aplicativo, existem vários modelos, incluindo modelos de pool para atender a pools de CPU e GPU, pools do Windows e do Linux; os modelos de trabalho incluem renderização de quadro completo ou de misturador de ladrilhos e renderização distribuída de V-Ray. O conjunto de modelos fornecidos será expandido ao longo do tempo para atender a outros recursos do lote, como o dimensionamento automático do pool.

Também é possível que modelos personalizados sejam produzidos, do zero ou modificando os modelos fornecidos. Os modelos personalizados podem ser usados selecionando o item ' modelos locais ' na seção ' Galeria ' de Batch Explorer.

### <a name="file-system-and-data-movement"></a>Sistema de arquivos e movimentação de dados

A seção ' dados ' no Batch Explorer permite que os arquivos sejam copiados entre um sistema de arquivos local e contas de armazenamento do Azure.

## <a name="client-application-plug-ins"></a>Plug-ins de aplicativo cliente

Os plug-ins estão disponíveis para alguns dos aplicativos cliente.  Os plug-ins permitem que pools e trabalhos sejam criados diretamente do aplicativo ou invoquem Batch Explorer.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Passos seguintes

Para obter exemplos de renderização em lote, experimente os dois tutoriais:

* [Renderização usando o CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderizando usando Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)