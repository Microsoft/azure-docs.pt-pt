---
title: Avere vFXT para projetos de demonstração da Azure
description: 'Estas amostras mostram características-chave e casos de utilização para Avere vFXT para Azure: renderização de vídeo, computação de alto desempenho, desempenho vFXT e configuração do cliente.'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 31e32bfc0a2c3279375148bdf3da7d4a4829af1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271078"
---
# <a name="avere-vfxt-demo-projects"></a>Projetos de demonstração Avere vFXT

Os tutoriais da amostra são fornecidos no [GitHub.](https://github.com/Azure/Avere) Estes pequenos projetos demonstram características-chave e casos de utilização de Avere vFXT para Azure.

## <a name="video-rendering"></a>Renderização de vídeo

* [Renderização usando Azure Batch e Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) - um projeto de 60 minutos que demonstra como usar o Autodesk Maya com Azure Batch e um cluster Avere vFXT para gerar um filme de animação

* [Porquê usar o Avere vFXT para renderização?](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) - Demonstração que compara os tempos de renderização do armazenamento ligado à rede com e sem um cluster Avere vFXT

## <a name="high-performance-computing"></a>Computação de alto desempenho

* [Melhores práticas para melhorar o tempo de arranque da Azure Virtual Machine (VM)](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) - Um teste reprodutível que usa Avere vFXT para a Azure para melhorar os tempos de arranque quando gira milhares de clientes computação rapidamente

## <a name="vfxt-performance"></a>vFXT desempenho

* [Medir o desempenho vFXT com vdbench](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) - Uma configuração de teste básica para gerar cargas de trabalho de pequena e média dimensão para testar os subsistemas de memória vFXT e discos de disco

## <a name="client-setup"></a>Configuração do cliente

* [Estação de trabalho do Windows 10 para Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) - Demonstra como configurar uma estação de trabalho do Windows e montá-la num cluster Avere vFXT
