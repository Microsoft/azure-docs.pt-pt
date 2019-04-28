---
title: Integrar dados SIM na monitorização solução remota - Azure | Documentos da Microsoft
description: Este artigo descreve como integrar dados Telefónica SIM na solução de monitorização remota.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442244"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrar dados SIM na solução de monitorização remota

Muitas vezes, ligarem dispositivos IoT à cloud através de um cartão SIM que permita que sejam enviar fluxos de dados a partir de qualquer lugar. A solução de monitorização remota do Azure IoT permite a integração de dados de conectividade gerida de IoT, para que os operadores também podem controlar o estado de funcionamento do dispositivo por meio dos dados fornecidos pelo SIM de IoT.

Monitorização remota fornece fora a total integração com a conectividade de IoT Telefónica, permitindo que os clientes que utilizam a sua plataforma de conectividade de IoT sincronizar o dispositivo de dados de conectividade de SIMs às respetivas soluções. Esta solução pode ser estendida para oferecer suporte a outros fornecedores de conectividade de IoT através do GitHub [repositório](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

Neste tutorial, ficará a saber como:

* Integrar dados SIM de IoT Telefónica na solução de monitorização remota
* Ver telemetria em tempo real
* Ver dados do SIM

## <a name="telefnica-iot-integration-setup"></a>Configuração de integração de telefónica IoT

### <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade adicional de monitorização remota está atualmente em pré-visualização. Para sincronizar os dados de conectividade na solução de monitorização remota do Azure, siga estes passos:

1. Preencher um pedido na [site do Telefónica](https://iot.telefonica.com/contact), selecione a opção **monitorização remota do Azure**, incluindo os dados de contactos.
2. Telefónica ativa a sua conta.
3. Se ainda não é um cliente Telefónica e deseja aproveitar isso ou outros serviços de IoT conectividade Cloud pronto, visite [site do Telefónica](https://iot.telefonica.com/) e selecione a opção **conectividade**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM setup
Associação de ID de dispositivo do telefónica SIM & duplo do Azure baseia-se a propriedade de "alias" Telefónica IoT SIM. 

Navegue para [Portal de plataforma de conectividade de IoT Telefónica](https://m2m-movistar-es.telefonica.com/) > inventário SIM > selecione sua SIM e atualizar cada SIM "alias" com o seu deviceID pretendida do duplo. Esta tarefa também pode ser feita no modo em massa (consulte a manuais do usuário de plataformas de conectividade de IoT Telefónica).

Esta tarefa também pode ser feita no modo em massa (consulte a manuais do usuário de plataformas de conectividade de IoT Telefónica)

![Atualização de telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Para ligar o seu dispositivo para a monitorização remota, pode seguir estes tutoriais usando [C](iot-accelerators-connecting-devices-linux.md) ou [nó](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Ver telemetria do dispositivo e propriedades do SIM

Assim que a sua conta de Telefónica está configurada corretamente e o seu dispositivo está ligado, pode ver os detalhes do dispositivo e os dados SIM.

Os seguintes parâmetros de conectividade são publicados:

* ICCID
* IP
* Presença de rede
* Estado do SIM
* Localização com base em rede
* Tráfego de dados consumidos

![Dashboard](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral de como integrar dados SIM na monitorização remota do Azure IoT, Eis os passos seguintes sugeridos para Aceleradores de soluções:

* [Operar a solução de monitorização remota do Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizar a monitorização avançada](iot-accelerators-remote-monitoring-monitor.md)
* [Gerir os seus dispositivos](iot-accelerators-remote-monitoring-manage.md)
* [Resolver problemas do dispositivo](iot-accelerators-remote-monitoring-maintain.md)

