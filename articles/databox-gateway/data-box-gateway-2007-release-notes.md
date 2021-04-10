---
title: Azure Stack Edge & notas de lançamento do Azure Data Box Gateway 2007| Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o lançamento de Azure Stack Edge e Data Box Gateway em execução em 2007.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582786"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Notas de lançamento de Azure Stack Edge e Azure Data Box Gateway 2007

As seguintes notas de lançamento identificam as questões abertas críticas e os problemas resolvidos para o lançamento de 2007 para Azure Stack Edge e Data Box Gateway.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu Azure Stack Edge/Data Box Gateway, reveja cuidadosamente as informações contidas nas notas de lançamento.

Esta versão corresponde às versões de software:

- **Azure Stack Edge 2007 (1.6.1280.1667)** - KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** - KB 4566550

> [!NOTE]
> A atualização de 2007 só pode ser aplicada a todos os dispositivos que estejam a executar versões gerais de disponibilidade (GA) do software ou posterior.

## <a name="whats-new"></a>Novidades

Esta versão contém a seguinte correção de erro:

- **Problema de upload** - Este lançamento corrige um problema de upload onde o upload recomeça devido a falhas pode atrasar a taxa de conclusão do upload. Este problema pode ocorrer ao carregar um conjunto de dados que consiste principalmente em ficheiros de grande dimensão em relação à largura de banda disponível, particularmente, mas não se limitando a, quando o estrangulamento da largura de banda está ativo. Esta alteração garante que é dada oportunidade suficiente para a conclusão do upload antes de reiniciar o upload para um determinado ficheiro.

Esta versão também contém as seguintes atualizações:

- A imagem base do Windows VHD foi atualizada.
- Todas as atualizações cumulativas do Windows e atualizações de quadros .NET estão incluídas que foram lançadas até maio de 2020.
- Esta versão suporta ioT Edge 1.0.9.3 em dispositivos Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Questões conhecidas neste lançamento

Não são divulgados novos problemas para este lançamento. Todas as questões notadas pela libertação foram transitadas das versões anteriores. Para ver uma lista de questões conhecidas, vá a [questões conhecidas no lançamento da AG](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
