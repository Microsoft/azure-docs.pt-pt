---
title: Otimizar o Visual Studio para o Azure Service Fabric malha
description: Este artigo mostra como otimizar o desempenho do Visual Studio para projetos de malha Service Fabric de forma que sua primeira execução de depuração (F5) seja muito mais rápida.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497972"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Visual Studio para projetos de malha Service Fabric

Este artigo mostra como otimizar o desempenho do Visual Studio para projetos de malha Service Fabric de forma que sua primeira execução de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar as configurações do Visual Studio
 
No Visual Studio, em **ferramentas** > **Opções**  > **Service Fabric ferramentas de malha** > **geral**, você pode ajustar as seguintes configurações:

- Efetuar **pull de imagens do Docker necessárias no projeto aberto** faz com que sua primeira execução de depuração (F5) seja mais rápida, iniciando o processo de download da imagem durante o carregamento do projeto.  
- **Implantar aplicativo no projeto aberto** pode fazer sua primeira execução de depuração (F5) mais rápido iniciando o processo de implantação quando o projeto é aberto.  
- **Remover aplicativo no projeto fechar** recursos de recuperações (CPU, RAM) alocados para o aplicativo removendo o aplicativo de malha quando o projeto é fechado.  

Quando você vê mensagens na janela Ferramentas do Service Fabric saída que o Visual Studio está "obtendo imagens", "aquecendo" ou "removendo aplicativo", ele está em referência às configurações acima. Você pode desativar essas configurações.

## <a name="next-steps"></a>Passos seguintes

Leia o [tutorial depurar um aplicativo de malha](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)