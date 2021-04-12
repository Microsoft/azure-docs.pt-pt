---
title: Configuração de firewall Azure Percept e recomendações de segurança
description: Saiba mais sobre a configuração de firewall do Azure Percept e recomendações de segurança
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604418"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Configuração de firewall Azure Percept e recomendações de segurança

Reveja as diretrizes abaixo para obter informações sobre firewalls configurantes e boas práticas de segurança geral com Azure Percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurar firewalls para Azure Percept DK

Se a sua configuração de rede exigir que permita explicitamente ligações feitas a partir de dispositivos Azure Percept DK, reveja a seguinte lista de componentes.

Esta lista de verificação é um ponto de partida para as regras de firewall:

|URL (* = wildcard)|Portas TCP de saída|Utilização|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|443|Autenticação e Autorização Azure DK SOM|
|*.auth.projectsantacruz.azure.net|443|Autenticação e Autorização Azure DK SOM|

Além disso, reveja a lista de [ligações utilizadas pela Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Recomendações adicionais para a implantação da produção

Azure Percept DK oferece uma grande variedade de capacidades de segurança fora da caixa. Além das poderosas funcionalidades de segurança incluídas na versão atual, a Microsoft também sugere as seguintes orientações ao considerar as implementações de produção:

- Forte proteção física do próprio dispositivo
- Garantir que a encriptação de dados em repouso está ativada
- Monitorize continuamente a postura do dispositivo e responda rapidamente aos alertas
- Limitar o número de administradores que têm acesso ao dispositivo

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a segurança da Azure Percept](./overview-percept-security.md)