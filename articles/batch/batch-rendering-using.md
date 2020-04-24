---
title: Usando capacidades de renderização
description: Como utilizar as capacidades de renderização do Lote Azure. Tente utilizar a aplicação Batch Explorer, diretamente ou invocada a partir de um plug-in de aplicação de cliente.
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: 6dd7d73d2b04f6e076794ee63d0a546170782fce
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115640"
---
# <a name="using-azure-batch-rendering"></a>Utilização da renderização do Lote Azure

Existem várias formas de utilizar a renderização do Lote Azure:

* APIs:
  * Escreva código utilizando qualquer uma das APIs do Lote.  Os desenvolvedores podem integrar as capacidades do Azure Batch nas suas aplicações ou fluxos de trabalho existentes, seja em nuvem ou com base no local.
* Ferramentas de linha de comando:
  * A linha de [comando Azure](https://docs.microsoft.com/cli/azure/) ou [PowerShell](https://docs.microsoft.com/powershell/azure/overview) podem ser usadas para escrever a utilização do lote.
  * Em particular, o suporte do [modelo DE Lote CLI](https://docs.microsoft.com/azure/batch/batch-cli-templates) torna muito mais fácil criar piscinas e apresentar empregos.
* Batch Explorer UI:
  * [O Batch Explorer](https://github.com/Azure/BatchLabs) é uma ferramenta de cliente transversal que também permite que as contas do Batch sejam geridas e monitorizadas.
  * Para cada uma das aplicações de renderização, são fornecidos vários modelos de piscina e de emprego que podem ser usados para criar facilmente piscinas e para apresentar empregos.  Um conjunto de modelos está listado na UI da aplicação, com os ficheiros de modelo a serem acedidos a partir do GitHub.
  * Os modelos personalizados podem ser da autoria do zero ou os modelos fornecidos do GitHub podem ser copiados e modificados.
* Plug-ins de aplicação de clientes:
  * Estão disponíveis plug-ins que permitem que a renderização do Lote seja utilizada diretamente dentro das aplicações de design e modelação do cliente.  Os plug-ins invocam principalmente a aplicação Do Batch Explorer com informações contextuais sobre o atual modelo 3D e inclui funcionalidades para ajudar a gerir os ativos.

A melhor maneira de experimentar a renderização do Lote Azure e a forma mais simples para os utilizadores finais, que não são desenvolvedores e não especialistas do Azure, é utilizar a aplicação Batch Explorer, diretamente ou invocada a partir de um plug-in de aplicação de cliente.

## <a name="using-batch-explorer"></a>Usando o Explorador de Lotes

Para um tutorial passo a passo para utilizar o Batch Explorer para realizar a renderização, consulte o [tutorial do Liquidificador](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Baixar e Instalar

Os [downloads](https://azure.github.io/BatchExplorer/) do Batch Explorer estão disponíveis para Windows, OSX e Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Usando modelos para criar piscinas e executar empregos

Um conjunto abrangente de modelos está disponível para uso com o Batch Explorer que facilita a criação de piscinas e submete empregos para as várias aplicações de renderização sem ter que especificar todas as propriedades necessárias para criar piscinas, empregos e tarefas diretamente com O Lote.  Os modelos disponíveis no Batch Explorer são armazenados e visíveis [num repositório GitHub.](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)

![Galeria Do Explorador de Lote](./media/batch-rendering-using/batch-explorer-gallery.png)

São fornecidos modelos que atendem a todas as aplicações presentes no Marketplace que renderizam imagens VM.  Para cada aplicação existem vários modelos, incluindo modelos de piscina para atender piscinas de CPU e GPU, piscinas Windows e Linux; Os modelos de trabalho incluem a renderização completa ou liquidação de liquidação de liquidação em liquidação de liquidação em azulejo e renderização distribuída por V-Ray. O conjunto de modelos fornecidos será expandido ao longo do tempo para atender a outras capacidades do Lote, como a escala automática da piscina.

Também é possível que os modelos personalizados sejam produzidos, do zero ou modificando os modelos fornecidos. Os modelos personalizados podem ser usados selecionando o item 'Modelos Locais' na secção 'Galeria' do Batch Explorer.

### <a name="file-system-and-data-movement"></a>Sistema de ficheiros e movimento de dados

A secção 'Data' no Batch Explorer permite que os ficheiros sejam copiados entre um sistema de ficheiros local e contas de Armazenamento Azure.

## <a name="client-application-plug-ins"></a>Plug-ins de aplicação de clientes

Os plug-ins estão disponíveis para algumas das aplicações do cliente.  Os plug-ins permitem criar piscinas e postos de trabalho diretamente a partir da aplicação ou invocar o Batch Explorer.

* [Liquidificador 2.79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Liquidificador 2.8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Passos seguintes

Por exemplo, a renderização do Lote experimente os dois tutoriais:

* [Renderização utilizando o Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Compor com o Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)