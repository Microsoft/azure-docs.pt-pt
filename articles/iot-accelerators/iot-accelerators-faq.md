---
title: Aceleradores de soluções IoT FAQ - Azure Microsoft Docs
description: Este artigo responde às perguntas frequentes para aceleradores de soluções IoT. Inclui ligações aos repositórios GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647718"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas mais frequentes sobre aceleradores de soluções do IoT

Consulte também as [FAQ específicas](iot-accelerators-faq-cf.md) da Fábrica Conectada e as [FAQ específicas](iot-accelerators-faq-rm-v2.md) da monitorização remota .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código fonte para os aceleradores de solução?

O código fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador de solução de monitorização remota (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acelerador de solução de monitorização remota (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de solução de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acelerador de solução de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Que SDKs posso usar para desenvolver clientes de dispositivos para os aceleradores de soluções?

Pode encontrar links para os diferentes idiomas (C, .NET, Java, Node.js, Python) dispositivo IoT sDKs nos repositórios GitHub da [Microsoft Azure IoT SDKs.](https://github.com/Azure/azure-iot-sdks)

Se estiver a utilizar o dispositivo DevKit, pode encontrar recursos e amostras no repositório [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>A nova arquitetura de microserviços está disponível para todos os três aceleradores de soluções?

Atualmente, apenas a solução de Monitorização Remota utiliza a arquitetura de microserviços, uma vez que cobre o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Que vantagens a nova arquitetura baseada em microserviços de origem aberta proporciona na nova atualização?

Nos últimos dois anos, a arquitetura em nuvem evoluiu muito. Os microserviços surgiram como um grande padrão para alcançar a escala e a flexibilidade, sem sacrificar a velocidade de desenvolvimento. Este padrão arquitetónico é usado em vários serviços da Microsoft internamente com grande fiabilidade e resultados de escalabilidade. A Microsoft está a pôr estas aprendizagens em prática nos aceleradores de soluções para que os clientes beneficiem deles.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Sou administrador de serviços e gostaria de mudar o mapeamento de diretório entre a minha assinatura e um inquilino específico da AD Azure. Como posso completar esta tarefa?

Ver [Adicionar uma subscrição existente ao seu diretório Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Quero mudar um Administrador de Serviço ou Coadministrador quando iniciar sessão com uma conta organizacional

Consulte o artigo de apoio [Change Service Administrator and Co-Administrator quando iniciar sessão com uma conta organizacional](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Porque estou a ver este erro? "A sua conta não tem as permissões adequadas para criar uma solução. Consulte o seu administrador de conta ou tente com uma conta diferente."

Veja o seguinte diagrama para orientação:

![Fluxograma de permissões](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se continuar a ver o erro depois de validar que é administrador global do inquilino da AD Azure e coadministrador da subscrição, peça ao administrador da sua conta que remova o utilizador e redesigne as permissões necessárias nesta ordem. Em primeiro lugar, adicione o utilizador como administrador global e, em seguida, adicione o utilizador como coadministrador da subscrição Azure. Se persistirem problemas, contacte [a Ajuda & Suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Porque estou a ver este erro quando tenho uma subscrição do Azure? "É necessária uma subscrição azure para criar soluções pré-configuradas. Pode criar uma conta de teste gratuita em apenas alguns minutos."

Se tem a certeza de que tem uma assinatura Azure, valide o mapeamento do inquilino para a sua subscrição e verifique se o inquilino correto está selecionado no dropdown. Se validou o inquilino está correto, siga o diagrama anterior e valide o mapeamento da sua subscrição e deste inquilino da AD Azure.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de Monitorização Remota?

A versão anterior do acelerador de soluções de monitorização remota era conhecida como a solução preconfigurada de monitorização remota ioT Suite. Pode encontrar a documentação [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)arquivada em .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>O novo acelerador de soluções está disponível na mesma região geográfica que a solução existente?

Sim, a nova Monitorização Remota está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual é a diferença entre eliminar um grupo de recursos no portal Azure e clicar em apagar num acelerador de soluções em azureiotsolutions.com?

* Se eliminar o acelerador de soluções em [azureiotsolutions.com,](https://www.azureiotsolutions.com/)elimina todos os recursos que foram implantados quando criou o acelerador de soluções. Se adicionou recursos adicionais ao grupo de recursos, estes recursos também são eliminados.
* Se eliminar o grupo de recursos no [portal Azure,](https://portal.azure.com)apenas elimina os recursos desse grupo de recursos. Também é necessário eliminar a aplicação Azure Ative Directory associada ao acelerador de soluções.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar a alavancar os meus investimentos existentes em aceleradores de soluções Azure IoT?

Sim. Qualquer solução que exista hoje continua a funcionar na sua subscrição Azure e o código fonte permanece disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por predefinição, pode fornecer [10 hubs IoT por subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um bilhete de [apoio Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite. Como resultado, uma vez que cada acelerador de soluções prevê um novo Hub IoT, só pode fornecer até 10 aceleradores de soluções numa determinada subscrição.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias da Azure Cosmos DB posso fornecer numa subscrição?

Cinquenta. Você pode criar um bilhete de [suporte Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite, mas por padrão, você só pode fornecer 50 instâncias Cosmos DB por subscrição.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. A solução de Monitorização Remota é aprovisionada por padrão com o plano nível 1 de Transações Internas. Como resultado, só é possível fornecer até duas soluções de Monitorização Remota numa subscrição sem alterações.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um acelerador de soluções se tiver o Microsoft Azure para o DreamSpark?

> [!NOTE]
> O Microsoft Azure para dreamSpark é agora conhecido como Microsoft Imagine para estudantes.

Atualmente, não é possível criar um acelerador de soluções com uma conta [Microsoft Azure para dreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) No entanto, pode criar uma [conta de teste gratuita para o Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que lhe permita criar um acelerador de soluções.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Como posso apagar um inquilino da AD Azure?

Veja o blog de Eric Golpe [walkthrough of Deleting a Azure AD Tenant](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore as capacidades do acelerador de soluções de monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implementar acelerador de solução de fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
