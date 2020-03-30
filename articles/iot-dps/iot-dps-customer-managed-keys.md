---
title: Encriptação de dados do Serviço de Provisionamento de Dispositivos Azure em repouso através de chaves geridas pelo cliente Microsoft Docs
description: Encriptação de dados em repouso com chaves geridas pelo cliente para o Serviço de Provisionamento de Dispositivos
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675146"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Encriptação de dados em repouso com chaves geridas pelo cliente para o Serviço de Provisionamento de Dispositivos

## <a name="overview"></a>Descrição geral

O Serviço de Provisionamento de Dispositivos (DPS) suporta a encriptação de dados em repouso com chaves geridas pelo cliente (CMK), também conhecida como Bring your own key (BYOK). O DPS fornece encriptação de dados em repouso e em trânsito. Por padrão, o DPS utiliza chaves geridas pela Microsoft para encriptar os dados. Com o suporte da CMK, os clientes têm agora a opção de encriptar os dados em repouso com uma chave de encriptação, gerida pelos clientes, utilizando o [Cofre de Chaves Azure.](https://azure.microsoft.com/services/key-vault/)

Esta capacidade requer a criação de um novo DPS, numa das seguintes regiões: Leste dos EUA, Oeste dos EUA 2 ou Centro-Sul dos EUA. Para experimentar esta capacidade, contacte-nos através do [suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Partilhe o nome da sua empresa e o ID de subscrição ao contactar o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps/)

* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)