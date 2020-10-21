---
title: incluir ficheiro
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284817"
---
Se selecionou um dos VM pré-configurados com um sistema operativo (e serviços adicionais opcionais), já escolheu um tamanho VM standard. Iniciar a sua solução com um SISTEMA pré-configurado é a abordagem recomendada. No entanto, se estiver a instalar um SO manualmente, deve dimensionar o seu VHD primário na sua imagem VM. Certifique-se de que o tamanho do disco OS está dentro dos limites para Linux ou Windows.

| SO | Tamanho VHD |
| --- | --- |
| Linux | 30 a 1023 GB |
| Windows | 30 a 250 GB |
|

As imagens base do Windows ou SQL Server fornecidas como base aprovada já satisfazem estes requisitos, por isso não altere o formato ou o tamanho do VHD.

Os discos de dados podem ser tão grandes como 1 TB. Ao decidir sobre o tamanho, lembre-se que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implementação. Crie VHDs de disco de dados como VHDs de formato fixo. Também devem ser expansíveis (escassos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.