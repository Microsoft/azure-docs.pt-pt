---
title: Azure Stack Edge Pro com notas de lançamento FPGA 2101 | Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o lançamento do Azure Stack Edge 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727554"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro com notas de lançamento da FPGA 2101

As seguintes notas de lançamento identificam as questões abertas críticas e os problemas resolvidos para o lançamento de Azure Stack Edge Pro em 2101 com um Field Programmable Gate Array (FPGA).

As notas de lançamento são continuamente atualizadas. À medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo Azure Stack Edge, reveja cuidadosamente as informações nas notas de lançamento.  

Esta versão corresponde à versão do software:

- **Azure Stack Edge 2101 (1.6.1475.2528)** - KB 4599267

> [!NOTE]
> A atualização 2101 só pode ser aplicada em dispositivos que estejam a executar versões de disponibilidade geral (GA) do software ou posterior.

## <a name="whats-new"></a>Novidades

Esta versão contém a seguinte correção de erro:

- **Problema de upload** - Este lançamento corrige um problema de upload, onde o upload recomeça causado por uma falha pode retardar a taxa de conclusão do upload. Este problema pode ocorrer ao carregar um conjunto de dados que consiste principalmente em ficheiros que são grandes em relação à largura de banda disponível, particularmente, mas não se limitando a, quando o estrangulamento da largura de banda está ativo. Esta alteração garante oportunidade suficiente para a conclusão do upload antes de reiniciar o upload para um determinado ficheiro.

Esta versão também contém as seguintes atualizações:

- Todas as atualizações cumulativas do Windows e atualizações de quadros .NET lançadas até outubro de 2020.
- A versão de firmware do controlador de gestão de baseboard (BMC) é atualizada de 3.32.32.32 para 3.36.36.36 durante a instalação da fábrica para resolver a incompatibilidade com as mais recentes unidades de alimentação da Dell.
- Esta versão suporta ioT Edge 1.0.9.3 em dispositivos Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Questões conhecidas neste lançamento

Não são divulgados novos problemas para este lançamento. Todas as questões notadas pela libertação foram transitadas das versões anteriores. Para ver uma lista de questões conhecidas, vá a [questões conhecidas no lançamento da AG](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)