---
title: Gerir a capacidade de memória física para o Azure Stack | Documentos da Microsoft
description: Monitorizar e gerir o espaço de armazenamento disponível para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cb5b64869a26133e1c08095a816c2d2854f85b94
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103507"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Gerir a capacidade de memória física para o Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Para aumentar a capacidade de memória total disponível para o Azure Stack, pode adicionar memória adicional. No Azure Stack seu servidor físico é também referido como um *nó de unidade de escala*. Todos os nós de unidade de escala que são membros de uma unidade de escala único tem de ter a mesma quantidade de memória.

> [!note]  
> Antes de continuar, consulte a documentação do fabricante de hardware para ver se um fabricante do seu suporta uma atualização de memória física. Seu contrato de suporte do fornecedor de hardware OEM pode exigir que o fornecedor de efetuar a colocação de rack do servidor físico e a atualização de firmware do dispositivo.

O diagrama de fluxo que se segue mostra o processo geral para adicionar memória a cada nó de unidade de escala.

![Adicionar memória a cada nó de unidade de escala](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Adicione memória a um nó existente
Os passos seguintes fornecem uma visão geral sobre o processo de memória de adicionar. 

> [!Warning]
> Não siga estes passos sem referir-se a sua documentação fornecida pelo OEM.
> 
> [!Warning]
> A unidade de escala completa deve ser desligada como uma atualização sem interrupção de memória não é suportada.

1. Parar o Azure Stack, utilizando os passos documentados no [início e fim do Azure Stack](azure-stack-start-and-stop.md) artigo.
2. Atualize a memória em cada computador físico com a documentação do fabricante de hardware.
3. Iniciar o Azure Stack, utilizando os passos a [início e fim do Azure Stack](azure-stack-start-and-stop.md) artigo.

## <a name="next-steps"></a>Passos Seguintes

 - Para saber como gerir contas de armazenamento no Azure Stack para encontrar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócio, veja [gerir contas de armazenamento no Azure Stack](azure-stack-manage-storage-accounts.md).
 - Para saber o operador de cloud do Azure Stack monitoriza e gere a capacidade de armazenamento da sua implementação do Azure Stack, veja [gerir a capacidade de armazenamento para o Azure Stack](azure-stack-manage-storage-shares.md). 
