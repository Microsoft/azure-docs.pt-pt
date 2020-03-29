---
title: Otimize Estúdio Visual para Malha de Tecido de Serviço Azure
description: Este artigo mostra-lhe como otimizar o desempenho do Estúdio Visual para projetos de malha de tecido de serviço para que a sua primeira execução de depuração (F5) seja muito mais rápida.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497972"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Estúdio Visual para projetos de malha de tecido de serviço

Este artigo mostra-lhe como otimizar o desempenho do Estúdio Visual para projetos de malha de tecido de serviço para que a sua primeira execução de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar as definições do Estúdio Visual
 
No Estúdio Visual, em **ferramentas** > **opções**  > **de ferramentas Ferramentas** > de malha de tecido**geral,** pode ajustar as seguintes definições:

- **Puxe as imagens necessárias** do Docker em projeto aberto torna a sua primeira execução de depuração (F5) mais rápida, iniciando o processo de descarregamento de imagem enquanto o projeto está a carregar.  
- **Implementar aplicação em projeto aberto** pode tornar a sua primeira execução de depuração (F5) mais rápida, iniciando o processo de implantação assim que o projeto for aberto.  
- **Remover a aplicação no projeto de perto** recupera recursos (CPU, RAM) atribuídos à app removendo a app Mesh quando o projeto está fechado.  

Quando se vê mensagens na janela de saída do Service Fabric Tools que o Visual Studio está a 'puxar imagens', 'aquecer', ou 'remover a aplicação', é em referência às definições acima. Pode desligar estas definições.

## <a name="next-steps"></a>Passos seguintes

Ler através do [tutorial de aplicação Debug a Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)