---
title: Introdução aos aceleradores de solução IoT - Azure | Microsoft Docs
description: Saiba mais sobre os aceleradores de soluções do Azure IoT. Os aceleradores de soluções IoT são completos, ponto a ponto e estão prontos para implementar soluções IoT.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c972211a48816409ff626396fb5ddc7c939c021b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057886"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>O que são os aceleradores de soluções IoT do Azure?

Uma solução IoT baseada na nuvem normalmente usa código personalizado e serviços de nuvem para gerir a conectividade do dispositivo, processamento de dados e análise, e apresentação.

Os aceleradores de soluções de IoT são soluções de IoT completas e prontas a implementar que aplicam cenários de IoT comuns. Os cenários incluem uma fábrica ligada e uma simulação de dispositivos. Quando implementa um acelerador de soluções, a implementação inclui todos os serviços baseados na cloud que são precisos, juntamente com qualquer código de aplicação exigido.

Os aceleradores de soluções são pontos de partida para as suas próprias soluções IoT. O código de origem de todos os aceleradores de soluções é aberto e está disponível no GitHub. Pode transferir e personalizar os aceleradores de soluções para satisfazer os seus requisitos.

Também pode utilizar os aceleradores de soluções como ferramentas de aprendizagem antes de compilar uma solução de IoT personalizada do zero. Os aceleradores de solução implementam práticas comprovadas para soluções IoT baseadas na cloud para acompanhar.

O código da aplicação em cada acelerador de soluções inclui uma aplicação Web que lhe permite gerir o mesmo.

> [!NOTE]
> As soluções de monitorização remota e manutenção preditiva foram removidas do local de aceleração da [solução Azure IoT.](https://www.azureiotsolutions.com/Accelerators) Para saber mais, veja quais são os aceleradores de [solução Azure IoT? (versão anterior)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Cenários IoT suportados

Atualmente, existem dois aceleradores de solução disponíveis para implementar:

### <a name="connected-factory"></a>Fábrica Ligada

Utilize o [acelerador de soluções Connected Factory](iot-accelerators-connected-factory-features.md) para recolher telemetria de ativos industriais com uma interface de Arquitetura Unificada [OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) e controlá-las. Os ativos industriais podem incluir estações de assemblagem e teste numa linha de produção de fábrica.

Pode utilizar o dashboard da fábrica ligada para monitorizar e gerir os seus dispositivos industriais:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Screenshot que mostra o painel de solução de fábrica conectado." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Simulação do Dispositivo

Utilize o [acelerador de solução de simulação](iot-accelerators-device-simulation-overview.md) do dispositivo para executar dispositivos simulados que gerem telemetria realista. Pode utilizar este acelerador de soluções para testar o comportamento de outros aceleradores de soluções ou para testar as suas próprias soluções IoT personalizadas.

Pode utilizar a aplicação Web de simulação de dispositivos para configurar e executar simulações:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Screenshot que mostra o painel de instrumentos de simulação do dispositivo." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Princípios de conceção

Todos os aceleradores de soluções seguem os mesmos princípios de design e objetivos. Foram criados para serem:

* **Dimensionáveis**, permitindo-lhe ligar e gerir milhões de dispositivos ligados.
* **Extensíveis**, permitindo-lhe personalizá-los para satisfazer os seus requisitos.
* **Compreensíveis**, permitindo-lhe entender como funcionam e como são implementados.
* **Modulares**, permitindo-lhe trocar serviços por alternativas.
* **Seguros**, ao combinar a segurança do Azure com funcionalidades de segurança de dispositivo e conectividade incorporadas.

## <a name="architectures-and-languages"></a>Arquiteturas e linguagens

Os aceleradores de soluções originais foram escritos com .NET através de uma arquitetura do modelo-vista-controlador (MVC). A Microsoft está a atualizar os aceleradores de soluções para uma nova arquitetura de microsserviços. A tabela abaixo mostra o estado atual dos aceleradores de soluções, com ligações para os repositórios do GitHub:

| Acelerador de soluções   | Arquitetura  | Idiomas     |
| ---------------------- | ------------- | ------------- |
| Fábrica Ligada      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulação do Dispositivo      | Microsserviços | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Para saber mais sobre a arquitetura de microserviços, consulte [Introdução à arquitetura de referência Azure IoT.](/azure/architecture/reference-architectures/iot/)

## <a name="deployment-options"></a>Opções de implementação

Pode implementar os aceleradores de soluções a partir do site [Aceleradores de Soluções do Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) ou com a linha de comandos.

O custo de funcionamento de um acelerador de solução é o custo combinado [de funcionamento dos serviços Azure subjacentes.](https://azure.microsoft.com/pricing) Verá os detalhes dos serviços do Azure utilizados ao escolher as suas opções de implementação.

## <a name="next-steps"></a>Passos seguintes

Para experimentar um dos aceleradores de solução IoT, confira o quickstart [Experimente uma solução de fábrica conectada.](quickstart-connected-factory-deploy.md)
