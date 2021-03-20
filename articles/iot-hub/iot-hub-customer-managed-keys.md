---
title: Encriptação de dados do Azure IoT Hub em repouso através de chaves geridas pelo cliente| Microsoft Docs
description: Encriptação de dados em repouso com chaves geridas pelo cliente para ioT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142201"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Encriptação de dados em repouso com chaves geridas pelo cliente para ioT Hub

O IoT Hub suporta a encriptação de dados em repouso com as teclas geridas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK). O Azure IoT Hub fornece encriptação de dados em repouso e em trânsito, tal como está escrito nos nossos datacenters e desencripta-os para si à medida que os acede. Por padrão, o IoT Hub utiliza as chaves geridas pela Microsoft para encriptar os dados em repouso. Com a CMK, pode obter outra camada de encriptação em cima da encriptação padrão e pode optar por encriptar dados em repouso com uma chave de encriptação chave, gerida através do seu [Cofre de Chave Azure](https://azure.microsoft.com/services/key-vault/). Isto dá-lhe a flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Se o BYOK estiver configurado para o seu IoT Hub, também fornecemos uma dupla encriptação, que oferece uma segunda camada de proteção, ao mesmo tempo que lhe permite controlar a chave de encriptação através do seu Cofre de Chave Azure.

Esta capacidade requer a criação de um novo Hub IoT (nível básico ou padrão). Para experimentar esta capacidade, contacte-nos através do [suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Partilhe o nome da sua empresa e o ID de subscrição ao contactar o suporte da Microsoft.


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o IoT Hub](./about-iot-hub.md)

* [Saiba mais sobre o Azure Key Vault](../key-vault/general/overview.md)