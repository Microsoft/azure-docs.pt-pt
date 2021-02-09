---
title: Quais são as soluções de saúde Azure IoT Central | Microsoft Docs
description: Aprenda a construir solução de cuidados de saúde usando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: e8a72195f0fcacce2c994e8770157b05b65d70ee
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833155"
---
# <a name="what-are-the-iot-central-healthcare-solutions"></a>Quais são as soluções de cuidados de saúde da IoT Central?

Aprenda a construir soluções de cuidados de saúde com a Azure IoT Central utilizando modelos de aplicação.

## <a name="what-is-continuous-patient-monitoring-template"></a>O que é o modelo de monitorização contínua do paciente?

No espaço IoT dos cuidados de saúde, a Monitorização Contínua do Doente é um dos principais facilitadores da redução do risco de internamento, gestão de doenças crónicas de forma mais eficaz e melhoria dos resultados do doente. A Monitorização Contínua do Doente pode ser dividida em duas categorias principais:

1. **Monitorização no doente**: Utilizando wearables médicos e outros dispositivos no hospital, as equipas de cuidados podem monitorizar os sinais vitais do paciente e as condições médicas sem terem de enviar uma enfermeira para verificar um paciente várias vezes ao dia. As equipas de cuidados podem compreender o momento em que um paciente precisa de atenção crítica através de notificações e prioriza o seu tempo de forma eficaz.
1. **Monitorização remota do paciente**: Utilizando wearables médicos e pacientes reportados resultados (PROs) para monitorizar pacientes fora do hospital, o risco de internamento pode ser reduzido. Os dados dos doentes com doenças crónicas e dos doentes de reabilitação podem ser recolhidos para garantir que os pacientes estão a aderir aos planos de cuidados e que os alertas de deterioração do paciente podem ser apresentados às equipas de cuidados antes que se tornem críticos.

Este modelo de aplicação pode ser usado para construir soluções para ambas as categorias de Monitorização Contínua do Paciente. Os benefícios incluem:

* Ligar perfeitamente diferentes tipos de wearables médicos a um caso IoT Central.
* Monitorize e gere os dispositivos para garantir que se mantenham saudáveis.
* Crie regras personalizadas em torno dos dados do dispositivo para desencadear alertas apropriados.
* Exporte os dados de saúde do seu paciente para a AZure API para fHIR, uma loja de dados compatível.
* Exportar os conhecimentos agregados para aplicações comerciais existentes ou novas.

>[!div class="mx-imgBorder"] 
>![Painel CPM](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Para começar a construir uma solução de monitorização contínua do paciente:

* [Implementar o modelo de aplicação](tutorial-continuous-patient-monitoring.md)
* [Veja um exemplo de arquitetura](concept-continuous-patient-monitoring-architecture.md)