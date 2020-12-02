---
title: Limites Azure Stack Edge Mini R / Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Azure Stack Edge Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467850"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Limites Azure Stack Edge Mini R


Considere estes limites à medida que implementa e opera a sua solução Azure Stack Edge Mini R.

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Limites do dispositivo Azure Stack Edge Mini R

A tabela seguinte descreve os limites para o dispositivo Azure Stack Edge Mini R.

| Descrição | Limite|
|---|---:|
|Não. de ficheiros por dispositivo | 100 milhões <!--check with devs-->|
|Não. de ações por contentor | 1|
|Máximo não. de pontos finais de ações e pontos finais DE REST por dispositivo| 24 |
|Máximo não. de contas de armazenamento hierárquico por dispositivo| 24|
|Tamanho máximo do ficheiro escrito para uma partilha| 500 GB|
|Número máximo de grupos de recursos por dispositivo| 800|

## <a name="azure-storage-limits"></a>Limites de armazenamento Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Tamanho da conta de armazenamento Azure e limites do tamanho do objeto

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto azul

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
