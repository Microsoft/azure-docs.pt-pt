---
title: Azure Stack Edge Pro limita | Microsoft Docs
description: Saiba mais sobre os limites e tamanhos recomendados à medida que implementa e opera o Azure Stack Edge Pro, incluindo limites de serviço, limites de dispositivos e limites de armazenamento.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992760"
---
# <a name="azure-stack-edge-pro-limits"></a>Limites do Azure Stack Edge Pro

Considere estes limites à medida que implementa e opera a sua solução Microsoft Azure Stack Edge Pro. 

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limites do dispositivo Azure Stack Edge

A tabela seguinte descreve os limites para o dispositivo Azure Stack Edge Pro. 

A tabela seguinte descreve os limites para o dispositivo Azure Stack Edge.

| Description | Valor |
|---|---|
|N.º de ficheiros por dispositivo |100 milhões |
|N.º de ações por contentor |1 |
|Máximo não. de pontos finais de ações e pontos finais DE REST por dispositivo| 24 |
|Máximo não. de contas de armazenamento hierárquico por dispositivo| 24|
|Tamanho máximo do ficheiro escrito para uma partilha| 5 TB |
|Número máximo de grupos de recursos por dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limites de armazenamento Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Tamanho da conta de armazenamento Azure e limites do tamanho do objeto

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto azul

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
