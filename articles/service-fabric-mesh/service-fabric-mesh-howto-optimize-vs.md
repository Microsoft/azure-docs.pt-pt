---
title: Otimizar o desempenho do Visual Studio para projetos de malha de Service Fabric do Azure | Microsoft Docs
description: Este artigo mostra como otimizar o desempenho do Visual Studio para projetos de malha Service Fabric de forma que sua primeira execução de depuração (F5) seja muito mais rápida.
services: service-fabric-mesh
keywords: otimizar o desempenho de depuração
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663437"
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