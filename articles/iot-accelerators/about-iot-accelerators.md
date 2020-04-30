---
title: Introdução aos aceleradores de soluções IoT - Azure [ Microsoft Docs
description: Saiba mais sobre os aceleradores de soluções do Azure IoT. Os aceleradores de soluções IoT são completos, ponto a ponto e estão prontos para implementar soluções IoT.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 1a27d748e16f892a748cf18569c13ca3f9ead1dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "71309507"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>O que são os aceleradores de soluções IoT do Azure?

Uma solução IoT baseada na nuvem normalmente usa código personalizado e serviços de nuvem para gerir a conectividade do dispositivo, processamento de dados e análise, e apresentação.

Os aceleradores de soluções de IoT são soluções de IoT completas e prontas a implementar que aplicam cenários de IoT comuns. Os cenários incluem monitorização remota, fábrica ligada, manutenção preditiva e simulação de dispositivos. Quando implementa um acelerador de soluções, a implementação inclui todos os serviços baseados na cloud que são precisos, juntamente com qualquer código de aplicação exigido.

Os aceleradores de soluções são pontos de partida para as suas próprias soluções IoT. O código de origem de todos os aceleradores de soluções é aberto e está disponível no GitHub. Pode transferir e personalizar os aceleradores de soluções para satisfazer os seus requisitos.

Também pode utilizar os aceleradores de soluções como ferramentas de aprendizagem antes de compilar uma solução de IoT personalizada do zero. Os aceleradores de solução implementam práticas comprovadas para soluções IoT baseadas na cloud para acompanhar.

O código da aplicação em cada acelerador de soluções inclui uma aplicação Web que lhe permite gerir o mesmo.

## <a name="supported-iot-scenarios"></a>Cenários IoT suportados

Atualmente, existem quatro aceleradores de soluções disponíveis para implementar:

### <a name="remote-monitoring"></a>Monitorização Remota

Utilize o acelerador de [solução de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md) para recolher telemetria a partir de dispositivos remotos e para os controlar. Os dispositivos de exemplo incluem sistemas de arrefecimento instalados nas instalações dos seus clientes ou válvulas instaladas em estações de bombeamento remotas.

Pode utilizar o dashboard de monitorização remota para ver a telemetria dos seus dispositivos ligados, aprovisionar novos dispositivos ou atualizar o firmware nos seus dispositivos ligados:

[![Painel de solução de monitorização remota](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Fábrica Ligada

Utilize o acelerador de [soluções Connected Factory](iot-accelerators-connected-factory-features.md) para recolher telemetria de ativos industriais com interface de [Arquitetura Unificada OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) e para controlá-los. Os ativos industriais podem incluir estações de assemblagem e teste numa linha de produção de fábrica.

Pode utilizar o dashboard da fábrica ligada para monitorizar e gerir os seus dispositivos industriais:

[![Painel de solução de fábrica conectado](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Manutenção Preditiva

Utilize o acelerador de solução de [manutenção preditiva](iot-accelerators-predictive-walkthrough.md) para prever quando se espera que um dispositivo remoto falhe para que possa efetuar a manutenção antes que o dispositivo falhe. Este acelerador de soluções utiliza algoritmos de aprendizagem automática para prever falhas de telemetria do dispositivo. Os dispositivos de exemplo podem ser motores de avião ou elevadores.

Pode utilizar o dashboard da manutenção preditiva para ver análises de manutenção preditiva:

[![Painel de solução de fábrica conectado](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Simulação do Dispositivo

Utilize o acelerador de [solução de simulação](iot-accelerators-device-simulation-overview.md) de dispositivos para executar dispositivos simulados que gerem telemetria realista. Pode utilizar este acelerador de soluções para testar o comportamento de outros aceleradores de soluções ou para testar as suas próprias soluções IoT personalizadas.

Pode utilizar a aplicação Web de simulação de dispositivos para configurar e executar simulações:

[![Painel de solução de fábrica conectado](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Princípios de conceção

Todos os aceleradores de soluções seguem os mesmos princípios de design e objetivos. Foram criados para serem:

* **Dimensionáveis**, permitindo-lhe ligar e gerir milhões de dispositivos ligados.
* **Extensíveis**, permitindo-lhe personalizá-los para satisfazer os seus requisitos.
* **Compreensíveis**, permitindo-lhe entender como funcionam e como são implementados.
* **Modulares**, permitindo-lhe trocar serviços por alternativas.
* **Seguros**, ao combinar a segurança do Azure com funcionalidades de segurança de dispositivo e conectividade incorporadas.

## <a name="architectures-and-languages"></a>Arquiteturas e linguagens

Os aceleradores de soluções originais foram escritos com .NET através de uma arquitetura do modelo-vista-controlador (MVC). A Microsoft está a atualizar os aceleradores de soluções para uma nova arquitetura de microsserviços. A tabela abaixo mostra o estado atual dos aceleradores de soluções, com ligações para os repositórios do GitHub:

| Acelerador de soluções   | Arquitetura  | Linguagens     |
| ---------------------- | ------------- | ------------- |
| Monitorização Remota      | Microsserviços | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) e [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Manutenção Preditiva | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Fábrica Ligada      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulação do Dispositivo      | Microsserviços | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Para saber mais sobre a arquitetura dos microserviços, consulte Introdução à arquitetura de [referência Azure IoT.](https://docs.microsoft.com/azure/architecture/reference-architectures/iot/)

## <a name="deployment-options"></a>Opções de implementação

Pode implementar os aceleradores de soluções a partir do site [Aceleradores de Soluções do Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) ou com a linha de comandos.

Pode implementar o acelerador de soluções de Monitorização Remota nas seguintes configurações:

* **Standard:** implementação de infraestrutura expandida para desenvolver uma implementação de produção. O Serviço de Contentores Azure implanta os microserviços em várias máquinas virtuais Azure. O Kubernetes orquestra os contentores do Docker que alojam os microsserviços individuais.
* **Básica:** versão com custo reduzido para demonstração ou para testar implementações. Todos os microsserviços são implementados numa máquina virtual do Azure individual.
* **Local:** implementação de computador local para teste e desenvolvimento. Esta abordagem implementa os microsserviços num contentor do Docker local e é ligada ao Hub IoT, ao Azure Cosmos DB e aos serviços de armazenamento do Azure na cloud.

O custo de funcionamento de um acelerador de soluções é o custo combinado [de funcionamento dos serviços Azure subjacentes.](https://azure.microsoft.com/pricing) Verá os detalhes dos serviços do Azure utilizados ao escolher as suas opções de implementação.

## <a name="next-steps"></a>Passos seguintes

Para experimentar um dos aceleradores de soluções de IoT, veja os inícios rápidos:

* [Tentar uma solução de monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Tentar uma solução de fábrica ligada](quickstart-connected-factory-deploy.md)
* [Try a predictive maintenance solution](quickstart-predictive-maintenance-deploy.md) (Experimentar uma solução de manutenção preditiva)
* [Try a device simulation solution](quickstart-device-simulation-deploy.md) (Testar uma solução de simulação de dispositivos)
