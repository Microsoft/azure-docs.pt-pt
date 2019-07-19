---
title: Perguntas frequentes sobre Azure DevTest Labs | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: spelluru
ms.openlocfilehash: 9f38502cc543f19855dbca32c4724a5651b1a3f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318111"
---
# <a name="azure-devtest-labs-faq"></a>FAQ de Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre Azure DevTest Labs.

## <a name="blog-post"></a>Postagem no blog
Nosso blog da equipe do DevTest Labs foi desativado em 20 de março de 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Onde posso rastrear as atualizações de recurso de agora em diante?
De agora em diante, lançaremos atualizações de recursos e postagens de blog informativas no blog do Azure e nas atualizações do Azure. Essas Postagens de blog também serão vinculadas à nossa documentação sempre que necessário.

Assine o [blog do Azure do DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) e [as atualizações do DevTest Labs do Azure](https://azure.microsoft.com/updates/?product=devtest-lab) para se manter informado sobre os novos recursos no DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>O que acontece com as postagens de blog existentes?
No momento, estamos trabalhando para migrar Postagens de blog existentes (excluindo atualizações de interrupção) para nossa [documentação do DevTest Labs](devtest-lab-overview.md). Quando o blog do MSDN for preterido, ele será redirecionado para a visão geral da documentação do DevTest Labs. Depois de Redirecionado, você pode pesquisar o artigo que está procurando no título ' Filtrar por '. Ainda não migramos todas as postagens, mas elas devem ser feitas pelo final deste mês. 


### <a name="where-do-i-see-outage-updates"></a>Onde posso ver as atualizações de interrupção?
Lançaremos atualizações de interrupção usando nosso identificador do Twitter a partir de agora em diante. Siga-nos no Twitter para obter atualizações mais recentes sobre interrupções e bugs conhecidos.

### <a name="twitter"></a>Twitter
Nosso identificador do Twitter:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não for respondida aqui?
Se sua pergunta não estiver listada aqui, informe-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final das perguntas frequentes. Envolva-se com a equipe de cache do Azure e outros membros da Comunidade sobre este artigo.
- Para alcançar um público mais amplo, poste uma pergunta no [fórum Azure DEVTEST Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Envolva-se com a equipe de Azure DevTest Labs e outros membros da Comunidade.
- Para solicitações de recursos, envie suas solicitações e ideias para [Azure DevTest Labs a voz do usuário](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>O que é um conta Microsoft?
Uma conta Microsoft é uma conta que você usa para quase tudo o que faz com os dispositivos e serviços da Microsoft. É um endereço de email e senha que você usa para entrar no Skype, Outlook.com, OneDrive, Windows Phone, Azure e Xbox Live. Uma única conta significa que seus arquivos, fotos, contatos e configurações podem seguir você em qualquer dispositivo.
 
> [!NOTE]
> Um conta Microsoft usado para ser chamado de Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar Azure DevTest Labs?
Azure DevTest Labs pode economizar tempo e dinheiro da equipe. Os desenvolvedores podem criar seus próprios ambientes usando várias bases diferentes. Eles também podem usar artefatos para implantar e configurar aplicativos rapidamente. Usando imagens e fórmulas personalizadas, você pode salvar máquinas virtuais (VMs) como modelos e reproduzi-las facilmente em toda a equipe. O DevTest Labs também oferece várias políticas configuráveis que os administradores de laboratório podem usar para reduzir o desperdício e gerenciar os ambientes de uma equipe. Essas políticas incluem desligamento automático, limite de custo, máximo de VMs por usuário e tamanho máximo da VM. Para obter uma explicação mais detalhada sobre o DevTest Labs, consulte a [visão geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "autoatendimento sem preocupações"?
O autoatendimento sem preocupações significa que os desenvolvedores e testadores criam seus próprios ambientes, conforme necessário. Os administradores têm a segurança de saber que o DevTest Labs pode ajudar a definir o acesso apropriado, minimizar os custos de desperdício e controle. Os administradores podem especificar quais tamanhos de VM são permitidos, o número máximo de VMs e quando as VMs são iniciadas e desligadas. O DevTest Labs também facilita o monitoramento de custos e a definição de alertas, para ajudá-lo a se manter informado sobre como os recursos de laboratório estão sendo usados.

### <a name="how-can-i-use-devtest-labs"></a>Como posso usar o DevTest Labs?
O DevTest Labs é útil sempre que você precisa de ambientes de desenvolvimento ou teste e deseja reproduzi-los rapidamente ou gerenciá-los usando políticas de economia de custos.
Aqui estão alguns cenários que nossos clientes usam DevTest Labs para:

- Gerencie ambientes de desenvolvimento e teste em um único lugar. Use políticas para reduzir custos e criar imagens personalizadas para compartilhar compilações na equipe.
- Desenvolva um aplicativo usando imagens personalizadas para salvar o estado do disco em todos os estágios de desenvolvimento.
- Acompanhe o custo em relação ao progresso.
- Crie ambientes de teste em massa para testes de garantia de qualidade.
- Use artefatos e fórmulas para configurar e reproduzir facilmente um aplicativo em vários ambientes.
- Distribua VMs para hackathons (desenvolvimento colaborativo ou trabalho de teste) e, em seguida, Desprovisione-as facilmente quando o evento terminar.

### <a name="how-am-i-billed-for-devtest-labs"></a>Como sou cobrado pelo DevTest Labs?
O DevTest Labs é um serviço gratuito. Criar laboratórios e configurar políticas, modelos e artefatos no DevTest Labs é gratuito. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo dos recursos de laboratório, consulte [preços de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Segurança

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os diferentes níveis de segurança no DevTest Labs?
O acesso de segurança é determinado pelo RBAC (controle de acesso baseado em função). Para saber como funciona o Access, ele ajuda a aprender as diferenças entre uma permissão, uma função e um escopo, conforme definido pelo RBAC.

- **Permissão**: Uma permissão é um acesso definido a uma ação específica. Por exemplo, uma permissão pode ser acesso de leitura a todas as VMs.
- **Função**: Uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, um usuário com uma função de proprietário de assinatura tem acesso a todos os recursos em uma assinatura.
- **Escopo**: Um escopo é um nível dentro da hierarquia de um recurso do Azure. Por exemplo, um escopo pode ser um grupo de recursos, um único laboratório ou toda a assinatura.

Dentro do escopo do DevTest Labs, há dois tipos de funções que definem permissões de usuário:

- **Proprietário do laboratório**: Um proprietário de laboratório tem acesso a todos os recursos do laboratório. Um proprietário de laboratório pode modificar políticas, ler e gravar em qualquer VM, alterar a rede virtual e assim por diante.
- **Usuário do laboratório**: Um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais. No entanto, um usuário de laboratório não pode modificar políticas ou VMs que foram criadas por outros usuários.

Você também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, consulte [conceder permissões de usuário para políticas de laboratório específicas](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, o usuário recebe automaticamente essas permissões em cada escopo de nível inferior no escopo. Por exemplo, se um usuário receber a função de proprietário da assinatura, o usuário terá acesso a todos os recursos em uma assinatura. Esses recursos incluem VMs, redes virtuais e laboratórios. Um proprietário de assinatura herda automaticamente a função de proprietário do laboratório. No entanto, o oposto não é verdadeiro. Um proprietário de laboratório tem acesso a um laboratório, que é um escopo inferior ao nível de assinatura. Portanto, um proprietário de laboratório não pode ver VMs, redes virtuais ou quaisquer outros recursos que estejam fora do laboratório.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Como fazer definir o controle de acesso baseado em função para meus ambientes do DevTest Labs para garantir que ele possa ser controlado, enquanto os desenvolvedores/testes podem fazer seu trabalho?
Há um padrão amplo, no entanto, os detalhes dependem de sua organização.

A ti central deve ter apenas o que é necessário e permitir que as equipes de projeto e de aplicativo tenham o nível de controle necessário. Normalmente, isso significa que a ti central possui a assinatura e manipula funções de ti essenciais, como configurações de rede. O conjunto de **proprietários** de uma assinatura deve ser pequeno. Esses proprietários podem indicar proprietários adicionais quando há uma necessidade ou aplicar políticas de nível de assinatura, por exemplo, "nenhum IP público".

Pode haver um subconjunto de usuários que exigem acesso em uma assinatura, como o suporte a nível 1 ou nível 2. Nesse caso, é recomendável conceder a esses usuários o acesso de **colaborador** para que eles possam gerenciar os recursos, mas não fornecer acesso ao usuário ou ajustar políticas.

O recurso DevTest Labs deve ser de propriedade dos proprietários que estão próximos da equipe de projeto/aplicativo. É porque eles entendem seus requisitos para computadores e o software necessário. Na maioria das organizações, o proprietário desse recurso do DevTest Labs normalmente é o líder de projeto/desenvolvimento. Esse proprietário pode gerenciar usuários e políticas no ambiente de laboratório e pode gerenciar todas as VMs no ambiente do DevTest Labs.

Os membros da equipe do projeto/aplicativo devem ser adicionados à função **usuários do DevTest Labs** . Esses usuários podem criar máquinas virtuais (em linha com o laboratório e as políticas de nível de assinatura). Eles também podem gerenciar suas próprias máquinas virtuais. Eles não podem gerenciar máquinas virtuais que pertencem a outros usuários.

Para obter mais informações, consulte [Azure Enterprise Scaffold – documentação de governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Como fazer criar uma função para permitir que os usuários façam uma tarefa específica?
Para obter um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a uma função, consulte [conceder permissões de usuário para políticas de laboratório específicas](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Aqui está um exemplo de um script que cria a função **usuário avançado do DevTest Labs**, que tem permissão para iniciar e parar todas as VMs no laboratório:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Como uma organização pode garantir que as políticas de segurança corporativas estejam em vigor?
Uma organização pode conseguir isso fazendo as seguintes ações:

- Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associado ao software usando os ativos de nuvem. Ele também define o que claramente viola a política.
- Desenvolva uma imagem personalizada, artefatos personalizados e um processo de implantação que permita a orquestração dentro do realm de segurança definido com o Active Directory. Essa abordagem impõe o limite corporativo e define um conjunto comum de controles ambientais. Esses controles no ambiente que um desenvolvedor pode considerar ao desenvolver e seguir um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo também é fornecer um ambiente que não seja excessivamente restritivo que possa atrapalhar o desenvolvimento, mas um conjunto razoável de controles. As diretivas de grupo na UO (unidade organizacional) que contém máquinas virtuais de laboratório podem ser um subconjunto do total de políticas de grupo encontradas na produção. Como alternativa, eles podem ser um conjunto adicional para atenuar corretamente quaisquer riscos identificados.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Como uma organização pode garantir a integridade dos dados para garantir que os desenvolvedores de comunicação remota não possam remover código ou introduzir malware ou software não aprovado?
Há várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de contrato ou funcionários que são de comunicação remota para colaborar no DevTest Labs.

Conforme mencionado anteriormente, a primeira etapa deve ter uma política de uso aceitável, rascunho e definida que descreve claramente as consequências quando alguém viola a política.

A primeira camada de controles para acesso remoto é aplicar uma política de acesso remoto por meio de uma conexão VPN que não está conectada diretamente ao laboratório.

A segunda camada de controles é aplicar um conjunto de objetos de diretiva de grupo que impeçam a cópia e a colagem da área de trabalho remota. Uma política de rede pode ser implementada para não permitir serviços de saída do ambiente, como serviços FTP e RDP do ambiente. O roteamento definido pelo usuário pode forçar todo o tráfego de rede do Azure de volta para o local, mas o roteamento não pôde considerar todas as URLs que possam permitir o carregamento de dados, a menos que sejam controlados por meio de um proxy para verificar o conteúdo e as sessões. Os IPs públicos podem ser restritos na rede virtual que dá suporte ao DevTest Labs para não permitir a ponte de um recurso de rede externa.

Por fim, o mesmo tipo de restrições precisa ser aplicado em toda a organização, o que consideraria todos os possíveis métodos de mídia removível ou URLs externas que poderiam aceitar uma postagem de conteúdo. Consulte seu profissional de segurança para examinar e implementar uma política de segurança. Para obter mais recomendações, consulte [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configuração do laboratório

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como fazer criar um laboratório de um modelo do Resource Manager?
Oferecemos um [repositório GitHub de modelos de Azure Resource Manager de laboratório](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) que você pode implantar como estão ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem um link para implantar o laboratório como ele está em sua própria assinatura do Azure. Ou, você pode personalizar o modelo e [implantá-lo usando o PowerShell ou o CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Posso ter todas as máquinas virtuais a serem criadas em um grupo de recursos comum, em vez de ter cada computador em seu próprio grupo de recursos? 
Sim, como proprietário de um laboratório, você pode deixar que o laboratório manipule a alocação do grupo de recursos para você ou tenha todas as máquinas virtuais criadas em um grupo de recursos comum que você especificar. 

Cenário de grupo de recursos separado:
-   O DevTest Labs cria um novo grupo de recursos para cada máquina virtual IP pública/privada que você gira
-   O DevTest Labs cria um grupo de recursos para máquinas IP compartilhadas que pertencem ao mesmo tamanho.

Cenário de grupo de recursos comuns:
-   Todas as máquinas virtuais são giradas no grupo de recursos comum que você especificar. Saiba mais sobre [a alocação de grupo de recursos para o laboratório](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Como fazer manter uma Convenção de nomenclatura em meu ambiente do DevTest Labs?
Talvez você queira estender as atuais convenções de nomenclatura corporativa para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs. Ao implantar o DevTest Labs, recomendamos que você tenha políticas de início específicas. Você implanta essas políticas por um script central e modelos JSON para impor a consistência. As políticas de nomenclatura podem ser implementadas por meio de políticas do Azure aplicadas no nível da assinatura. Para obter exemplos de JSON para Azure Policy, consulte [Azure Policy exemplos](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios posso criar na mesma assinatura?
Não há um limite específico para o número de laboratórios que podem ser criados por assinatura. No entanto, a quantidade de recursos usados por assinatura é limitada. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-subscription-service-limits.md) e [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Quantas VMs posso criar por laboratório?
Não há nenhum limite específico para o número de VMs que podem ser criadas por laboratório. No entanto, os recursos (núcleos de VM, endereços IP públicos e assim por diante) que são usados são limitados por assinatura. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-subscription-service-limits.md) e [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Como fazer determinar a taxa de usuários por laboratório e o número total de laboratórios necessários em uma organização?
Recomendamos que as unidades de negócios e os grupos de desenvolvimento associados ao mesmo projeto de desenvolvimento estejam associados ao mesmo laboratório. Ele permite que os mesmos tipos de políticas, imagens e políticas de desligamento sejam aplicados a ambos os grupos.

Talvez você também precise considerar os limites geográficos. Por exemplo, os desenvolvedores no leste do Norte Estados Unidos (EUA) podem usar um laboratório provisionado no leste dos EUA 2. Além de desenvolvedores em Dallas, Texas e Denver, a Colorado pode ser direcionada para usar um recurso no centro-sul dos EUA. Se houver um esforço colaborativo com terceiros externos, eles poderão ser atribuídos a um laboratório que não é usado por desenvolvedores internos.

Você também pode usar um laboratório para um projeto específico dentro de Azure DevOps Projects. Em seguida, aplique a segurança por meio de um grupo de Azure Active Directory especificado, que permite o acesso a ambos os conjuntos de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar usuários.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Como podemos impedir a exclusão de recursos em um laboratório?
Recomendamos que você defina as permissões adequadas no nível de laboratório para que somente usuários autorizados possam excluir recursos ou alterar políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **usuários do DevTest Labs** . O desenvolvedor líder ou o líder da infra-estrutura deve ser o **proprietário do DevTest Labs**. Recomendamos que você tenha apenas dois proprietários de laboratório. Essa política se estende até o repositório de códigos para evitar corrupção. Os usuários do laboratório têm direitos para usar recursos, mas não podem atualizar as políticas de laboratório. Consulte o seguinte artigo que lista as funções e os direitos que cada grupo interno tem dentro de um laboratório: [Adicionar proprietários e usuários no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como fazer compartilhar um link direto para o meu laboratório?

1. No [portal do Azure](https://portal.azure.com), vá para o laboratório.
2. Copie a **URL do laboratório** do seu navegador e compartilhe-a com os usuários do laboratório.

> [!NOTE]
> Se um usuário de laboratório for um usuário externo que tem um conta Microsoft, mas que não é membro da instância Active Directory da sua organização, o usuário poderá ver uma mensagem de erro ao tentar acessar o link compartilhado. Se um usuário externo vir uma mensagem de erro, peça ao usuário para selecionar primeiro seu nome no canto superior direito do portal do Azure. Em seguida, na seção diretório do menu, o usuário pode selecionar o diretório onde o laboratório existe.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Por que não consigo ver as VMs na página de máquinas virtuais que vejo no DevTest Labs?
Ao criar uma VM no DevTest Labs, você terá permissão para acessar essa VM. Você pode exibir a VM na página laboratórios e na página **máquinas virtuais** . Os usuários atribuídos à função de **proprietário do DevTest Labs** podem ver todas as VMs que foram criadas no laboratório na página **todas as máquinas virtuais** do laboratório. No entanto, os usuários que têm a função de **usuário do DevTest Labs** não recebem automaticamente o acesso de leitura aos recursos da VM que outros usuários criaram. Portanto, essas VMs não são exibidas na página **máquinas virtuais** .


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como fazer criar várias VMs do mesmo modelo de uma só vez?
Você tem duas opções para criar simultaneamente várias VMs do mesmo modelo:

- Você pode usar a [extensão de tarefas do Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Você pode [gerar um modelo do Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) enquanto estiver criando uma VM e [implantar o modelo do Resource Manager do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Você também pode especificar mais de uma instância de um computador a ser criada durante a criação da máquina virtual. Para saber mais sobre como criar várias instâncias de máquinas virtuais, confira o documento sobre como [criar uma máquina virtual de laboratório](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como fazer mover minhas VMs do Azure existentes para meu laboratório do DevTest Labs?
Para copiar suas VMs existentes para o DevTest Labs:

1.  Copie o arquivo VHD da VM existente usando um script do [Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Crie a [imagem personalizada](devtest-lab-create-template.md) dentro de seu laboratório do DevTest Labs.
3.  Crie uma VM no laboratório a partir de sua imagem personalizada.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Posso anexar vários discos a minhas VMs?

Sim, você pode anexar vários discos a suas VMs.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se eu quiser usar uma imagem do sistema operacional Windows para o meu teste, preciso comprar uma assinatura do MSDN?
Para usar as imagens do sistema operacional Windows Client (Windows 7 ou uma versão posterior) para seu desenvolvimento ou teste no Azure, execute uma das seguintes etapas:

- [Compre uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se você tiver um Enterprise Agreement, crie uma assinatura do Azure com a [oferta de desenvolvimento/teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos do Azure para cada oferta do MSDN, consulte [crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como fazer automatizar o processo de exclusão de todas as VMs em meu laboratório?
Como proprietário de um laboratório, você pode excluir VMs do seu laboratório no portal do Azure. Você também pode excluir todas as VMs em seu laboratório usando um script do PowerShell. No exemplo a seguir, no comentário **valores a serem alterados** , modifique os valores de parâmetro. Você pode recuperar os valores SubscriptionId, labResourceGroup e labName do painel de laboratório no portal do Azure.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Ambientes 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Como posso usar modelos do Resource Manager em meu ambiente do DevTest Labs?
Você implanta seus modelos do Resource Manager em um ambiente do DevTest Labs usando as etapas mencionadas no artigo [ambientes do DevTest Labs](devtest-lab-test-env.md) . Basicamente, você verifica os modelos do Resource Manager em um repositório git (Azure Repos ou GitHub) e adiciona um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório. Esse cenário pode não ser útil se você estiver usando o DevTest Labs para hospedar máquinas de desenvolvimento, mas pode ser útil se você estiver criando um ambiente de preparo, que é representativo da produção.

Também vale a pena observar que o número de máquinas virtuais por laboratório ou opção por usuário limita apenas o número de máquinas criadas nativamente no laboratório em si e não por nenhum ambiente (modelos do Resource Manager).

## <a name="custom-images"></a>Imagens Personalizadas

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Como posso configurar um processo facilmente reproduzível para trazer minhas imagens organizacionais personalizadas em um ambiente do DevTest Labs?
Consulte este [vídeo no padrão de fábrica de imagens](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Esse cenário é um cenário avançado, e os scripts fornecidos são apenas scripts de exemplo. Se forem necessárias alterações, você precisará gerenciar e manter os scripts usados em seu ambiente.

Para obter informações detalhadas sobre como criar uma fábrica de imagens, consulte [criar uma fábrica de imagens Personalizada no Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é uma imagem gerenciada. Uma fórmula é uma imagem que você pode definir com configurações adicionais e, em seguida, salvar e reproduzir. Uma imagem personalizada pode ser preferível se você quiser criar rapidamente vários ambientes usando a mesma imagem básica e imutável. Uma fórmula poderá ser melhor se você quiser reproduzir a configuração da VM com os bits mais recentes, como parte de uma rede virtual ou sub-rede, ou como uma VM de um tamanho específico. Para obter uma explicação mais detalhada, consulte comparando [imagens e fórmulas personalizadas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando devo usar uma fórmula versus uma imagem personalizada?
Normalmente, o fator decisivo nesse cenário é o custo e a reutilização. Se você tiver um cenário em que muitos usuários/laboratórios exigem uma imagem com muitos softwares sobre a imagem base, você pode reduzir os custos criando uma imagem personalizada. Isso significa que a imagem é criada uma vez. Ele reduz o tempo de configuração da máquina virtual e o custo incorrido devido à máquina virtual em execução quando a instalação ocorre.

No entanto, um fator adicional a ser observado é a frequência das alterações no pacote de software. Se você executar compilações diárias e exigir que o software esteja nas máquinas virtuais de seus usuários, considere usar uma fórmula em vez de uma imagem personalizada.

Para obter uma explicação mais detalhada, consulte comparando [imagens e fórmulas personalizadas](devtest-lab-comparing-vm-base-image-types.md) no DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como fazer automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?

Para automatizar o carregamento de arquivos VHD para criar imagens personalizadas, você tem duas opções:

- Use [AzCopy](../storage/common/storage-use-azcopy-v10.md) para copiar ou carregar arquivos VHD na conta de armazenamento associada ao laboratório.
- Use [Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Gerenciador de Armazenamento é um aplicativo autônomo que é executado no Windows, no OS X e no Linux.

Para localizar a conta de armazenamento de destino associada ao seu laboratório:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu à esquerda, selecione **grupos de recursos**.
3.  Localize e selecione o grupo de recursos associado ao seu laboratório.
4.  Em **visão geral**, selecione uma das contas de armazenamento.
5.  Selecione **Blobs**.
6.  Procure por carregamentos na lista. Se não houver nenhum, retorne para a etapa 4 e tente outra conta de armazenamento.
7.  Use a **URL** como o destino no comando AzCopy.

Quando devo usar uma imagem do Azure Marketplace versus minha própria imagem organizacional personalizada?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quando devo usar uma imagem do Azure Marketplace versus minha própria imagem organizacional personalizada?
O Azure Marketplace deve ser usado por padrão, a menos que você tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração de software complexo que exige que um aplicativo seja incluído como parte da imagem base.
- A instalação e a configuração de um aplicativo podem levar muitas horas, o que não é um uso eficiente do tempo de computação a ser adicionado em uma imagem do Azure Marketplace.
- Os desenvolvedores e testadores precisam acessar uma máquina virtual rapidamente e desejam minimizar o tempo de configuração de uma nova máquina virtual.
- Condições de conformidade ou regulatórias (por exemplo, políticas de segurança) que devem estar em vigor para todos os computadores.
- O uso de imagens personalizadas não deve ser considerado levemente. Elas apresentam complexidade extra, já que você deve gerenciar arquivos VHD para essas imagens base subjacentes. Você também precisa corrigir rotineiramente essas imagens base com atualizações de software. Essas atualizações incluem novas atualizações de sistema operacional (SO) e todas as atualizações ou alterações de configuração necessárias para o próprio pacote de software.

## <a name="artifacts"></a>Artefactos

### <a name="what-are-artifacts"></a>O que são artefatos?
Os artefatos são elementos personalizáveis que você pode usar para implantar seus bits mais recentes ou implantar suas ferramentas de desenvolvimento em uma VM. Anexe artefatos à sua VM ao criar a VM. Depois que a VM é provisionada, os artefatos implantam e configuram sua VM. Vários artefatos pré-existentes estão disponíveis em nosso [repositório GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Você também pode [criar seus próprios artefatos](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefato falhou durante a criação da VM. Como fazer solucionar problemas?
Para saber como obter logs para seu artefato com falha, consulte [como diagnosticar falhas de artefato no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando uma organização deve usar um repositório de artefato público versus um repositório de artefato privado no DevTest Labs?
O [repositório público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) de artefatos fornece um conjunto inicial de pacotes de software que são usados com mais frequência. Isso ajuda na implantação rápida sem a necessidade de investir tempo para reproduzir ferramentas de desenvolvimento e suplementos comuns. Você pode optar por implantar seu próprio repositório privado. Você pode usar um repositório público e um privado em paralelo. Você também pode optar por desabilitar o repositório público. Os critérios para implantar um repositório privado devem ser orientados pelas perguntas e considerações a seguir:

- A organização tem a necessidade de ter software licenciado corporativo como parte de sua oferta do DevTest Labs? Se a resposta for sim, um repositório privado deverá ser criado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é necessária como parte do processo geral de provisionamento? Se a resposta for sim, um repositório privado deverá ser implantado.
- Se a política de governança da organização exigir isolamento e os repositórios externos não estiverem sob o gerenciamento de configuração direta pela organização, um repositório de artefatos privado deverá ser implantado. Como parte desse processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desabilitado para que ninguém na organização possa acessá-lo mais. Essa abordagem força todos os usuários na organização a terem apenas um único repositório que é aprovado pela organização e minimizam a descompasso de configuração.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Uma organização deve planejar um único repositório ou permitir vários repositórios?
Como parte da estratégia geral de gerenciamento de configuração e governança da sua organização, recomendamos que você use um repositório centralizado. Quando você usa vários repositórios, eles podem se tornar silos de software não gerenciado no decorrer do tempo. Com um repositório central, várias equipes podem consumir artefatos desse repositório para seus projetos. Ela impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte da centralização, as seguintes ações são práticas recomendadas para gerenciamento e sustentabilidade de longo prazo:

- Associe o Azure Repos com o mesmo locatário Azure Active Directory que a assinatura do Azure está usando para autenticação e autorização.
- Crie um grupo chamado `All DevTest Labs Developers` em Azure Active Directory que seja gerenciado centralmente. Qualquer desenvolvedor que contribui para o desenvolvimento de artefatos deve ser colocado nesse grupo.
- O mesmo grupo de Azure Active Directory pode ser usado para fornecer acesso ao repositório de Azure Repos e ao laboratório.
- Em Azure Repos, a ramificação ou a bifurcação devem ser usadas para separar um repositório no desenvolvimento do repositório de produção primário. O conteúdo é adicionado somente ao Branch mestre com uma solicitação pull após uma revisão de código adequada. Depois que o revisor de código aprovar a alteração, um desenvolvedor líder, responsável pela manutenção do Branch mestre, mesclará o código atualizado.

## <a name="cicd-integration"></a>Integração de CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>O DevTest Labs se integra ao meu CI/CD ferramentas?
Se você estiver usando o Azure DevOps, poderá usar uma [extensão de tarefas do DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar seu pipeline de lançamento no DevTest Labs. Algumas das tarefas que você pode fazer com essa extensão incluem:

- Crie e implante uma VM automaticamente. Você também pode configurar a VM com a compilação mais recente usando as tarefas de cópia de arquivo do Azure ou Azure DevOps Services do PowerShell.
- Capture automaticamente o estado de uma VM após o teste para reproduzir um bug na mesma VM para uma investigação mais aprofundada.
- Exclua a VM no final do pipeline de lançamento, quando ela não for mais necessária.

As postagens de blog a seguir oferecem orientações e informações sobre como usar a extensão Azure DevOps Services:

- [DevTest Labs e a extensão DevOps do Azure](integrate-environments-devops-pipeline.md)
- [Implantar uma nova VM em um laboratório existente do DevTest Labs do Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Usando o gerenciamento de versão Azure DevOps Services para implantações contínuas no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outros cadeias de CI (integração contínua)/Continuous, você pode obter os mesmos cenários implantando [modelos de Azure Resource Manager](https://azure.microsoft.com/resources/templates/) usando [cmdlets Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [SDKs .net](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para o DevTest Labs](https://aka.ms/dtlrestapis) para integrar com seu ferramentas.

## <a name="networking"></a>Redes

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando devo criar uma nova rede virtual para meu ambiente do DevTest Labs versus usar uma rede virtual existente?
Se suas VMs precisarem interagir com a infraestrutura existente, considere usar uma rede virtual existente dentro do ambiente do DevTest Labs. Se você usar o ExpressRoute, talvez queira minimizar a quantidade de VNets/sub-redes para não fragmentar o espaço de endereço IP que é atribuído para uso nas assinaturas. 

Considere usar o padrão de emparelhamento VNet aqui ([modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) também. Essa abordagem permite a comunicação de vnet/sub-rede entre assinaturas. Caso contrário, cada ambiente do DevTest Labs poderia ter sua própria rede virtual. 

Há [limites](../azure-subscription-service-limits.md) no número de redes virtuais por assinatura. O valor padrão é 50, embora esse limite possa ser gerado para 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando devo usar um IP compartilhado vs. IP público vs. IP privado?
 
Se você usar uma VPN site a site ou uma rota expressa, considere usar IPs privados para que seus computadores possam ser acessados por meio de sua rede interna e inacessíveis via Internet pública.

> [!NOTE]
> Os proprietários de laboratório podem alterar essa política de sub-rede para garantir que ninguém crie acidentalmente endereços IP públicos para suas VMs. O proprietário da assinatura deve criar uma política de assinatura impedindo que IPs públicos sejam criados.

Ao usar IPs públicos compartilhados, as máquinas virtuais em um laboratório compartilham um endereço IP público. Essa abordagem pode ser útil quando você precisa evitar a violação dos limites em endereços IP públicos para uma determinada assinatura.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Como fazer garantir que as máquinas virtuais de desenvolvimento e teste não sejam capazes de alcançar a Internet pública? Há padrões recomendados para definir a configuração de rede?

Sim. Há dois aspectos a serem considerados – tráfego de entrada e de saída.

- **Tráfego de entrada** – se a máquina virtual não tiver um endereço IP público, ela não poderá ser acessada pela Internet. Uma abordagem comum é garantir que uma política de nível de assinatura esteja definida, de modo que nenhum usuário possa criar um endereço IP público.
- **Tráfego de saída** – se você quiser impedir que máquinas virtuais acessem a Internet pública diretamente e forçar o tráfego por meio de um firewall corporativo, você pode rotear o tráfego local por meio de rota expressa ou VPN, usando o roteamento forçado.

> [!NOTE]
> Se você tiver um servidor proxy que bloqueia o tráfego sem configurações de proxy, não se esqueça de adicionar exceções à conta de armazenamento do artefato do laboratório.

Você também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Esta etapa adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que minha rede virtual existente não está salvando corretamente?
Uma possibilidade é que seu nome de rede virtual contenha pontos. Nesse caso, tente remover os períodos ou substituí-los por hifens. Em seguida, tente salvar a rede virtual novamente.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que obtenho um erro "recurso pai não encontrado" ao provisionar uma VM do PowerShell?
Quando um recurso é pai de outro recurso, o recurso pai deve existir antes de você criar o recurso filho. Se o recurso pai não existir, você verá uma mensagem **ParentResourceNotFound** . Se você não especificar uma dependência no recurso pai, o recurso filho poderá ser implantado antes do pai.

As VMs são recursos filho em um laboratório em um grupo de recursos. Quando você usa modelos do Resource Manager para implantar VMs usando o PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [solucionar erros comuns de implantação do Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações de erro se uma implantação de VM falhar?
Os erros de implantação de VM são capturados nos logs de atividade. Você pode encontrar os logs de atividade da VM do laboratório em **logs de auditoria** ou **diagnóstico de máquina virtual** no menu de recursos na página VM do laboratório (a página aparece depois que você seleciona a VM na lista minhas máquinas virtuais).

Às vezes, o erro de implantação ocorre antes do início da implantação da VM. Um exemplo é quando o limite de assinatura para um recurso que foi criado com a VM é excedido. Nesse caso, os detalhes do erro são capturados nos logs de atividade de nível de laboratório. Os logs de atividade estão localizados na parte inferior das definições de **configuração e políticas** . Para obter mais informações sobre como usar logs de atividade no Azure, consulte [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/resource-group-audit.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Por que obtenho o erro "o local não está disponível para o tipo de recurso" ao tentar criar um laboratório?
Você poderá ver uma mensagem de erro semelhante à seguinte quando tentar criar um laboratório: 

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Você pode resolver esse erro executando uma das seguintes etapas:

#### <a name="option-1"></a>Opção 1
Verifique a disponibilidade do tipo de recurso nas regiões do Azure na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) . Se o tipo de recurso não estiver disponível em uma determinada região, o DevTest Labs não oferecerá suporte à criação de um laboratório nessa região. Selecione outra região ao criar seu laboratório. 

#### <a name="option-2"></a>Opção 2
Se o tipo de recurso estiver disponível em sua região, verifique se ele está registrado com sua assinatura. Isso pode ser feito no nível do proprietário da assinatura, conforme mostrado neste [artigo](../azure-resource-manager/resource-manager-supported-services.md). 


