---
title: Arquitetura contínua de monitorização do paciente em Azure IoT Central Microsoft Docs
description: Conheça uma arquitetura contínua de solução de monitorização do paciente.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021701"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura da monitorização contínua do paciente



As soluções contínuas de monitorização do paciente podem ser construídas utilizando o modelo de aplicação fornecido e utilizando a arquitetura que está descrita abaixo como orientação.

>[!div class="mx-imgBorder"] 
>![Arquitetura CPM](media/cpm-architecture.png)

1. Dispositivos médicos que comunicam utilizando Bluetooth Low Energy (BLE)
1. Gateway de telemóvel recebendo dados BLE e enviando para a IoT Central
1. Exportação contínua de dados de saúde do doente para a API Azure para fhir&reg;
1. Aprendizagem automática com base em dados interoperáveis
1. Painel de cuidados construído com dados do FHIR

## <a name="details"></a>Detalhes
Esta secção descreve cada parte do diagrama de arquitetura com mais detalhes.

### <a name="ble-medical-devices"></a>Dispositivos médicos BLE
Muitos wearables médicos usados no espaço IoT de saúde são dispositivos Bluetooth Low Energy. Não conseguem falar diretamente com a nuvem e terão de passar por um portal. Esta arquitetura sugere usar uma aplicação de telemóvel como esta porta de entrada.

### <a name="mobile-phone-gateway"></a>Gateway do telemóvel
A função principal da aplicação de telemóvel é ingerir dados DE BLE a partir de dispositivos médicos e comunicá-lo à Azure IoT Central. Além disso, a aplicação pode ajudar a orientar os pacientes através de um fluxo de configuração e fornecimento de dispositivos e ajudá-los a ver uma visão dos seus dados pessoais de saúde. Outras soluções podem usar um portal de tablet ou um portal estático se dentro de um quarto de hospital para alcançar o mesmo fluxo de comunicação.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportação para API Azure para FHIR&reg;
A Azure IoT Central é certificada&reg; em conformidade com a HIPAA e certificada pela HITRUST, mas também pode querer enviar dados relacionados com a saúde do paciente para a API Azure para fhir. [A API Azure para FHIR](../../healthcare-apis/overview.md) é uma API totalmente gerida, baseada em padrões e compatível com dados de saúde clínica que lhe permite criar novos sistemas de envolvimento com os seus dados de saúde. Permite uma rápida troca de dados através de APIs FHIR, apoiado por uma oferta gerida de Serviço plataforma-as-a (PaaS) na nuvem. Utilizando a funcionalidade De Exportação Contínua de Dados da IoT Central, pode enviar dados para a API Azure para FHIR.

### <a name="machine-learning"></a>Aprendizagem automática
Depois de agregar os seus dados e traduzi-los em formato FHIR, pode construir modelos de aprendizagem automática que possam enriquecer insights e permitir uma tomada de decisão mais inteligente para a sua equipa de cuidados. Existem diferentes tipos de serviços que podem ser usados para construir, treinar e implementar modelos de aprendizagem automática. Mais informações sobre como usar as ofertas de machine learning do Azure podem ser encontradas na nossa documentação de [aprendizagem automática.](../../machine-learning/index.yml)

### <a name="provider-dashboard"></a>Painel de instrumentos do fornecedor
Os dados localizados na API Azure para FHIR podem ser usados para construir um painel de insights do paciente ou podem ser diretamente integrados numa EMR para ajudar as equipas de cuidados a visualizar o estado do paciente. As equipas de cuidados podem usar este painel para cuidar de pacientes que precisam de assistência e detetar sinais de alerta precoce de deterioração. Para aprender a construir um dashboard de fornecedor de power bi em tempo real, siga o nosso [guia de como guiar](howto-health-data-triage.md).

## <a name="next-steps"></a>Passos seguintes
* [Aprenda a implementar um modelo contínuo de aplicação de monitorização do paciente](tutorial-continuous-patient-monitoring.md)