---
title: Encriptação de dados do Azure IoT Hub em repouso através de chaves geridas pelo cliente Microsoft Docs
description: Encriptação de dados em repouso com chaves geridas pelo cliente para IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370581"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Encriptação de dados em repouso com chaves geridas pelo cliente para IoT Hub

O IoT Hub suporta a encriptação de dados em repouso com chaves geridas pelo cliente (CMK), também conhecida como Bring your own key (BYOK), suporte para O Hub Azure IoT. O Azure IoT Hub fornece encriptação de dados em repouso e em trânsito. Por padrão, o IoT Hub utiliza chaves geridas pela Microsoft para encriptar os dados. Com o suporte da CMK, os clientes têm agora a opção de encriptar os dados em repouso com uma chave de encriptação, gerida pelos clientes, utilizando o [Cofre de Chaves Azure.](https://azure.microsoft.com/services/key-vault/)

Esta capacidade requer a criação de um novo Hub IoT (nível básico ou padrão), numa das seguintes regiões: Leste dos EUA, Oeste dos EUA 2, Centro-Sul dos EUA ou Gov dos EUA. Para experimentar esta capacidade, contacte-nos através do [suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Partilhe o nome da sua empresa e o ID de subscrição ao contactar o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
