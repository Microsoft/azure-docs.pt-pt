---
title: Encriptação de dados do Serviço de Fornecimento de Dispositivos Azure em repouso através de chaves geridas pelo cliente Microsoft Docs
description: Encriptação de dados em repouso com chaves geridas pelo cliente para o Serviço de Provisionamento de Dispositivos
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: e9b5a549d550f117e080fc64193fbe29658f401d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85298230"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Encriptação de dados em repouso com chaves geridas pelo cliente para o Serviço de Provisionamento de Dispositivos

## <a name="overview"></a>Descrição geral

O Serviço de Provisionamento de Dispositivos (DPS) suporta a encriptação de dados em repouso com as teclas geridas pelo cliente (CMK), também conhecidas como trazer a sua própria chave (BYOK). O DPS fornece encriptação de dados em repouso e em trânsito tal como está escrito nos nossos datacenters e desencripta-os para si, à medida que os acede. Por predefinição, o DPS utiliza as chaves geridas pela Microsoft para encriptar os dados em repouso. Com a CMK, pode obter uma camada adicional de encriptação em cima da encriptação da plataforma padrão, optando por encriptar dados em repouso com uma chave de encriptação, gerida através do seu Cofre de [Chave Azure](https://azure.microsoft.com/services/key-vault/). Isto dá-lhe a flexibilidade para criar, rodar, desativar e revogar as teclas. Se a CMK estiver configurada para o seu DPS, implica que a dupla encriptação está ativada com duas camadas de proteção protegendo ativamente os seus dados. 

Esta capacidade requer a criação de um novo DPS. Para experimentar esta capacidade, contacte-nos através do [suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Partilhe o nome da sua empresa e o ID de subscrição ao contactar o suporte da Microsoft.


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps/)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
