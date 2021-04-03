---
title: Azure Stack Edge Pro R limita | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Azure Stack Edge Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467631"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Limites Azure Stack Edge Pro R

Considere estes limites à medida que implementa e opera a sua solução Azure Stack Edge Pro R.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Limites de serviço Azure Stack Edge Pro R

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Limites do dispositivo Azure Stack Edge Pro R

A tabela seguinte descreve os limites para o dispositivo Azure Stack Edge Pro R.

| Description | Valor |
|---|---|
|N.º de ficheiros por dispositivo |100 milhões |
|N.º de ações por contentor |1 |
|Máximo não. de pontos finais de ações e pontos finais DE REST por dispositivo| 24 |
|Máximo não. de contas de armazenamento hierárquico por dispositivo| 24|
|Tamanho máximo do ficheiro escrito para uma partilha| 5 TB |
|Número máximo de grupos de recursos por dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limites de armazenamento Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Tamanho da conta de armazenamento Azure e limites do tamanho do objeto

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto azul

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)
