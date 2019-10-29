---
title: Arquitetura contínua de monitoramento de pacientes no Azure IoT Central | Microsoft Docs
description: Saiba mais sobre uma arquitetura de solução de monitoramento de pacientes contínua.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027482"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura de monitoramento de pacientes contínua

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

As soluções de monitoramento de pacientes contínuas podem ser criadas aproveitando o modelo de aplicativo fornecido e usando a arquitetura descrita abaixo como orientação.

>[!div class="mx-imgBorder"] 
>![arquitetura CPM](media/cpm-architecture.png)

1. Dispositivos médicos comunicando-se via Bluetooth de baixa energia (BLE)
1. Gateway de celular recebendo dados do BLE e enviando para IoT Central
1. Exportação contínua de dados dos dados de integridade do paciente para a API do Azure para FHIR&reg;
1. Machine Learning com base em dados interoperáveis
1. Cuidado com o Team Dashboard criado sobre os dados do FHIR

## <a name="details"></a>Detalhes
Esta seção descreve cada parte do diagrama de arquitetura em mais detalhes.

### <a name="ble-medical-devices"></a>Dispositivos médicos BLE
Muitos wearables médicos usados no espaço de IoT da área de saúde são dispositivos Bluetooth de baixa energia, o que significa que eles não podem falar diretamente com a nuvem e precisarão passar por um gateway. Essa arquitetura sugere o uso de um aplicativo de telefone celular como esse gateway.

### <a name="mobile-phone-gateway"></a>Gateway de celular
A função principal do aplicativo de telefone celular é ingerir dados BLE de dispositivos médicos e transmiti-los para o Azure IoT Central. Além disso, o aplicativo pode ajudar a orientar os pacientes por meio de um fluxo de configuração e provisionamento de dispositivo e ajudá-los a ver uma exibição de seus dados pessoais de integridade. Outras soluções podem usar um gateway de Tablet ou um gateway estático se estiver dentro de uma sala de hospital para atingir o mesmo fluxo de comunicação.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportar para a API do Azure para FHIR&reg;
O Azure IoT Central é compatível com HIPAA e HITRUST&reg; certificado, mas você também pode querer enviar dados relacionados à integridade do paciente para a API do Azure para FHIR. A [API do Azure para FHIR](../../healthcare-apis/overview.md) é uma API compatível com base em padrões e totalmente gerenciada para dados de integridade clínico que permite criar novos sistemas de envolvimento com seus dados de integridade. Ele permite o intercâmbio rápido de dados por meio de APIs FHIR, apoiado por uma oferta de PaaS (plataforma como serviço) gerenciada na nuvem. Usando a funcionalidade de exportação de dados contínua do IoT Central, você pode enviar dados para a API do Azure para FHIR.

### <a name="machine-learning"></a>Machine learning
Depois de agregar seus dados e contorná-los no formato FHIR, você pode criar modelos de aprendizado de máquina que podem enriquecer as ideias e habilitar a tomada de decisões mais inteligentes para sua equipe de atendimento. Há uma variedade de serviços que podem ser usados para criar, treinar e implantar modelos de aprendizado de máquina. Mais informações sobre como usar as ofertas de aprendizado de máquina do Azure podem ser encontradas em nossa [documentação de aprendizado de máquina](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Painel do provedor
Os dados localizados na API do Azure para FHIR podem ser usados para criar um painel de informações de pacientes ou diretamente integrados a um EMR para ajudar as equipes a visualizarem o status do paciente. As equipes de cuidado podem usar esse painel para tomar medidas para que os pacientes precisem de assistência e identifiquem os sinais de indeterioração de aviso antecipado. Para saber como criar um Power BI painel de provedor em tempo real, siga nosso [Guia de instruções](howto-health-data-triage.md).

## <a name="next-steps"></a>Passos seguintes
* [Saiba como implantar um modelo de aplicativo de monitoramento contínuo do paciente](tutorial-continuous-patient-monitoring.md)