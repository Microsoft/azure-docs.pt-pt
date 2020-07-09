---
title: Encontre amostras de malha de tecido de serviço Azure
description: Aqui está um índice de aplicações disponíveis da amostra de malha de tecido de serviço. O código fonte nestes exemplos mostra como alcançar um determinado cenário utilizando o Modelo de Recursos de Tecido de Serviço.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75461905"
---
# <a name="find-service-fabric-mesh-samples"></a>Encontre amostras de malha de tecido de serviço

Esta tabela descreve as aplicações de amostra de malha de tecido de serviço disponíveis. O código fonte nestes exemplos mostra como alcançar um determinado cenário utilizando o Modelo de Recursos de Tecido de Serviço.

Para obter mais informações sobre a implementação de modelos diretamente para Azure, consulte a [página do modelo de amostra GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Modelo de amostra|Descrição do Cenário|Código fonte|Ferramentas de Programação|
|------------|--------------------|----------|----------------------|
| [Olá World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página estática hospedada num recipiente. Para o Linux utiliza nginx, para o Windows IIS | [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Sem requisitos |
| [Counter App para volumes de ficheiros Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Armazenar o estado através da montagem do volume baseado em Ficheiros Azure dentro do recipiente. <br><br> **Nota:** Este modelo requer que uma partilha de ficheiros Azure Files seja já fornecida [Instruções](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramenta de malha de estúdio visual |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie uma aplicação com um serviço frontal e backend que utilize resolução baseada em DNS. Usado como tutorial [aqui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Ferramenta de malha de estúdio visual |
| [App objetos visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Dimensione e atualize microserviços dentro de uma aplicação. | [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Ferramenta de malha de estúdio visual |
| [App de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Crie uma aplicação com um serviço frontend e backend que utiliza resolução baseada em DNS | [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Ferramenta de malha de estúdio visual para a versão Windows, VS Code / dotnet CLI pode ser usada para a versão Linux |
| [Counter App para volumes fiáveis de tecido de serviço](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Armazenar o estado de montagem do volume de disco fiável do tecido de serviço dentro do recipiente.| [Código fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramenta de malha de estúdio visual |
