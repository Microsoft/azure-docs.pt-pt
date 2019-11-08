---
title: Perguntas frequentes do acelerador de solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre os aceleradores de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826242"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Perguntas frequentes sobre o acelerador de solução de monitoramento remoto

Consulte também as [perguntas frequentes](iot-accelerators-faq.md)gerais.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa para provisionar a nova solução de monitoramento remoto?

O novo Solution Accelerator oferece duas opções de implantação:

* Uma opção *básica* projetada para desenvolvedores que buscam custos de desenvolvimento menores ou clientes que buscam criar uma demonstração ou prova de conceito.
* Uma opção *padrão* projetada para empresas que desejam implantar uma infraestrutura pronta para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como posso garantir que eu mantenha meus custos inativos enquanto desenvolva minha solução?

Além de fornecer duas implantações diferenciadas, a nova solução de monitoramento remoto tem uma configuração para habilitar ou desabilitar todos os dispositivos simulados sob demanda. A desabilitação da simulação reduz os dados ingeridos na solução e, portanto, o custo geral.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções de implantação básica e padrão? Como fazer decidir entre as duas opções de implantação?

Cada opção de implantação responde a diferentes necessidades. A implantação básica foi projetada para começar e desenvolver PoC e pilotos pequenos. Ele fornece uma arquitetura simplificada com os recursos mínimos necessários e um custo mais baixo. A implantação padrão foi projetada para criar e personalizar uma solução pronta para produção e fornece uma implantação com os elementos necessários para perceber isso. Para confiabilidade e escala, os microserviços do aplicativo são criados como contêineres do Docker e implantados usando um orquestrador (kubernetes por padrão). O orquestrador é responsável por implementar, dimensionar e gerir a aplicação. Você deve escolher uma opção com base em suas necessidades atuais. Você pode usar um, o outro ou uma combinação de ambos, dependendo do estágio do projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como fazer configurar um mapa dinâmico no painel?

Para obter mais informações, consulte [Atualizar chave do mapa para ver os dispositivos em um mapa dinâmico](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de monitoramento remoto?

A versão anterior do acelerador de solução de monitoramento remoto era conhecida como a solução pré-configurada de monitoramento remoto IoT Suite. Você pode encontrar a documentação arquivada em [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore os recursos do acelerador de solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implantar o Solution Accelerator da fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
