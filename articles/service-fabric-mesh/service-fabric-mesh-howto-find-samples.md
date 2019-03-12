---
title: Encontre exemplos de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba como localizar diferentes aplicativos de exemplo de malha do Service Fabric.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780887"
---
# <a name="find-service-fabric-mesh-samples"></a>Encontre exemplos de malha do Service Fabric

Esta tabela descreve os aplicativos de exemplo malha de recursos de infraestrutura do serviço disponíveis. O código-fonte nestes exemplos mostra como obter um cenário em particular com o modelo de recursos do Service Fabric.

Para obter mais informações sobre a implementação de modelos diretamente para o Azure, consulte o [página GitHub do modelo de exemplo.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)


|Modelo de exemplo|Descrição do cenário|Código-fonte|Ferramentas de Programação|
|------------|--------------------|----------|----------------------|
| [Aplicação Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página de Web estática alojada num contentor. Para Linux nginx, ele usa para o IIS do Windows | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Sem requisitos |
| [Aplicação de contadores para Volumes de ficheiros do Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Estado de Store ao montar ficheiros do Azure com a base de volume no interior do contentor. <br><br> **Nota:** Este modelo requer uma partilha de ficheiros de ficheiros do Azure para já estar aprovisionado [instruções](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | As ferramentas de malha do Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie uma aplicação com um serviço de front-end e back-end que utiliza a resolução de DNS. Utilizado como um tutorial [aqui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | As ferramentas de malha do Visual Studio |
| [Aplicação de objetos visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Dimensionamento e atualização microsserviços dentro de um aplicativo. | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  As ferramentas de malha do Visual Studio |
| [Aplicação de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Criar uma aplicação com um serviço de front-end e back-end que utiliza a resolução de DNS | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Código do Visual Studio malha de ferramentas para a versão do Windows, VS / cli do dotnet pode ser utilizada para a versão do Linux |
| [Aplicação de contadores para Volumes fiáveis do Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Estado de Store ao montar o disco fiáveis do Service Fabric com a base de volume no interior do contentor.| [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | As ferramentas de malha do Visual Studio |
