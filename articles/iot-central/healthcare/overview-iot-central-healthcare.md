---
title: Crie soluções de saúde com o Azure IoT Central | Microsoft Docs
description: Aprenda a criar uma solução de saúde usando os modelos de aplicativo IoT Central do Azure.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: a5a8c8e52c4bebda4e22e592fefa3801449504e3
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027460"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Criando soluções de saúde com o Azure IoT Central 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Aprenda a criar soluções de saúde com o Azure IoT Central usando modelos de aplicativos.

## <a name="what-is-continuous-patient-monitoring-template"></a>O que é o modelo de monitoramento de pacientes contínuo?

No espaço de IoT de saúde, o monitoramento contínuo de pacientes é um dos principais capacitadores de reduzir o risco de readmissão, gerenciar crônicos doenças com mais eficiência e melhorar os resultados do paciente. O monitoramento contínuo do paciente pode ser dividido em duas categorias principais:

1. **Monitoramento no paciente**: usando wearables médicos e outros dispositivos no hospital, as equipes de atendimento podem monitorar os sinais médicos do paciente e as condições médicas sem precisar enviar um enfermaria para fazer o check-in de um paciente várias vezes por dia. As equipes de cuidados podem entender o momento em que um paciente precisa de atenção crítica por meio de notificações e prioriza seu tempo de forma eficaz.
1. **Monitoramento remoto do paciente**: ao usar o wearables médico e os profissionais (resultados relatados pelo paciente) para monitorar pacientes fora do hospital, o risco de readmissão pode ser significativamente reduzido. Os dados de pacientes de doenças crônicos e pacientes lei reabilitação podem ser coletados para garantir que os pacientes estejam cumprindo os planos de atendimento e que os alertas de deterioração do paciente possam ser exibidos para as equipes de ti antes de se tornarem críticos.

Este modelo de aplicativo pode ser usado para criar soluções para as duas categorias de monitoramento contínuo de pacientes. Os benefícios incluem:

* Conecte diretamente uma variedade de wearables médicos a uma instância IoT Central.
* Monitore e gerencie os dispositivos para garantir que eles permaneçam íntegros.
* Crie regras personalizadas em volta dos dados do dispositivo para disparar os alertas apropriados.
* Exporte seus dados de integridade do paciente para a API do Azure para FHIR, um armazenamento de dados em conformidade.
* Exporte as informações agregadas em aplicativos de negócios novos ou existentes que capacitam as equipes de atendimento.

>[!div class="mx-imgBorder"] 
>![CPM-Dashboard](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Para começar a criar uma solução contínua de monitoramento de pacientes:

* [Implantar o modelo de aplicativo](tutorial-continuous-patient-monitoring.md)
* [Veja uma arquitetura de exemplo](concept-continuous-patient-monitoring-architecture.md)