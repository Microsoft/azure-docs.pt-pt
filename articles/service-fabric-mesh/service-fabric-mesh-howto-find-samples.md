---
title: Encontre amostras de malha de tecido de serviço Azure
description: Aqui está um índice de aplicações disponíveis de amostra de malha de tecido de serviço. O código fonte nestes exemplos mostra como alcançar um cenário específico usando o Modelo de Recursos de Tecido de Serviço.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75461905"
---
# <a name="find-service-fabric-mesh-samples"></a>Encontre amostras de malha de tecido de serviço

Esta tabela descreve as aplicações disponíveis de amostra de malha de tecido de serviço. O código fonte nestes exemplos mostra como alcançar um cenário específico usando o Modelo de Recursos de Tecido de Serviço.

Para obter mais informações sobre a implementação de modelos diretamente para O Azure, consulte a [página GitHub](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md) do modelo de amostra.

|Modelo de amostra|Descrição do Cenário|Código Fonte|Ferramentas de Programação|
|------------|--------------------|----------|----------------------|
| [Olá Aplicativo Mundo](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página estátática hospedada num recipiente. Para o Linux utiliza nginx, para Windows IIS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Sem requisitos |
| [Contra app para volumes de ficheiros Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Armazenar o estado através da montagem de volume baseado em Ficheiros Azure dentro do recipiente. <br><br> **Nota:** Este modelo requer uma partilha de ficheiros Azure Files para já ser provisionado [Instruções](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas de malha de estúdio visual |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie uma aplicação com um serviço frontend e backend que utilize resolução baseada em DNS. Usado como tutorial [aqui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Ferramentas de malha de estúdio visual |
| [Aplicativo de objetos visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Escala e upgrade de microserviços dentro de uma aplicação. | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Ferramentas de malha de estúdio visual |
| [App de voto](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Crie uma aplicação com um serviço frontend e backend que utilize resolução baseada em DNS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Ferramentas de malha de estúdio visual para a versão Windows, VS Code / dotnet CLI podem ser usadas para a versão Linux |
| [Contra app para volumes fiáveis de tecido de serviço](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Armazenar estado montando volume fiável de disco de tecido de serviço dentro do recipiente.| [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas de malha de estúdio visual |
