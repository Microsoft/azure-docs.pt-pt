---
title: Encriptação de dados do Azure IoT Hub em repouso através de chaves geridas pelo cliente Microsoft Docs
description: Encriptação de dados em repouso com chaves geridas pelo cliente para ioT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976579"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Encriptação de dados em repouso com chaves geridas pelo cliente para ioT Hub

O IoT Hub suporta a encriptação de dados em repouso com as teclas geridas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK). O Azure IoT Hub fornece encriptação de dados em repouso e em trânsito, tal como está escrito nos nossos datacenters e desencripta-os para si à medida que os acede. Por padrão, o IoT Hub utiliza as chaves geridas pela Microsoft para encriptar os dados em repouso. Com a CMK, pode obter outra camada de encriptação em cima da encriptação padrão e pode optar por encriptar dados em repouso com uma chave de encriptação chave, gerida através do seu [Cofre de Chave Azure](https://azure.microsoft.com/services/key-vault/). Isto dá-lhe a flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Se o BYOK estiver configurado para o seu IoT Hub, também fornecemos uma dupla encriptação, que oferece uma segunda camada de proteção, ao mesmo tempo que lhe permite controlar a chave de encriptação através do seu Cofre de Chave Azure.

Esta capacidade requer a criação de um novo Hub IoT (nível básico ou padrão). Para experimentar esta capacidade, contacte-nos através do [suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Partilhe o nome da sua empresa e o ID de subscrição ao contactar o suporte da Microsoft.


## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
