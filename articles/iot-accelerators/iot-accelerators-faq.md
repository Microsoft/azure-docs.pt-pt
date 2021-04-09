---
title: Aceleradores de solução IoT FAQ - Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes para aceleradores de solução IoT. Inclui ligações aos repositórios do GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903994"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas mais frequentes sobre aceleradores de soluções do IoT

Consulte também as [FAQ específicas da fábrica conectada.](iot-accelerators-faq-cf.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código-fonte para os aceleradores de solução?

O código fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador de solução de fábrica conectado](https://github.com/Azure/azure-iot-connected-factory)
* [Acelerador de solução de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Onde posso encontrar os aceleradores de monitorização remota e de manutenção preditiva?

A partir de 10 de dezembro de 2020, os aceleradores de monitorização remota e de manutenção preditiva foram removidos do local de aceleração da [solução Azure IoT](https://www.azureiotsolutions.com/Accelerators) e já não estão disponíveis para novas implementações. Os repositórios do GitHub para ambos os aceleradores foram arquivados. O código ainda está disponível para qualquer um ter acesso, mas os repositórios não estão a receber novas contribuições.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>O que acontece com as minhas monitorizações remotas e operações de manutenção preditivas?

As implementações existentes não são afetadas pela remoção dos aceleradores de monitorização remota e de manutenção preditiva e continuarão a funcionar. Os repositórios forcados também não são impactados. Os repositórios mestres no GitHub foram arquivados.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Como posso implementar o acelerador de solução de simulação do dispositivo?

Para implementar o acelerador de solução de simulação do dispositivo, consulte o repositório de [simulação](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) do dispositivo GitHub.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Onde posso encontrar informações sobre os aceleradores de solução removidos?

Consulte as seguintes páginas no site das versões anteriores:

* [Monitorização remota](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Manutenção preditiva](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Que SDKs posso usar para desenvolver clientes de dispositivos para os aceleradores de solução?

Pode encontrar links para os diferentes idiomas (C, .NET, Java, Node.js, Python) dispositivoS SOT nos repositórios [GitHub do Microsoft Azure IoT SDKs.](https://github.com/Azure/azure-iot-sdks)

Se estiver a utilizar o dispositivo DevKit, pode encontrar recursos e amostras no repositório [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Sou administrador de serviços e gostaria de mudar o mapeamento do diretório entre a minha assinatura e um inquilino específico da AD Azure. Como posso completar esta tarefa?

Consulte [Para adicionar uma subscrição existente ao seu diretório AD Azure](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Quero alterar um Administrador de Serviço ou Co-Administrator quando iniciar sessão com uma conta organizacional

Consulte o artigo [De alteração de serviços Administrador e Co-Administrator quando iniciado sessão com uma conta organizacional](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Porque estou a ver este erro? "A sua conta não tem as permissões adequadas para criar uma solução. Consulte o seu administrador de conta ou tente com uma conta diferente."

Veja o seguinte diagrama para obter orientação:

![Permissões flowchart](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se continuar a ver o erro após validar é administrador global do inquilino AZure AD e coadministrador da subscrição, peça ao administrador da sua conta para remover o utilizador e reatribuir as permissões necessárias nesta ordem. Em primeiro lugar, adicione o utilizador como administrador global e, em seguida, adicione o utilizador como coadministrador da subscrição Azure. Se persistirem problemas, contacte [o Suporte & Ajuda](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Porque estou a ver este erro quando tenho uma subscrição do Azure? "Uma subscrição do Azure é necessária para criar soluções pré-configuradas. Pode criar uma conta de teste gratuita em apenas alguns minutos."

Se tiver a certeza de que tem uma assinatura Azure, valide o mapeamento do inquilino para a sua subscrição e verifique se o inquilino correto está selecionado no dropdown. Se validou o inquilino está correto, siga o diagrama anterior e valide o mapeamento da sua subscrição e deste inquilino AD AZure.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual é a diferença entre eliminar um grupo de recursos no portal Azure e clicar em excluir um acelerador de soluções em azureiotsolutions.com?

* Se eliminar o acelerador de solução em [azureiotsolutions.com,](https://www.azureiotsolutions.com/)elimina todos os recursos que foram utilizados quando criou o acelerador de solução. Se adicionar recursos adicionais ao grupo de recursos, estes recursos também são eliminados.
* Se eliminar o grupo de recursos no [portal Azure,](https://portal.azure.com)apenas eliminará os recursos desse grupo de recursos. Também é necessário eliminar a aplicação Azure Ative Directory associada ao acelerador de solução.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar a alavancar os meus investimentos existentes em aceleradores de solução Azure IoT?

Sim. Qualquer solução que exista hoje continua a funcionar na sua subscrição Azure e o código fonte permanece disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por predefinição, pode providenciar [10 hubs IoT por subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [bilhete de apoio Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite. Como resultado, uma vez que cada acelerador de soluções prevê um novo Hub IoT, só é possível providenciar até 10 aceleradores de solução numa determinada subscrição.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de DB da Azure Cosmos posso providenciar numa subscrição?

Cinquenta. Você pode criar um [bilhete de suporte Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para elevar este limite, mas por padrão, você só pode providenciar 50 instâncias Cosmos DB por subscrição.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um acelerador de soluções se tiver o Microsoft Azure para o DreamSpark?

> [!NOTE]
> O Microsoft Azure for DreamSpark é agora conhecido como Microsoft Imagine para estudantes.

Atualmente, não é possível criar um acelerador de soluções com uma conta [Microsoft Azure para DreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) No entanto, pode criar uma [conta de teste gratuita para o Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que lhe permite criar um acelerador de solução.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Como apago um inquilino da AD Azure?

Veja o post de Eric Golpe no blog [Walkthrough of Deleting a Azure AD Tenant](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Implementar acelerador de solução de fábrica conectado](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)