---
title: Otimizar o Estúdio Visual para a malha de tecido de serviço Azure
description: Este artigo mostra-lhe como otimizar o desempenho do Visual Studio para projetos de Malha de Tecido de Serviço para que a sua primeira corrida de depuração (F5) seja muito mais rápida.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625757"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Estúdio Visual para projetos de malha de tecido de serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Este artigo mostra-lhe como otimizar o desempenho do Visual Studio para projetos de Malha de Tecido de Serviço para que a sua primeira corrida de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar definições de Estúdio Visual
 
No Estúdio Visual, no âmbito   >  **das ferramentas de serviço**   >  **Ferramentas de malha de tecido**  >  **geral,** pode ajustar as seguintes definições:

- **Puxe as imagens necessárias do Docker no projeto aberto** torna a sua primeira corrida de depuração (F5) mais rápida iniciando o processo de descarregamento de imagem enquanto o projeto está a carregar.  
- **Implementar a aplicação em projeto aberto** pode tornar a sua primeira depuração (F5) mais rápida iniciando o processo de implementação assim que o projeto é aberto.  
- **Remover a aplicação em close do projeto** recupera recursos (CPU, RAM) atribuídos à app removendo a app mesh quando o projeto está fechado.  

Quando vê mensagens na janela de saída das Ferramentas de Tecido de Serviço que o Visual Studio está a "puxar imagens", a "aquecer", ou a "remover a aplicação", é uma referência às definições acima. Pode desligar estas definições.

## <a name="next-steps"></a>Passos seguintes

Leia através do tutorial da [app Debug a Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)