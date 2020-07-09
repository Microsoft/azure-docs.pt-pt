---
title: Otimizar o Estúdio Visual para a malha de tecido de serviço Azure
description: Este artigo mostra-lhe como otimizar o desempenho do Visual Studio para projetos de Malha de Tecido de Serviço para que a sua primeira corrida de depuração (F5) seja muito mais rápida.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75497972"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Estúdio Visual para projetos de malha de tecido de serviço

Este artigo mostra-lhe como otimizar o desempenho do Visual Studio para projetos de Malha de Tecido de Serviço para que a sua primeira corrida de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar definições de Estúdio Visual
 
No Estúdio Visual, no âmbito **Tools**  >  **das ferramentas de serviço**   >  **Ferramentas de malha de tecido**  >  **geral,** pode ajustar as seguintes definições:

- **Puxe as imagens necessárias do Docker no projeto aberto** torna a sua primeira corrida de depuração (F5) mais rápida iniciando o processo de descarregamento de imagem enquanto o projeto está a carregar.  
- **Implementar a aplicação em projeto aberto** pode tornar a sua primeira depuração (F5) mais rápida iniciando o processo de implementação assim que o projeto é aberto.  
- **Remover a aplicação em close do projeto** recupera recursos (CPU, RAM) atribuídos à app removendo a app mesh quando o projeto está fechado.  

Quando vê mensagens na janela de saída das Ferramentas de Tecido de Serviço que o Visual Studio está a "puxar imagens", a "aquecer", ou a "remover a aplicação", é uma referência às definições acima. Pode desligar estas definições.

## <a name="next-steps"></a>Próximos passos

Leia através do tutorial da [app Debug a Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)