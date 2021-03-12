---
title: Arquitetura de monitorização contínua do paciente em Azure IoT Central | Microsoft Docs
description: Tutorial - Conheça a arquitetura de uma solução de monitorização contínua do paciente.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6f7359b2b2fb0a1ea6ce92ec52bba15fc74fc75a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017159"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura da monitorização contínua do paciente

Este artigo descreve a arquitetura de uma solução construída a partir do modelo de aplicação **de monitorização contínua** do paciente:

As soluções de monitorização contínua do paciente podem ser construídas utilizando o modelo de aplicação fornecido, e usando a arquitetura que está descrita abaixo como orientação.

:::image type="content" source="media/cpm-architecture.png" alt-text="Arquitetura da monitorização contínua do paciente":::

## <a name="details"></a>Detalhes

Esta secção descreve cada parte do diagrama de arquitetura com mais detalhes:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Dispositivos médicos Bluetooth Low Energy (BLE)

Muitos wearables médicos usados em soluções de IoT de cuidados de saúde são dispositivos BLE. Estes dispositivos não conseguem comunicar diretamente com a nuvem e precisam de usar uma porta de entrada para trocar dados com a sua solução de nuvem. Esta arquitetura usa uma aplicação de telemóvel como porta de entrada.

### <a name="mobile-phone-gateway"></a>Gateway de telemóvel

A função principal da aplicação do telemóvel é recolher dados BLE de dispositivos médicos e comunicá-lo à IoT Central. A aplicação também guia os pacientes através da configuração do dispositivo e permite-lhes ver os seus dados pessoais de saúde. Outras soluções poderiam usar um gateway tablet ou uma porta estática em um quarto de hospital. Uma aplicação móvel de amostra de código aberto está disponível para Android e iOS para usar como ponto de partida para o desenvolvimento da sua aplicação. Para saber mais, consulte a [aplicação móvel IoT Central Continuous Patient Monitoring](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)Mobile .

### <a name="export-to-azure-api-for-fhirreg"></a>Exportação para AZure API para FHIR&reg;

A Azure IoT Central é compatível com a HIPAA e &reg; certificada pela HITRUST. Também pode enviar dados de saúde do paciente para outros serviços utilizando a [API Azure para FHIR](../../healthcare-apis/fhir/overview.md). A Azure API para FHIR é uma API baseada em padrões para dados de saúde clínica. O [conector Azure IoT para FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) permite-lhe utilizar a API Azure para FHIR como um destino contínuo de exportação de dados da IoT Central.

### <a name="machine-learning"></a>Aprendizagem automática

Utilize modelos de machine learning com os seus dados FHIR para gerar insights e apoiar a tomada de decisão pela sua equipa de cuidados. Para saber mais, consulte a [documentação de aprendizagem automática Azure.](../../machine-learning/index.yml)

### <a name="provider-dashboard"></a>Painel de instrumentos do fornecedor

Utilize a AZure API para obter dados de FHIR para construir um painel de insights do paciente ou integrá-lo diretamente num registo médico eletrónico utilizado pelas equipas de cuidados. As equipas de cuidados podem usar o painel de instrumentos para ajudar os pacientes e identificar sinais de alerta precoce de deterioração. Para saber mais, consulte o tutorial do [painel de instrumentos Build a Power BI.](tutorial-health-data-triage.md)

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é [aprender a implementar um modelo de aplicação de monitorização contínua do paciente.](tutorial-continuous-patient-monitoring.md)