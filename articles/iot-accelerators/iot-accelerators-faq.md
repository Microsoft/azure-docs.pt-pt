---
title: Aceleradores de solução de IoT do Azure FAQ | Documentos da Microsoft
description: Perguntas mais frequentes sobre os Aceleradores de solução de IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 154d82ab5e309631df0bfa40df7fe14902327f46
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884280"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas mais frequentes sobre os Aceleradores de solução de IoT

Além disso, consulte a [FAQ sobre a específicos de fábrica ligada](iot-accelerators-faq-cf.md) e o [FAQ de específicos de monitorização remota](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código-fonte para os Aceleradores de solução?

O código-fonte é armazenado nos repositórios do GitHub seguintes:

* [Acelerador de solução monitorização remota (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Remoto acelerador de solução de monitorização (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de solução de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acelerador de solução de fábrica ligada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Os SDKs que uso para desenvolver clientes de dispositivos para os Aceleradores de solução?

Pode encontrar links para os SDKs do dispositivo de IoT de idioma diferente (C, .NET, Java, node. js, Python) na [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) repositórios do GitHub.

Se estiver a utilizar o dispositivo DevKit, pode encontrar recursos e exemplos na [SDK do IoT DevKit](https://github.com/Microsoft/devkit-sdk) repositório do GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Está disponível para todos os três solution accelerators-o a nova arquitetura de microsserviços?

Atualmente, apenas a solução de monitorização remota utiliza a arquitetura de microsserviços, pois abrange o cenário mais ampla.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>As vantagens que a nova aberto baseadas em microsserviços arquitetura fornece na atualização de novo?

Nos últimos dois anos, arquitetura de nuvem bastante evoluiu. Microsserviços surgiram como um excelente padrão para alcançar dimensionamento e flexibilidade, sem sacrificar a rapidez do desenvolvimento. Este padrão de arquitetura é utilizado em vários serviços do Microsoft internamente com confiabilidade excelente e resultados de escalabilidade. Que estamos colocando estes de aprendizagem na prática, para que os nossos clientes beneficiam deles.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de monitorização remota?

A versão anterior do solution accelerator monitorização remota era conhecida como a solução de IoT Suite monitorização remota pré-configurada. Pode encontrar a documentação arquivada em [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>O novo acelerador de solução está disponível na mesma região geográfica que a solução existente?

Sim, a monitorização remota novos está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>O que é a diferença entre a eliminação de um grupo de recursos no portal do Azure e clicar eliminar num acelerador de solução em azureiotsuite.com?

* Se eliminar o solution accelerator na [azureiotsuite.com](https://www.azureiotsolutions.com/), eliminar todos os recursos que foram aprovisionados quando criou o solution accelerator. Se adicionou recursos adicionais para o grupo de recursos, esses recursos também são eliminados.
* Se eliminar o grupo de recursos no [portal do Azure](https://portal.azure.com), só é eliminar os recursos nesse grupo de recursos. Também terá de eliminar a aplicação do Azure Active Directory associada com o solution accelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar a tirar partido do meu investimentos existentes em Aceleradores de solução de IoT do Azure?

Sim. Qualquer solução que hoje em dia exista continua a funcionar na sua subscrição do Azure e o código-fonte permanece disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por predefinição, pode aprovisionar [10 hubs IoT por subscrição](../azure-subscription-service-limits.md#iot-hub-limits). Pode criar uma [pedido de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite. Como resultado, desde que todas as provisões de acelerador de solução um novo IoT Hub, apenas pode aprovisionar até 10 Aceleradores de solução numa determinada subscrição.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB pode aprovisionar numa subscrição?

Cinquenta. Pode criar uma [pedido de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite, mas por predefinição, apenas pode aprovisionar 50 instâncias do Cosmos DB por subscrição.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. A solução de monitorização remota é aprovisionada por predefinição com o plano de 1 de nível de transações internas. Como resultado, apenas pode aprovisionar até duas monitorização remota soluções numa subscrição sem qualquer modificação.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>É possível criar um acelerador de solução se tiver o Microsoft Azure para DreamSpark?

> [!NOTE]
> Microsoft Azure para DreamSpark é agora conhecido como Microsoft Imagine para estudantes.

Atualmente, não é possível criar um acelerador de solução com um [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) conta. No entanto, pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que lhe permitem criar um acelerador de solução.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso eliminar um inquilino do AAD?

Consulte a postagem de blog de Eric Golpe [instruções de eliminação de um inquilino do Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passos Seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore os recursos do solution accelerator monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar o acelerador de solução de fábrica ligada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
