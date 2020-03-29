---
title: Utilize serviços de Plataforma como um Serviço (PaaS) em Laboratórios Azure DevTest
description: Saiba como utilizar os serviços Platform-as-a-Service (Pass) em Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169198"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Utilize serviços de Plataforma como um Serviço (PaaS) em Laboratórios Azure DevTest
O PaaS é suportado nos Laboratórios DevTest através da funcionalidade ambientes. Os ambientes em DevTest Labs são suportados por modelos pré-configurados do Gestor de Recursos Azure num repositório Git. Os ambientes podem conter tanto os recursos paaS como os iaaS. Permitem criar sistemas complexos que podem incluir recursos Azure, como máquinas virtuais, bases de dados, redes virtuais e aplicações Web, que são personalizadas para trabalhar em conjunto. Estes modelos permitem uma implementação consistente e uma melhor gestão dos ambientes utilizando o controlo de código fonte. 

Um sistema de configuração adequada permite os seguintes cenários: 

- Desenvolvedores para ter ambientes independentes e múltiplos
- Testar em diferentes configurações assincronicamente
- Integração em oleodutos de encenação e produção sem alterações de modelo
- Ter máquinas de desenvolvimento e ambientes dentro do mesmo laboratório melhora a facilidade de gestão e reporte de custos.  

O fornecedor de recursos da DevTest Labs cria recursos em nome do utilizador do laboratório, pelo que não é necessário dar permissões adicionais ao utilizador do laboratório para permitir a utilização dos recursos PaaS. A imagem seguinte mostra um cluster de Tecido de Serviço como um ambiente no laboratório.

![Cluster de tecido de serviço como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre a configuração de ambientes, consulte [Criar ambientes multi-VM e recursos PaaS com modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos Azure . A DevTest Labs tem um repositório público de modelos do Gestor de Recursos Azure que pode usar para criar ambientes sem ter que se conectar a uma fonte externa do GitHub por si mesmo. Para obter mais informações sobre ambientes públicos, consulte [Configure e utilize ambientes públicos em Laboratórios Azure DevTest](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipas de desenvolvimento normalmente fornecem ambientes como ambientes de teste personalizados/isolados. Pode haver ambientes que devem ser utilizados por todas as equipas dentro de uma unidade de negócio ou de uma divisão. O grupo de TI pode querer fornecer os seus ambientes que podem ser usados por todas as equipas da organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Sandbox 
O dono do laboratório pode personalizar ambientes de laboratório para alterar o papel do utilizador de **leitor** para **colaborador** dentro do grupo de recursos. Esta capacidade está na página **Definições** de Laboratório sob a **Configuração e Políticas** do laboratório. Esta alteração de função permite ao utilizador adicionar ou remover recursos dentro desse ambiente. Se quiser restringir ainda mais o acesso, utilize as políticas do Azure. Esta funcionalidade permite personalizar os recursos ou configuração sem o acesso ao nível de subscrição.

#### <a name="custom-tokens"></a>Fichas personalizadas
Há algumas informações personalizadas do laboratório que estão fora do grupo de recursos e são específicas para ambientes que o modelo pode aceder. Aqui estão alguns deles: 

- Identificação da rede de laboratório
- Localização
- Conta de armazenamento na qual os ficheiros de modelos do Gestor de Recursos são armazenados. 
 
#### <a name="lab-virtual-network"></a>Rede virtual de laboratório
Os [ambientes de ligação ao](connect-environment-lab-virtual-network.md) artigo de rede virtual do laboratório `$(LabSubnetId)` descrevem como modificar o seu modelo de Gestor de Recursos para usar o símbolo. Quando um ambiente é `$(LabSubnetId)` criado, o símbolo é substituído pela primeira marca de sub-rede onde a utilização na opção de **criação de máquinavirtual** é definida como **verdadeira**. Permite que o nosso ambiente utilize redes previamente criadas. Se pretender utilizar os mesmos modelos de Gestor de Recursos `$(LabSubnetId)` em ambientes em teste como encenação e produção, use como um valor padrão num parâmetro de modelo do Gestor de Recursos. 

#### <a name="environment-storage-account"></a>Conta de Armazenamento ambiental
A DevTest Labs suporta o uso de [modelos aninhados do Gestor](../azure-resource-manager/templates/linked-templates.md)de Recursos. O [ Implementar modelos do Gestor de[Recursos do Azure aninhado para testar ambientes](deploy-nested-template-environments.md) de ambientes de artigo explica como usar `_artifactsLocation` e `_artifactsLocationSasToken` tokens para criar um URI para um modelo de Gestor de Recursos na mesma pasta que ou numa pasta aninhada do modelo principal. Para mais informações sobre estas duas fichas, consulte a secção de **artefactos** de implantação do Gestor de Recursos Azure – Guia de [Boas Práticas.](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)

## <a name="user-experience"></a>Experiência do Utilizador

## <a name="developer"></a>Programador
Os desenvolvedores usam o mesmo fluxo de trabalho para criar um VM para criar um ambiente específico. Selecionam o ambiente vs. a imagem da máquina e introduzem as informações necessárias pelo modelo. Cada desenvolvedor com um ambiente permite a implementação de alterações e depuração de ciclo interno melhorado. O ambiente pode ser criado a qualquer momento usando o modelo mais recente.  Esta funcionalidade permite que os ambientes sejam destruídos e recriados para ajudar a reduzir o tempo de inatividade de ter de criar manualmente o sistema ou recuperar de testes de avaria.  

### <a name="testing"></a>Testar
Os ambientes da DevTest Labs permitem testar independentemente código sincronicamente e configurações. A prática comum é configurar o ambiente com o código a partir do pedido de puxar individualmente e iniciar qualquer teste automatizado. Uma vez concluído o teste automatizado, qualquer teste manual pode ser executado contra o ambiente específico. Normalmente, este processo é feito como parte do oleoduto CI/CD. 

## <a name="management-experience"></a>Experiência de Gestão

### <a name="cost-tracking"></a>Rastreio de Custos
A funcionalidade de rastreio de custos inclui os recursos do Azure dentro dos diferentes ambientes como parte da tendência global de custos. O custo por recursos não desfaz os diferentes recursos dentro do ambiente, mas mostra o ambiente como um custo único.

### <a name="security"></a>Segurança
Uma subscrição Azure devidamente configurada com a DevTest Labs pode [limitar o acesso aos recursos do Azure apenas através do laboratório.](devtest-lab-add-devtest-user.md) Com ambientes, um proprietário de laboratório pode permitir que os utilizadores acedam aos recursos paaS com configurações aprovadas sem permitir o acesso a quaisquer outros recursos Azure. No cenário em que os utilizadores de laboratório personalizam ambientes, o proprietário do laboratório pode permitir o acesso dos contribuintes. O acesso do contribuinte permite ao utilizador do laboratório adicionar ou remover o recurso Azure apenas dentro do grupo de recursos geridos. Permite um rastreio e gestão mais fáceis versus permitir ao colaborador utilizador o acesso à subscrição.

### <a name="automation"></a>Automatização
A automação é um componente fundamental para um ecossistema em larga escala e eficaz. A automatização é necessária para lidar com a gestão ou rastreio de múltiplos ambientes através de subscrições e laboratórios.

### <a name="cicd-pipeline"></a>CI/CD Pipeline
Os serviços paaS em DevTest Labs podem ajudar a melhorar o oleoduto CI/CD, tendo implantado si o foco onde o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- 
- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Criar um ambiente com cluster self-contained Service Fabric em Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Ligue um ambiente à rede virtual do seu laboratório em Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integre os ambientes nos seus oleodutos Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





