---
title: Limites de gateway de caixa de dados Azure | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Gateway da Caixa de Dados do Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582661"
---
# <a name="azure-data-box-gateway-limits"></a>Limites de gateway de caixa de dados Azure

Considere estes limites à medida que implementa e opera a sua solução Microsoft Azure Data Box Gateway.

## <a name="data-box-gateway-service-limits"></a>Limites de serviço data box gateway

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limites do dispositivo Data Box Gateway

A tabela seguinte descreve os limites para o dispositivo Data Box Gateway.

| Description | Valor |
|---|---|
|N.º de ficheiros por dispositivo |100 milhões <br> Por cada 25 milhões de ficheiros que estão a ser adicionados (com limite máximo de 100 milhões), deve adicionar 2 TB de espaço em disco, 8 GB de RAM e 4 núcleos de CPU. |
|N.º de ações por dispositivo |24 |
|N.º de ações por recipiente de armazenamento Azure |1 |
|Tamanho máximo do ficheiro escrito para uma partilha|Para um dispositivo virtual de 2-TB, o tamanho máximo do ficheiro é de 500 GB. <br> O tamanho máximo do ficheiro aumenta com o tamanho do disco de dados na relação anterior até atingir um máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Limites de armazenamento Azure

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Tamanho da conta de armazenamento Azure e limites do tamanho do objeto

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto azul

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
