---
title: Otimizar o desempenho do Visual Studio para projetos de malha do Azure Service Fabric | Documentos da Microsoft
description: Otimizar o desempenho do Visual Studio para aplicações de Mesh de recursos de infraestrutura de serviço do Azure
services: service-fabric-mesh
keywords: otimizar o desempenho de depuração
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778931"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Visual Studio para projetos de malha do Service Fabric

Este artigo mostra-lhe como otimizar o desempenho do Visual Studio para projetos de malha do Service Fabric, para que a sua depuração primeiro executar (F5) é muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar configurações do Visual Studio
 
No Visual Studio, em **ferramentas** > **opções**  > **ferramentas de malha de recursos de infraestrutura do serviço** > **geral**, pode ajustar as seguintes definições:

- **Extrair imagens de Docker necessárias no projeto aberto** torna a sua depuração primeiro executar (F5) mais rapidamente ao iniciar o processo de download da imagem, enquanto o projeto está a carregar.  
- **Implementar a aplicação no projeto aberto** pode tornar a sua depuração primeiro executar (F5) mais rapidamente ao iniciar o processo de implementação quando o projeto é aberto.  
- **Remover a aplicação no projeto fechar** reclama recursos (CPU, RAM) alocados para a aplicação ao remover a aplicação de malha, quando o projeto está fechado.  

Quando vir as mensagens na janela de saída de ferramentas do Service Fabric que o Visual Studio é "solicitar imagens", "aquecer" ou "Remover a aplicação", é no contexto das definições acima. Pode desativar estas definições.

## <a name="next-steps"></a>Passos Seguintes

Leia o [depurar um tutorial de aplicação de malha](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)