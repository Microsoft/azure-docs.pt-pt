---
title: Encontre amostras de malha de tecido de serviço Azure
description: Aqui está um índice de aplicações disponíveis da amostra de malha de tecido de serviço. O código fonte nestes exemplos mostra como alcançar um determinado cenário utilizando o Modelo de Recursos de Tecido de Serviço.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627033"
---
# <a name="find-service-fabric-mesh-samples"></a>Encontre amostras de malha de tecido de serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Esta tabela descreve as aplicações de amostra de malha de tecido de serviço disponíveis. O código fonte nestes exemplos mostra como alcançar um determinado cenário utilizando o Modelo de Recursos de Tecido de Serviço.

Para obter mais informações sobre a implementação de modelos diretamente para Azure, consulte a [página do modelo de amostra GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Modelo de amostra|Descrição do Cenário|Código Fonte|Ferramentas de Programação|
|------------|--------------------|----------|----------------------|
| [Olá World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página estática hospedada num recipiente. Para o Linux utiliza nginx, para o Windows IIS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Sem requisitos |
| [Counter App para volumes de ficheiros Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Armazenar o estado através da montagem do volume baseado em Ficheiros Azure dentro do recipiente. <br><br> **Nota:** Este modelo requer que uma partilha de ficheiros Azure Files seja já fornecida [Instruções](../storage/files/storage-how-to-create-file-share.md) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramenta de malha de estúdio visual |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie uma aplicação com um serviço frontal e backend que utilize resolução baseada em DNS. Usado como tutorial [aqui](./service-fabric-mesh-tutorial-create-dotnetcore.md) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Ferramenta de malha de estúdio visual |
| [App objetos visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Dimensione e atualize microserviços dentro de uma aplicação. | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Ferramenta de malha de estúdio visual |
| [App de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Crie uma aplicação com um serviço frontend e backend que utiliza resolução baseada em DNS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Ferramenta de malha de estúdio visual para a versão Windows, VS Code / dotnet CLI pode ser usada para a versão Linux |
| [Counter App para volumes fiáveis de tecido de serviço](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Armazenar o estado de montagem do volume de disco fiável do tecido de serviço dentro do recipiente.| [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramenta de malha de estúdio visual |
