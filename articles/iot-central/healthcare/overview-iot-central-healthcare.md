---
title: Construir soluções de saúde com a Azure IoT Central [ Central De Azure IoT] Microsoft Docs
description: Aprenda a construir uma solução de saúde utilizando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021496"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Criar soluções para os cuidados de saúde com o Azure IoT Central 



Aprenda a construir soluções de saúde com a Azure IoT Central utilizando modelos de aplicação.

## <a name="what-is-continuous-patient-monitoring-template"></a>O que é o modelo contínuo de monitorização do paciente?

No espaço IoT dos cuidados de saúde, a Monitorização Contínua do Doente é um dos principais facilitadores da redução do risco de readmissões, gestão de doenças crónicas de forma mais eficaz e melhoria dos resultados do paciente. A monitorização contínua do paciente pode ser dividida em duas categorias principais:

1. **Monitorização do paciente**: Utilizando wearables médicos e outros dispositivos no hospital, as equipas de cuidados podem monitorizar os sinais vitais do paciente e as condições médicas sem terem de enviar uma enfermeira para verificar um paciente várias vezes ao dia. As equipas de cuidados podem entender o momento em que um paciente precisa de atenção crítica através de notificações e prioriza o seu tempo de forma eficaz.
1. **Monitorização remota do paciente**: Utilizando wearables médicos e resultados reportados (ROP) para monitorizar pacientes fora do hospital, o risco de readmissão pode ser reduzido. Os dados dos doentes crónicos e dos doentes de reabilitação podem ser recolhidos para garantir que os pacientes estão a aderindo aos planos de cuidados e que os alertas de deterioração do paciente podem ser apresentados às equipas de cuidados antes de se tornarem críticos.

Este modelo de aplicação pode ser usado para construir soluções para ambas as categorias de Monitorização Contínua do Paciente. Os benefícios incluem:

* Ligue perfeitamente diferentes tipos de wearables médicos a uma instância central iot.
* Monitorize e gerencie os dispositivos para garantir que se mantenham saudáveis.
* Crie regras personalizadas em torno dos dados do dispositivo para desencadear alertas apropriados.
* Exporte os dados de saúde do seu paciente para a API Azure para fhir, uma loja de dados conforme.
* Exportar os conhecimentos agregados sobre as aplicações comerciais existentes ou novas.

>[!div class="mx-imgBorder"] 
>![Painel de instrumentos CPM](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Para começar a construir uma solução de monitorização contínua do paciente:

* [Implementar o modelo de aplicação](tutorial-continuous-patient-monitoring.md)
* [Ver um exemplo de arquitetura](concept-continuous-patient-monitoring-architecture.md)