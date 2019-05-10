---
title: Utilizar os serviços de plataforma-como-serviço (PaaS) no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como utilizar os serviços de plataforma-como-serviço (Pass) no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233116"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Utilizar os serviços de plataforma-como-serviço (PaaS) no Azure DevTest Labs
PaaS é suportada no DevTest Labs através da funcionalidade de ambientes. Ambientes no DevTest Labs são compatíveis com modelos pré-configurados do Azure Resource Manager num repositório Git. Ambientes podem conter recursos de PaaS e IaaS. Eles permitem criar sistemas complexos que podem incluir os recursos do Azure como máquinas virtuais, bases de dados, redes virtuais e aplicações Web, que são personalizadas para trabalhar em conjunto. Estes modelos permitem a implantação consistente e melhor gerenciamento de ambientes usando o controle de código fonte. 

A configurar corretamente o sistema permite que os seguintes cenários: 

- Desenvolvedores tenham independente e vários ambientes
- Teste em diferentes configurações de forma assíncrona
- Integração com pipelines de transição e produção sem qualquer alteração de modelo
- Ter máquinas de desenvolvimento e ambientes dentro do mesmo laboratório melhora a facilidade de gestão e relatórios de custo.  

O fornecedor de recursos do DevTest Labs cria recursos em nome do usuário de laboratório, pelo que não existem permissões Extras necessário para o utilizador de laboratório para permitir a utilização dos recursos PaaS. A imagem seguinte mostra um cluster do Service Fabric como um ambiente no laboratório.

![Cluster do Service Fabric como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre como configurar ambientes, veja [criar ambientes multi-VM e recursos PaaS com modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs tem um repositório público dos modelos do Azure Resource Manager que pode utilizar para criar ambientes sem ter de ligar a uma origem externa do GitHub sozinho. Para obter mais informações sobre ambientes públicos, consulte [configurar e utilizar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipes de desenvolvimento oferecem, normalmente, os ambientes, como ambientes de teste personalizada/isolada. Pode haver ambientes que estão a ser utilizada por todas as equipes dentro de uma unidade de negócios ou de uma divisão. O grupo de TI poderá fornecer seus ambientes que podem ser utilizados por todas as equipes na organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Sandbox 
O proprietário de laboratório pode personalizar a ambientes de laboratório para alterar a função do utilizador de **leitor** ao **contribuinte** dentro do grupo de recursos. Esta capacidade está no **definições de laboratório** página sob a **configuração e políticas de** do laboratório. Esta alteração na função permite ao utilizador adicionar ou remover recursos dentro desse ambiente. Se quiser restringir ainda mais o acesso, utilize as políticas do Azure. Esta funcionalidade permite-lhe personalizar a configuração sem o acesso ao nível da subscrição ou recursos.

#### <a name="custom-tokens"></a>Tokens personalizados
Há algumas informações de laboratório personalizado que estão fora do grupo de recursos e é específico para ambientes que o modelo pode aceder. Aqui estão alguns deles: 

- Identificação de rede de laboratório
- Location
- Conta de armazenamento na qual são armazenados os ficheiros de modelos do Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Rede virtual de laboratório
O [ligar a ambientes ao laboratório](connect-environment-lab-virtual-network.md) artigo descreve como modificar o modelo do Resource Manager para utilizar o `$(LabSubnetId)` token. Quando é criado um ambiente, o `$(LabSubnetId)` token é substituído pela primeira marca de sub-rede em que o **utilização numa máquina virtual crie** opção estiver definida como **verdadeiro**. Ele permite que nosso ambiente utilizar redes que criou anteriormente. Se pretender utilizar os mesmos modelos do Gestor de recursos em ambientes de teste como teste e produção, utilize `$(LabSubnetId)` como um valor padrão num parâmetro de modelo do Resource Manager. 

#### <a name="environment-storage-account"></a>Conta de armazenamento de ambiente
DevTest Labs suporta a utilização de [aninhada de modelos do Resource Manager](../azure-resource-manager/resource-group-linked-templates.md). O [como o Azure DevTest Labs facilita o Gestor de recursos aninhados implementações de modelo em ambientes de teste](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments) artigo explica como usar `_artifactsLocation` e `_artifactsLocationSasToken` tokens para criar um URI para um modelo do Resource Manager no a mesma pasta como ou numa pasta aninhada do modelo principal. Para obter mais informações sobre estes dois tokens, consulte a **artefatos de implementação** secção [Gestor de recursos do Azure – guia de práticas recomendadas](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Experiência de utilizador

## <a name="developer"></a>Programador
Os desenvolvedores usar o mesmo fluxo de trabalho para criar uma VM para criar um ambiente específico. Eles selecione o ambiente versus a imagem de máquina e introduza as informações necessárias exigidas pelo modelo. Permite que cada desenvolvedor a ter um ambiente para a implementação de alterações e a depuração de loop interno melhorado. O ambiente pode ser criado em qualquer altura utilizando o modelo mais recente.  Esta funcionalidade permite que os ambientes ser destruída e recriada para ajudar a reduzir o tempo de inatividade da necessidade de criar o sistema ou recuperar de falhas de teste manualmente.  

### <a name="testing"></a>A testar
Ambientes de laboratórios DevTest permitem independentes teste de código específico e configurações de forma assíncrona. A prática comum é configurar o ambiente com o código no pedido pull individuais e começar a qualquer teste automatizado. Assim que o teste automatizado tiver executado para conclusão, o teste manual pode ser executado contra o ambiente específico. Normalmente, este processo é feito como parte do pipeline CI/CD. 

## <a name="management-experience"></a>Experiência de gestão

### <a name="cost-tracking"></a>Controlo de custos
A funcionalidade de controlo de custos inclui recursos do Azure em diferentes ambientes, como parte do geral tendência de custo. O custo por recursos não quebra os recursos diferentes dentro do ambiente, mas apresenta o ambiente como um custo único.

### <a name="security"></a>Segurança
Pode de uma subscrição do Azure configurada corretamente com o DevTest Labs [limitar o acesso aos recursos do Azure apenas por meio de laboratório](devtest-lab-add-devtest-user.md). Com ambientes, o proprietário de um laboratório pode permitir aos utilizadores aceder aos recursos de PaaS com configurações aprovadas sem conceder acesso a outros recursos do Azure. No cenário em que os utilizadores de laboratório personalizar ambientes, o proprietário de laboratório pode permitir o acesso de contribuinte. O acesso de contribuinte permite ao utilizador de laboratório adicionar ou remover recursos do Azure apenas dentro do grupo de recursos gerido. Ele permite que mais fácil de controlo e gestão versus permitir o acesso de contribuinte de utilizador à subscrição.

### <a name="automation"></a>Automatização
A automação é um componente-chave para uma grande escala, o ecossistema em vigor. A automação é necessária para processar a gestão ou vários ambientes de controle entre subscrições e laboratórios.

### <a name="cicd-pipeline"></a>Pipeline CI/CD
Serviços de PaaS no DevTest Labs podem ajudar a melhorar o pipeline de CI/CD ao ter voltada para implementações em que o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para obter detalhes sobre os ambientes: 

- 
- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e utilizar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Criar um ambiente com cluster autónomo do Service Fabric no Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Ligar um ambiente a rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrar ambientes os pipelines CI/CD do Azure DevOps](integrate-environments-devops-pipeline.md)
 





