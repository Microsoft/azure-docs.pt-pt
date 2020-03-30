---
title: Acelerador de solução de monitorização remota FAQ - Azure Microsoft Docs
description: Este artigo responde às perguntas frequentes para os aceleradores de soluções de monitorização remota.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826242"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Perguntas frequentes para acelerador de soluções de monitorização remota

Veja também, o [FAQ](iot-accelerators-faq.md)geral.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa fornecer a nova solução de Monitorização Remota?

O novo acelerador de soluções oferece duas opções de implementação:

* Uma opção *básica* projetada para desenvolvedores que procuram um menor custo de desenvolvimento ou clientes que procuram construir uma demonstração ou prova de conceito.
* Uma *opção-padrão* concebida para as empresas que pretendam implantar uma infraestrutura pronta para a produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como posso garantir que mantenho os meus custos baixos enquanto desfundo a minha solução?

Além de fornecer duas implementações diferenciadas, a nova solução de Monitorização Remota tem uma definição para ativar ou desativar todos os dispositivos simulados a pedido. Desativar a simulação reduz os dados ingeridos na solução e, assim, o custo global.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções básicas e standard de implementação? Como decido entre as duas opções de implantação?

Cada opção de implementação responde a diferentes necessidades. A implantação básica foi concebida para começar e desenvolver PoC e pequenos pilotos. Proporciona uma arquitetura simplificada com os recursos mínimos necessários e um custo mais baixo. A implementação padrão foi projetada para construir e personalizar uma solução pronta para a produção, e fornece uma implementação com os elementos necessários para perceber isso. Para fiabilidade e escala, os microserviços de aplicação são construídos como recipientes Docker e implantados usando um orquestrador (Kubernetes por padrão). O orquestrador é responsável pela implantação, escalação e gestão da aplicação. Deve escolher uma opção com base nas suas necessidades atuais. Podes usar um, o outro, ou uma combinação de ambos dependendo da fase do teu projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como configurar um mapa dinâmico no painel de instrumentos?

Para mais informações, consulte a chave do [mapa de upgrade para ver os dispositivos num mapa dinâmico](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de Monitorização Remota?

A versão anterior do acelerador de soluções de monitorização remota era conhecida como a solução preconfigurada de monitorização remota ioT Suite. Pode encontrar a documentação [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)arquivada em .

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore as capacidades do acelerador de soluções de monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar acelerador de solução de fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
