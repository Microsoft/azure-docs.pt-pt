---
title: Localizar exemplos de malha de Service Fabric do Azure | Microsoft Docs
description: Aqui está um índice de aplicativos de exemplo de malha Service Fabric disponíveis. O código-fonte nesses exemplos mostra como obter um cenário específico usando o modelo de recurso Service Fabric.
services: service-fabric-mesh
keywords: ''
author: athinanthny
ms.author: atsenthi
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: a300c06a6a18bf2e986b3736b78957daf3d7efd6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718811"
---
# <a name="find-service-fabric-mesh-samples"></a>Localizar amostras de malha Service Fabric

Esta tabela descreve os aplicativos de exemplo de malha Service Fabric disponíveis. O código-fonte nesses exemplos mostra como obter um cenário específico usando o modelo de recurso Service Fabric.

Para obter mais informações sobre como implantar modelos diretamente no Azure, consulte a [página do GitHub do modelo de exemplo.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Modelo de exemplo|Descrição do cenário|Código-fonte|Ferramentas de Programação|
|------------|--------------------|----------|----------------------|
| [Olá, Mundo aplicativo](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página da Web estática hospedada em um contêiner. Para o Linux, ele usa Nginx para o IIS do Windows | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Sem requisitos |
| [Aplicativo de contador para volumes de arquivos do Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Armazene o estado montando o volume baseado em arquivos do Azure dentro do contêiner. <br><br> **Observação:** Este modelo requer que um compartilhamento de arquivo de arquivos do Azure já tenha [instruções](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) provisionadas | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas de malha do Visual Studio |
| [Apldelistadetarefas](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie um aplicativo com um serviço de front-end e backend que usa a resolução baseada em DNS. Usado como um tutorial [aqui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Ferramentas de malha do Visual Studio |
| [Aplicativo de objetos visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Dimensione e atualize os microserviços em um aplicativo. | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Ferramentas de malha do Visual Studio |
| [Aplicativo de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Criar um aplicativo com um serviço de front-end e backend que usa a resolução baseada em DNS | [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Ferramentas de malha do Visual Studio para a versão do Windows, a CLI do VS Code/dotnet pode ser usada para a versão do Linux |
| [Aplicativo de contador para volumes Service Fabric confiáveis](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Armazene o estado montando Service Fabric volume baseado em disco confiável dentro do contêiner.| [Código-fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas de malha do Visual Studio |
