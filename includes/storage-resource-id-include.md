---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "68249922"
---
Um ID de recurso Azure indica o público para o qual um símbolo emitido pode ser usado para fornecer acesso a um recurso Azure. No caso do Azure Storage, o ID de recurso pode ser específico de uma única conta de armazenamento, ou pode aplicar-se a qualquer conta de armazenamento. A tabela a seguir descreve os valores que pode fornecer para o ID de recursos:

|ID do Recurso  |Description  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | O ponto final de serviço para uma determinada conta de armazenamento. Utilize este valor para adquirir um símbolo para autorizar pedidos a essa conta e serviço específicos do Azure Storage. Substitua o valor nos parênteses pelo nome da sua conta de armazenamento.      |
|`https://storage.azure.com/`     | Utilize para adquirir um símbolo para autorizar pedidos a qualquer conta de Armazenamento Azure.        |
