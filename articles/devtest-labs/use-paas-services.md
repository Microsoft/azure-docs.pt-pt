---
title: Utilizar serviços plataforma-as-a-service (PaaS) em Azure DevTest Labs
description: Saiba como utilizar os serviços Plataforma-as-a-Service (Pass) em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478744"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Utilizar serviços plataforma-as-a-service (PaaS) em Azure DevTest Labs
O PaaS é suportado nos Laboratórios DevTest através da funcionalidade ambiente. Ambientes em DevTest Labs são suportados por modelos pré-configurados do Gestor de Recursos Azure num repositório de Git. Os ambientes podem conter recursos paaS e IaaS. Permitem criar sistemas complexos que podem incluir recursos Azure, como máquinas virtuais, bases de dados, redes virtuais e aplicações Web, que são personalizadas para trabalhar em conjunto. Estes modelos permitem uma implementação consistente e uma melhor gestão dos ambientes utilizando o controlo do código fonte. 

Um sistema devidamente configurado permite os seguintes cenários: 

- Desenvolvedores para ter ambientes independentes e múltiplos
- Testes em diferentes configurações assíncroneas
- Integração em oleodutos de encenação e produção sem alterações de modelo
- Ter máquinas de desenvolvimento e ambientes dentro do mesmo laboratório melhora a facilidade de gestão e a comunicação de custos.  

O fornecedor de recursos DavTest Labs cria recursos em nome do utilizador do laboratório, pelo que não é necessário dar permissões adicionais ao utilizador do laboratório para permitir a utilização dos recursos do PaaS. A imagem a seguir mostra um cluster de Tecido de Serviço como um ambiente no laboratório.

![Cluster de tecido de serviço como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre a configuração de ambientes, consulte [Criar ambientes multi-VM e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md). A DevTest Labs tem um repositório público de modelos do Azure Resource Manager que pode usar para criar ambientes sem ter de se ligar a uma fonte externa do GitHub por si mesmo. Para obter mais informações sobre ambientes públicos, consulte [Configure e utilize ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipas de desenvolvimento normalmente fornecem ambientes como ambientes de teste personalizados/isolados. Pode haver ambientes que devem ser usados por todas as equipas dentro de uma unidade de negócio ou de uma divisão. O grupo de TI pode querer fornecer os seus ambientes que podem ser usados por todas as equipas da organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Sandbox 
O proprietário do laboratório pode personalizar ambientes de laboratório para alterar o papel do utilizador de **leitor** para **contribuinte** dentro do grupo de recursos. Esta capacidade está na página **de Definições** de Laboratório sob a **Configuração e Políticas** do laboratório. Esta alteração de função permite ao utilizador adicionar ou remover recursos dentro desse ambiente. Se quiser restringir ainda mais o acesso, utilize as políticas Azure. Esta funcionalidade permite personalizar os recursos ou configuração sem o acesso ao nível da subscrição.

#### <a name="custom-tokens"></a>Fichas personalizadas
Há algumas informações personalizadas do laboratório que estão fora do grupo de recursos e são específicas para ambientes que o modelo pode aceder. Aqui estão alguns deles: 

- Identificação da rede de laboratório
- Localização
- Conta de armazenamento na qual os ficheiros de modelos do Gestor de Recursos são armazenados. 
 
#### <a name="lab-virtual-network"></a>Rede virtual de laboratório
Os [ambientes de ligação ao](connect-environment-lab-virtual-network.md) artigo de rede virtual do laboratório descrevem como modificar o modelo do Gestor de Recursos para usar o `$(LabSubnetId)` token. Quando um ambiente é criado, o `$(LabSubnetId)` símbolo é substituído pela primeira marca de sub-rede onde a utilização na opção de **criação de máquinas virtuais** é definida como **verdadeira**. Permite que o nosso ambiente utilize redes previamente criadas. Se pretender utilizar os mesmos modelos de Gestor de Recursos em ambientes em teste como encenação e produção, utilize `$(LabSubnetId)` como valor padrão num parâmetro de modelo do Gestor de Recursos. 

#### <a name="environment-storage-account"></a>Conta de Armazenamento de Ambiente
A DevTest Labs suporta a utilização de [modelos de Gestores de Recursos aninhados.](../azure-resource-manager/templates/linked-templates.md) Os[modelos [ Implementar modelos de Gestor de Recursos Azure aninhados para testar ambientes](deploy-nested-template-environments.md) artigo explica como usar  `_artifactsLocation` e `_artifactsLocationSasToken` fichas para criar um URI para um modelo de Gestor de Recursos na mesma pasta que ou numa pasta aninhada do modelo principal. Para mais informações sobre estes dois tokens, consulte a secção de **artefactos** de implantação do [Azure Resource Manager – Guia de Boas Práticas.](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)

## <a name="user-experience"></a>Experiência do Utilizador

## <a name="developer"></a>Programador
Os desenvolvedores usam o mesmo fluxo de trabalho para criar um VM para criar um ambiente específico. Selecionam o ambiente vs. a imagem da máquina e introduzem as informações necessárias exigidas pelo modelo. Cada desenvolvedor com um ambiente permite a implementação de alterações e depuração melhorada do loop interno. O ambiente pode ser criado a qualquer momento usando o modelo mais recente.  Esta funcionalidade permite que os ambientes sejam destruídos e recriados para ajudar a reduzir o tempo de inatividade de ter de criar manualmente o sistema ou recuperar de testes de falhas.  

### <a name="testing"></a>Testar
Os ambientes de DevTest Labs permitem testes independentes de código e configurações específicas assíncroneas. A prática comum é configurar o ambiente com o código a partir do pedido de puxar individualmente e iniciar qualquer teste automatizado. Uma vez concluído o teste automatizado, qualquer teste manual pode ser executado contra o ambiente específico. Normalmente, este processo é feito como parte do gasoduto CI/CD. 

## <a name="management-experience"></a>Experiência de Gestão

### <a name="cost-tracking"></a>Rastreio de custos
A funcionalidade de rastreio de custos inclui os recursos da Azure dentro dos diferentes ambientes como parte da tendência global de custos. O custo por recursos não desfasa os diferentes recursos dentro do ambiente, mas mostra o ambiente como um único custo.

### <a name="security"></a>Segurança
Uma subscrição Azure devidamente configurada com a DevTest Labs só pode [limitar o acesso aos recursos do Azure através do laboratório.](devtest-lab-add-devtest-user.md) Com ambientes, um proprietário de laboratório pode permitir que os utilizadores acedam aos recursos paaS com configurações aprovadas sem permitir o acesso a quaisquer outros recursos Azure. No cenário em que os utilizadores de laboratório personalizam ambientes, o proprietário do laboratório pode permitir o acesso dos colaboradores. O acesso do contribuinte permite ao utilizador do laboratório adicionar ou remover o recurso Azure apenas dentro do grupo de recursos geridos. Permite um rastreio e gestão mais fácil versus permitir o acesso do colaborador do utilizador à subscrição.

### <a name="automation"></a>Automatização
A automatização é um componente fundamental para um ecossistema em grande escala e eficaz. A automatização é necessária para lidar com a gestão ou rastreio de vários ambientes através de subscrições e laboratórios.

### <a name="cicd-pipeline"></a>Gasoduto CI/CD
Os serviços paaS em DevTest Labs podem ajudar a melhorar o oleoduto CI/CD, tendo implementações focadas onde o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para mais detalhes sobre ambientes: 

- 
- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Criar um ambiente com cluster de tecido de serviço independente em Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Ligue um ambiente à rede virtual do seu laboratório em Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrar ambientes nos seus oleodutos Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





