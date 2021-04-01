---
title: Recuperar informações do balanceador de carga utilizando o Serviço de Metadados de Instância Azure
titleSuffix: Azure Load Balancer
description: Começa a aprender a usar o Serviço de Metadados de Instância Azure para recuperar informações sobre o balanceador de carga.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519087"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Recuperar informações do balanceador de carga utilizando o Serviço de Metadados de Instância Azure

O IMDS (Serviço de Metadados de Instância Azure) fornece informações sobre casos de máquinas virtuais atualmente em execução. O serviço é uma API REST que está disponível num endereço IP bem conhecido e não roteável (169.254.169.254). 

Quando colocar a máquina virtual ou a máquina virtual a definir instâncias atrás de um Balançador de Carga Padrão Azure, utilize o IMDS para recuperar metadados relacionados com o equilibrador de carga e as instâncias.

Os metadados incluem as seguintes informações para as máquinas virtuais ou conjuntos de escala de máquinas virtuais:

* IP público padrão SKU.
* Configurações de regra de entrada do equilibrador de carga de cada IP privado da interface de rede.
* Configurações de regra de saída do equilibrador de carga de cada IP privado da interface de rede.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Aceda aos metadados do balançador de carga utilizando o IMDS

Para obter mais informações sobre como aceder aos metadados do balanceador de carga, consulte [utilizar o Serviço de Metadados de Instância Azure para aceder às informações do balanceador de carga](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Resolução de erros códigos de erro comuns

Para obter mais informações sobre códigos de erro comuns e seus métodos de mitigação, consulte [códigos de erro comuns de resolução de problemas ao utilizar o IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Suporte

Se não conseguir recuperar uma resposta de metadados após várias tentativas, crie um problema de suporte no portal Azure.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [o Serviço de Metadados Azure Instance](../virtual-machines/windows/instance-metadata-service.md)

[Implementar um equilibrador de carga padrão](quickstart-load-balancer-standard-public-portal.md)

