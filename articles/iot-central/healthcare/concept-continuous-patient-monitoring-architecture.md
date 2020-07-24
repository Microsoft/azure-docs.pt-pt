---
title: Arquitetura de monitorização contínua de pacientes no Azure IoT Central Microsoft Docs
description: Saiba mais sobre uma arquitetura de solução de monitorização contínua do paciente.
author: philmea
ms.author: philmea
ms.date: 7/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 0032f341330ad394241806a4fe61add530253f09
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116865"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura da monitorização contínua do paciente



As soluções de monitorização contínua do paciente podem ser construídas utilizando o modelo de aplicação fornecido, e usando a arquitetura que está descrita abaixo como orientação.

>[!div class="mx-imgBorder"] 
>![Arquitetura CPM](media/cpm-architecture.png)

1. Dispositivos médicos que comunicam usando Bluetooth Low Energy (BLE)
1. Gateway de telemóvel recebendo dados ble e enviando para a IoT Central
1. Exportação contínua de dados de saúde do doente para a API Azure para fHIR&reg;
1. Aprendizagem automática com base em dados interoperáveis
1. Painel de cuidados da equipa construído com base em dados do FHIR

## <a name="details"></a>Detalhes
Esta secção descreve cada parte do diagrama de arquitetura com mais detalhes.

### <a name="ble-medical-devices"></a>Dispositivos médicos BLE
Muitos wearables médicos usados no espaço IoT dos cuidados de saúde são dispositivos Bluetooth Low Energy. Não conseguem falar diretamente para a nuvem e terão de passar por um portal. Esta arquitetura sugere usar uma aplicação de telemóvel como porta de entrada. 

### <a name="mobile-phone-gateway"></a>Gateway de telemóvel
A função principal da aplicação do telemóvel é ingerir dados BLE de dispositivos médicos e comunicá-lo à Azure IoT Central. Além disso, a aplicação pode ajudar a guiar os pacientes através de um fluxo de configuração e provisão do dispositivo e ajudá-los a ver uma visão dos seus dados pessoais de saúde. Outras soluções podem usar um gateway tablet ou uma porta estática se dentro de um quarto de hospital para obter o mesmo fluxo de comunicação. Criámos uma aplicação móvel de amostra aberta disponível para Android e iOS que podes usar como ponto de partida para iniciar os esforços de desenvolvimento da tua aplicação. Para obter mais informações sobre a amostra de aplicações móveis IoT Central Continuous Patient Monitoring, consulte [Azure Samples](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportação para AZure API para FHIR&reg;
A Azure IoT Central é compatível com a HIPAA e &reg; certificada pela HITRUST, mas também pode querer enviar dados relacionados com a saúde do paciente para a Azure API para fHIR. [A Azure API para FHIR](../../healthcare-apis/overview.md) é uma API totalmente gerida, baseada em padrões e conforme para dados de saúde clínica que permite criar novos sistemas de envolvimento com os seus dados de saúde. Permite uma rápida troca de dados através de APIs FHIR, apoiado por uma oferta gerida de Plataforma-as-A (PaaS) na nuvem. Utilizando a funcionalidade de Exportação contínua de dados da IoT Central, pode enviar dados para a API Azure para FHIR através do [conector Azure IoT para FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart).

### <a name="machine-learning"></a>Aprendizagem automática
Depois de agregar os seus dados e traduzi-lo em formato FHIR, pode construir modelos de machine learning que possam enriquecer insights e permitir uma tomada de decisão mais inteligente para a sua equipa de cuidados. Existem diferentes tipos de serviços que podem ser usados para construir, treinar e implementar modelos de aprendizagem automática. Mais informações sobre como usar as ofertas de machine learning da Azure podem ser encontradas na nossa [documentação de aprendizagem automática.](../../machine-learning/index.yml)

### <a name="provider-dashboard"></a>Painel de instrumentos do fornecedor
Os dados localizados na API Azure para FHIR podem ser usados para construir um painel de insights do paciente ou podem ser diretamente integrados numa EMR para ajudar as equipas de cuidados a visualizar o estado do paciente. As equipas de cuidados podem usar este painel para cuidar de pacientes que precisam de assistência e detetar sinais de deterioração precoce. Para aprender a construir um painel de fornecedores power BI em tempo real, siga o nosso [guia de como fazer.](howto-health-data-triage.md)

## <a name="next-steps"></a>Passos seguintes
* [Saiba como implementar um modelo de aplicação de monitorização contínua do paciente](tutorial-continuous-patient-monitoring.md)