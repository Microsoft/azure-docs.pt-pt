---
title: Acelerador de solução de monitorização remota FAQ - Azure / Microsoft Docs
description: Este artigo responde às perguntas frequentes para os aceleradores de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73826242"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Perguntas frequentes para acelerador de solução de monitorização remota

Consulte também, o [FAQ](iot-accelerators-faq.md)geral.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa a disponibilização da nova solução de Monitorização Remota?

O novo acelerador de soluções oferece duas opções de implantação:

* Uma opção *básica* projetada para desenvolvedores que procuram um menor custo de desenvolvimento ou clientes que procuram construir uma demonstração ou prova de conceito.
* Uma opção *padrão* concebida para as empresas que pretendam implantar uma infraestrutura pronta para a produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como posso assegurar-me de que mantenho os meus custos baixos enquanto desenvolvo a minha solução?

Além de fornecer duas implementações diferenciadas, a nova solução de Monitorização Remota tem uma definição para ativar ou desativar todos os dispositivos simulados a pedido. Desativar a simulação reduz os dados ingeridos na solução e, assim, o custo global.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções básicas e padrão de implantação? Como decido entre as duas opções de implantação?

Cada opção de implantação responde a diferentes necessidades. A implantação básica foi concebida para começar e desenvolver PoC e pequenos pilotos. Proporciona uma arquitetura simplificada com os recursos mínimos necessários e um custo mais baixo. A implementação padrão é projetada para construir e personalizar uma solução pronta a produção, e fornece uma implantação com os elementos necessários para perceber isso. Para fiabilidade e escala, os microserviços de aplicação são construídos como recipientes Docker e implantados usando um orquestrador (Kubernetes por padrão). O orquestrador é responsável pela implantação, escala e gestão da aplicação. Deve escolher uma opção com base nas suas necessidades atuais. Pode usar um, o outro, ou uma combinação de ambos dependendo da fase do seu projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como posso configurar um mapa dinâmico no painel de instrumentos?

Para obter mais informações, consulte [a chave do mapa de upgrade para ver os dispositivos num mapa dinâmico.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de Monitorização Remota?

A versão anterior do acelerador de solução de monitorização remota era conhecida como a solução pré-configurada de monitorização remota IoT Suite. Pode encontrar a documentação arquivada em [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore as capacidades do acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar acelerador de solução de fábrica conectado](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
