---
title: Integrar dados SIM na Solução de Monitorização Remota - Azure/ Microsoft Docs
description: Este artigo descreve como integrar os dados da Telefónica SIM na solução de Monitorização Remota.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323962"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrar dados SIM na solução de Monitorização Remota

Os dispositivos IoT ligam-se frequentemente à nuvem usando um cartão SIM que lhes permite enviar fluxos de dados de qualquer lugar. A solução de Monitorização Remota Azure IoT permite a integração de dados de conectividade gerida ioT, para que os operadores também possam acompanhar a saúde do dispositivo através dos dados fornecidos pelo IoT SIM.

A Remote Monitoring proporciona a integração fora da caixa com a Conectividade Telefónica IoT, permitindo que os clientes que utilizam a sua Plataforma de Conectividade IoT sincronizem os dados de conectividade do seu dispositivo SIMs às suas soluções. Esta solução pode ser alargada para apoiar outros fornecedores de conectividade IoT através do [repositório](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub.

Neste tutorial, ficará a saber como:

* Integrar dados da Telefónica IoT SIM na solução de Monitorização Remota
* Ver telemetria em tempo real
* Ver dados sim

## <a name="telefnica-iot-integration-setup"></a>Configuração de integração telefónica IoT

### <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade adicional de Monitorização Remota encontra-se atualmente em pré-visualização. Para sincronizar os seus dados de conectividade na Solução de Monitorização Remota Azure, siga estes passos:

1. Preencha um pedido no [site da Telefónica,](https://iot.telefonica.com/contact)selecione a opção **Azure Remote Monitoring**, incluindo os seus dados de contacto.
2. A Telefónica ativa a sua conta.
3. Se ainda não é cliente da Telefónica e pretende usufruir deste ou de outros serviços IoT Connectivity Cloud Ready, visite o [site da Telefónica](https://iot.telefonica.com/) e selecione a opção **Conectividade.**

### <a name="telefnica-sim-setup"></a>Configuração telefónica SIM
A Telefónica SIM & Associação de Identificação de Dispositivos Azure Twin é baseada na propriedade telefónica IoT SIM "alias". 

Navegue para o [Portal da Plataforma de Conectividade Telefónica IoT](https://m2m-movistar-es.telefonica.com/) > inventário SIM > Selecione o seu SIM e atualize cada SIM "pseudónimo" com o seu dispositivo TwinID desejado. Esta tarefa também pode ser feita em modo a granel (consulte os manuais de utilizador da Plataforma de Conectividade Telefónica IoT).

Esta tarefa também pode ser feita em modo granel (consulte os manuais de utilizador da Plataforma de Conectividade Telefónica IoT)

![Atualização telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Para ligar o seu dispositivo à Monitorização Remota, pode seguir estes tutoriais utilizando [C](iot-accelerators-connecting-devices-linux.md) ou [Nó](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Ver telemetria do dispositivo e propriedades SIM

Uma vez que a sua conta Telefónica esteja devidamente configurada e o seu dispositivo esteja ligado, pode visualizar os dados do dispositivo e sim.

São publicados os seguintes parâmetros de conectividade:

* ICCID
* IP
* Presença na rede
* Estado sim
* Localização baseada em rede
* Tráfego de dados consumido

![Screenshot da janela device Explorer no painel de monitorização remota Azure IoT. Uma linha que mostra detalhes do dispositivo WeatherStation está em destaque.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral de como integrar os Dados SIM na Monitorização Remota Azure IoT, aqui são sugeridos os próximos passos para os aceleradores de soluções:

* [Operar a solução de monitorização remota Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Realizar a monitorização avançada](iot-accelerators-remote-monitoring-monitor.md)
* [Faça a gestão dos seus dispositivos](iot-accelerators-remote-monitoring-manage.md)
* [Problemas com dispositivos de resolução de problemas](iot-accelerators-remote-monitoring-maintain.md)

