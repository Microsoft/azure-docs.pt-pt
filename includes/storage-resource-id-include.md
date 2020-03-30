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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249922"
---
Um ID de recurso Azure AD indica o público para o qual um símbolo que é emitido pode ser usado para fornecer acesso a um recurso Azure. No caso do Armazenamento Azure, o ID de recurso pode ser específico de uma única conta de armazenamento, ou pode aplicar-se a qualquer conta de armazenamento. A tabela seguinte descreve os valores que pode fornecer para o ID do recurso:

|ID do Recurso  |Descrição  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | O ponto final de serviço para uma determinada conta de armazenamento. Utilize este valor para adquirir um símbolo para autorizar pedidos apenas para essa conta e serviço específicos de Armazenamento Azure. Substitua o valor dos suportes pelo nome da sua conta de armazenamento.      |
|`https://storage.azure.com/`     | Utilize para adquirir um símbolo para autorizar pedidos a qualquer conta de Armazenamento Azure.        |
