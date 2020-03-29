---
title: Integrar dados SIM na Solução de Monitorização Remota - Azure Microsoft Docs
description: Este artigo descreve como integrar os dados da Telefónica SIM na solução de Monitorização Remota.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442244"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrar dados SIM na solução de Monitorização Remota

Os dispositivos IoT ligam-se frequentemente à nuvem utilizando um cartão SIM que lhes permite enviar fluxos de dados de qualquer lugar. A solução de Monitorização Remota Azure IoT permite a integração de dados de Conectividade Gerida ioT, para que os operadores também possam acompanhar a saúde do dispositivo através dos dados fornecidos pelo IoT SIM.

A Monitorização Remota fornece a integração da caixa com a Conectividade Telefónica IoT, permitindo que os clientes que utilizam a sua Plataforma de Conectividade IoT sincronizam os seus dados de conectividade sims de dispositivo para as suas soluções. Esta solução pode ser alargada para apoiar outros fornecedores de Conectividade IoT através do [repositório](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub .

Neste tutorial, ficará a saber como:

* Integrar os dados da Telefónica IoT SIM na solução de Monitorização Remota
* Ver telemetria em tempo real
* Ver dados SIM

## <a name="telefnica-iot-integration-setup"></a>Configuração de integração da Telefónica IoT

### <a name="prerequisites"></a>Pré-requisitos

Esta função adicional de monitorização remota está atualmente em pré-visualização. Para sincronizar os seus dados de conectividade na Solução de Monitorização Remota Azure, siga estes passos:

1. Preencha um pedido no [site da Telefónica,](https://iot.telefonica.com/contact)selecione a opção **Monitorização Remota Azure,** incluindo os seus dados de contacto.
2. A Telefónica ativa a sua conta.
3. Se ainda não é cliente da Telefónica e pretende desfrutar deste ou de outros serviços IoT Connectivity Cloud Ready, visite o [site da Telefónica](https://iot.telefonica.com/) e selecione a opção **Conectividade**.

### <a name="telefnica-sim-setup"></a>Configuração telefónica SIM
A Telefónica SIM & a associação de identificação de dispositivos Azure Twin baseia-se na propriedade "pseudónimo" Telefónica IoT SIM. 

Navegue para o Portal da Plataforma de [Conectividade Telefónica IoT](https://m2m-movistar-es.telefonica.com/) > inventário SIM > Selecione o seu SIM e atualize cada "pseudónimo" SIM com o seu dispositivo Twin pretendido. Esta tarefa também pode ser feita em modo granel (consulte os manuais de utilizador da Plataforma de Conectividade Telefónica IoT).

Esta tarefa também pode ser feita em modo granel (consulte os manuais de utilizador da Plataforma de Conectividade Telefónica IoT)

![Atualização da Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Para ligar o seu dispositivo à Monitorização Remota, pode seguir estes tutoriais utilizando [C](iot-accelerators-connecting-devices-linux.md) ou [Nó](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Ver telemetria do dispositivo e propriedades SIM

Uma vez que a sua conta Telefónica esteja corretamente configurada e o seu dispositivo esteja ligado, pode visualizar os detalhes do dispositivo e os dados SIM.

São publicados os seguintes parâmetros de conectividade:

* ICCID
* IP
* Presença de rede
* Estatuto SIM
* Localização baseada em rede
* Tráfego de dados consumido

![Dashboard](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral de como integrar os dados SIM na Monitorização Remota Azure IoT, aqui são sugeridos os próximos passos para aceleradores de soluções:

* [Operar a solução de monitorização remota Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizar a monitorização avançada](iot-accelerators-remote-monitoring-monitor.md)
* [Faça a gestão dos seus dispositivos](iot-accelerators-remote-monitoring-manage.md)
* [Resolver problemas do dispositivo](iot-accelerators-remote-monitoring-maintain.md)

