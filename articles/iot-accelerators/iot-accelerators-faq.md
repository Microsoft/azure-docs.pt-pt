---
title: Perguntas frequentes sobre aceleradores de solução de IoT – Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre os aceleradores de solução de IoT. Ele inclui links para os repositórios do GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647718"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas frequentes sobre os aceleradores de solução de IoT

Consulte também as [perguntas frequentes específicas de fábrica conectadas](iot-accelerators-faq-cf.md) e as [perguntas frequentes específicas do monitoramento remoto](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código fonte para os aceleradores de soluções?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador de solução de monitoramento remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acelerador de solução de monitoramento remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de solução de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solution Accelerator da fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Que SDKs posso utilizar para desenvolver clientes de dispositivo para os aceleradores de soluções?

Você pode encontrar links para os SDKs do dispositivo IoT de linguagem (C, .NET, Java, Node. js, Python) nos repositórios GitHub de [SDKs do Microsoft Azure IOT](https://github.com/Azure/azure-iot-sdks) .

Se você estiver usando o dispositivo DevKit, poderá encontrar recursos e amostras no repositório GitHub do [SDK do devkit do IOT](https://github.com/Microsoft/devkit-sdk) .

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>A nova arquitetura de microserviços está disponível para todos os três aceleradores de solução?

Atualmente, apenas a solução de monitoramento remoto usa a arquitetura de microserviço, pois abrange o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quais são as vantagens que a nova arquitetura baseada em microserviços de software livre fornece na nova atualização?

Nos últimos dois anos, a arquitetura de nuvem evoluiu muito. Os microserviços surgiram como um ótimo padrão para alcançar escala e flexibilidade, sem sacrificar a velocidade de desenvolvimento. Esse padrão de arquitetura é usado em vários serviços da Microsoft internamente com ótimos resultados de confiabilidade e escalabilidade. A Microsoft está fazendo esses aprendizados em prática nos aceleradores de solução para que os clientes se beneficiem deles.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Sou administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário específico do Azure AD. Como fazer concluir esta tarefa?

Consulte [para adicionar uma assinatura existente ao seu diretório do Azure ad](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Desejo alterar um administrador de serviços ou coadministrador quando estiver conectado com uma conta institucional

Consulte o artigo de suporte [alterando administrador de serviços e coadministrador quando conectado com uma conta organizacional](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Porque estou a ver este erro? "A sua conta não tem as permissões adequadas para criar uma solução. Consulte o seu administrador de conta ou tente com uma conta diferente."

Examine o seguinte diagrama para obter orientação:

![Fluxograma de permissões](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se você continuar vendo o erro depois de validar, você é um administrador global do locatário do Azure AD e um coadministrador da assinatura, peça ao administrador da conta para remover o usuário e reatribuir as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e, em seguida, adicione o usuário como um coadministrador da assinatura do Azure. Se os problemas persistirem, contate [a ajuda & suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Porque estou a ver este erro quando tenho uma subscrição do Azure? "Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. "

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento de locatário para sua assinatura e verifique se o locatário correto está selecionado na lista suspensa. Se você validou que o locatário está correto, siga o diagrama anterior e valide o mapeamento de sua assinatura e esse locatário do Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Onde posso encontrar informações sobre a versão anterior da solução de monitoramento remoto?

A versão anterior do acelerador de solução de monitoramento remoto era conhecida como a solução pré-configurada de monitoramento remoto IoT Suite. Você pode encontrar a documentação arquivada em [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>O novo Solution Accelerator está disponível na mesma região geográfica que a solução existente?

Sim, o novo monitoramento remoto está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em um Solution Accelerator no azureiotsolutions.com?

* Se você excluir o Solution Accelerator no [azureiotsolutions.com](https://www.azureiotsolutions.com/), você excluirá todos os recursos que foram implantados quando criou o Solution Accelerator. Se você adicionou recursos adicionais ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [portal do Azure](https://portal.azure.com), excluirá somente os recursos nesse grupo de recursos. Você também precisa excluir o aplicativo Azure Active Directory associado ao Solution Accelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar aproveitando meus investimentos existentes nos aceleradores de solução do Azure IoT?

Sim. Qualquer solução que exista hoje continuará a funcionar em sua assinatura do Azure e o código-fonte permanecerá disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por padrão, você pode provisionar [10 hubs IOT por assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, como cada Solution Accelerator provisiona um novo hub IoT, você só pode provisionar até 10 Solution Accelerators em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você só pode provisionar 50 instâncias de Cosmos dB por assinatura.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano de transações internas nível 1. Como resultado, você só pode provisionar até duas soluções de monitoramento remoto em uma assinatura sem modificações.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um Solution Accelerator se eu tiver Microsoft Azure para DreamSpark?

> [!NOTE]
> O Microsoft Azure para DreamSpark agora é conhecido como Microsoft Imagine para alunos.

No momento, não é possível criar um acelerador de solução com um [Microsoft Azure para](https://azure.microsoft.com/pricing/member-offers/imagine/) a conta DreamSpark. No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite criar um Solution Accelerator.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Como fazer excluir um locatário do Azure AD?

Consulte a postagem no blog [de Eric golpe, sobre como excluir um locatário do Azure ad](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades dos aceleradores de soluções do IoT:

* [Explore os recursos do acelerador de solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md)
* [Descrição geral do acelerador de soluções de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implantar o Solution Accelerator da fábrica conectada](quickstart-connected-factory-deploy.md)
* [Segurança de IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
