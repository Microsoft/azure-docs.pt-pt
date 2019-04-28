---
title: Remoto monitorização solution accelerator FAQ - Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 746d21c7ff4d5b939eea7690193ac07425b4001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447918"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Perguntas mais frequentes sobre acelerador de solução de monitorização remota

Além disso, consulte gerais [FAQ](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa para aprovisionar a nova solução de monitorização remota?

O novo solution accelerator oferece duas opções de implementação:

* R *básica* opção concebida para programadores à procura de menor custo de desenvolvimento ou os clientes que pretendem para criar uma demonstração ou prova de conceito.
* R *padrão* opção projetada para empresas que pretendem implementar uma infraestrutura de prontos para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como posso garantir que eu manter meu os custos baixos enquanto posso programar minha solução?

Além de fornecer duas implementações diferenciadas, a nova solução de monitorização remota tem uma definição para ativar ou desativar todos os dispositivos simulados a pedido. Desativar a simulação reduz os dados ingeridos a solução e, portanto, o custo geral.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>O que é a diferença entre as opções de implementação de básico e standard? Como posso decidir entre as opções de implementação de dois?

Cada opção de implementação responde às necessidades diferentes. A implementação básica destina-se para começar e desenvolver prova de conceito e pilotos pequenos. Ele fornece uma arquitetura simplificada com os recursos necessários mínimo e um custo mais baixo. A implementação padrão de foi concebida para criar e personalizar uma solução de prontos para produção e fornece uma implementação com os elementos necessários para perceber que. Para fiabilidade e dimensionamento, os microsserviços da aplicação são criados como contentores do Docker e implementados com um orquestrador (Kubernetes por predefinição). O orquestrador é responsável por implementar, dimensionar e gerir a aplicação. Deve escolher uma opção com base nas suas necessidades atuais. Pode usar um, a outra ou uma combinação de ambos, consoante a sua fase do projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como posso configurar um mapa dinâmico no dashboard?

Para obter mais informações, consulte [chave de atualização de mapa para ver os dispositivos num mapa dinâmico](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de monitorização remota?

A versão anterior do solution accelerator monitorização remota era conhecida como a solução de IoT Suite monitorização remota pré-configurada. Pode encontrar a documentação arquivada em [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Passos Seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore os recursos do solution accelerator monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar o acelerador de solução de fábrica ligada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
