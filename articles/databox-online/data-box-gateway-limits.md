---
title: Limita o Gateway de caixa de dados do Azure | Documentos da Microsoft
description: Descreve os limites de sistema e os tamanhos recomendados para o Gateway de caixa de dados do Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401568"
---
# <a name="azure-data-box-gateway-limits"></a>Limites do Gateway de caixa de dados do Azure

Considere estes limites, como implantar e operar a sua solução de Gateway de caixa de dados do Microsoft Azure. 


## <a name="data-box-gateway-service-limits"></a>Limites do serviço de Gateway de caixa de dados

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limites do dispositivo de Gateway de caixa de dados

A tabela seguinte descreve os limites para o dispositivo de Gateway de caixa de dados.

| Descrição | Value |
|---|---|
|Não. de arquivos por dispositivo |100 milhões <br> Limite é de aproximadamente 25 milhões de ficheiros para todas as 2 TB de espaço em disco com o limite máximo em 100 milhões |
|Não. de partilhas por dispositivo |24 |
|Não. de partilhas por contentor de armazenamento do Azure |1 |
|Tamanho máximo do ficheiro escrito para uma partilha|Para um dispositivo virtual de 2 TB, o tamanho máximo é 500 GB. <br> O tamanho máximo do ficheiro aumenta com o tamanho de disco de dados a proporção anterior até atingir um máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de carregamento de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho de tamanho de conta de armazenamento do Azure e o objeto

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
