---
title: FAQ de laboratórios DevTest do Azure | Documentos da Microsoft
description: Encontre respostas a perguntas comuns sobre o Azure DevTest Labs.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7d845b0e434f21c6db46cddb26b7f55aeb33d080
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733780"
---
# <a name="azure-devtest-labs-faq"></a>FAQ de Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre o Azure DevTest Labs.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Geral**
## <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não respondida aqui?
Se a sua pergunta não estiver listada aqui, conte para nós, portanto, podemos ajudar a encontrar uma resposta.

* Poste uma pergunta no final nestas perguntas frequentes. Interaja com a equipe de Cache do Azure e outros membros da Comunidade sobre este artigo.
* Para aceder um vasto público, postar uma pergunta sobre o [fórum MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Interaja com a equipe de laboratórios DevTest do Azure e outros membros da Comunidade.
* Para pedidos de funcionalidades, submeter os pedidos e idéias para [voz do utilizador do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Por que motivo devo utilizar o Azure DevTest Labs?
O Azure DevTest Labs pode economizar tempo da Equipe e dinheiro. Os desenvolvedores podem criar seus próprios ambientes, utilizando várias bases de diferentes. Também podem utilizar artefactos para implementar e configurar aplicativos rapidamente. Ao utilizar imagens personalizadas e fórmulas, pode guardar máquinas virtuais (VMs) como modelos e reproduzi-los facilmente em toda a equipe. DevTest Labs oferece também várias políticas configuráveis, esse laboratório, os administradores podem usar para reduzir o desperdício e gerir ambientes de uma equipe. Estas políticas incluem o encerramento automático, o limiar de custo, VMs máximos por utilizador e o tamanho máximo da VM. Para obter uma explicação mais aprofundada do DevTest Labs, consulte a [descrição geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>O que significa "livre gestão personalizada"?
Livre Self-Service significa que os desenvolvedores e testadores criam seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que o DevTest Labs pode ajudar a minimizar o desperdício e o controlo de custos. Os administradores podem especificar quais os tamanhos de VM são permitidos, o número máximo de VMs, e quando as VMs estão a utilizar e encerrar. DevTest Labs também torna mais fácil monitorizar os custos e definir alertas, que o ajudarão a ficar ciente de como os recursos do laboratório estão sendo usados.

## <a name="how-can-i-use-devtest-labs"></a>Como posso utilizar os laboratórios DevTest?
DevTest Labs é útil sempre que necessita de desenvolvimento ou os ambientes de teste e desejar reproduzi-los rapidamente ou geri-los ao utilizar políticas de poupança de custos.

Seguem-se alguns cenários em que os nossos clientes utilizam o DevTest Labs para:

* Gerir o desenvolvimento e os ambientes de teste num único lugar. Utilizar políticas para reduzir os custos e criar imagens personalizadas para partilhar baseia-se entre a equipe.
* Desenvolva uma aplicação ao utilizar imagens personalizadas para guardar o estado do disco em todas as fases de desenvolvimento.
* Controle de custos em relação ao progresso.
* Crie ambientes de teste em massa para fins de teste de garantia de qualidade.
* Utilize artefactos e fórmulas facilmente configurar e reproduzir uma aplicação em vários ambientes.
* Distribuir VMs aos programadores dos encontros (trabalho colaborativo de desenvolvimento ou teste) e, em seguida, desaprovisioná-las facilmente quando o evento terminar.

## <a name="how-am-i-billed-for-devtest-labs"></a>Como me são faturadas laboratórios DevTest?
DevTest Labs é um serviço gratuito. Criar laboratórios e configurar políticas, modelos e artefactos no DevTest Labs são gratuito. Paga apenas pelos recursos do Azure utilizados em seus laboratórios, como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo dos recursos de laboratório, consulte [preços de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Segurança**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os níveis de segurança diferentes no DevTest Labs?
Acesso de segurança é determinado pelo [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/built-in-roles.md). Para saber como funciona o acesso, ele ajuda a saber as diferenças entre uma permissão, uma função e um âmbito, conforme definido pelo RBAC.

* **Permissão**: Uma permissão é um acesso definidas para uma ação específica. Por exemplo, uma permissão pode ser o acesso de leitura a todas as VMs.
* **Função**: Uma função é um conjunto de permissões que podem ser agrupados e atribuídos a um usuário. Por exemplo, um utilizador com uma função de proprietário da subscrição tem acesso a todos os recursos numa subscrição.
* **Âmbito**: Um âmbito é um nível na hierarquia de um recurso do Azure. Por exemplo, um âmbito pode ser um grupo de recursos, um único laboratório ou a subscrição completa.

Dentro do escopo do DevTest Labs, existem dois tipos de funções que definem as permissões de utilizador:

* **Proprietário do laboratório**: Proprietário de um laboratório tem acesso a todos os recursos no laboratório. Proprietário de um laboratório pode modificar as políticas, ler e escrever todas as VMs, altere a rede virtual e assim por diante.
* **Utilizador do laboratório**: Um utilizador de laboratório, pode ver todos os recursos de laboratório, como VMs, políticas e redes virtuais. No entanto, um utilizador de laboratório não é possível modificar as políticas ou de VMS para as quais foram criados por outros utilizadores. 

Também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, veja [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, o utilizador é concedido automaticamente essas permissões em cada âmbito de nível inferior no âmbito. Por exemplo, se um utilizador é atribuído a função de proprietário da subscrição, o utilizador tem acesso a todos os recursos numa subscrição. Esses recursos incluem todas as VMs, todas as redes virtuais e todos os laboratórios. Um proprietário da subscrição herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdade. Proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de subscrição. Portanto, o proprietário de um laboratório não é possível ver as VMs, redes virtuais ou outros recursos que estão fora do laboratório.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como posso criar uma função para permitir que os utilizadores executar uma tarefa específica?
Para obter um artigo abrangente sobre como criar funções personalizadas e atribua permissões a uma função, veja [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Eis um exemplo de um script que cria a função *utilizador de avançada do DevTest Labs*, que tem permissão para iniciar e parar todas as VMs no laboratório:

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


**CI/CD integração e Automação**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrar o DevTest Labs com minha coleção de ferramentas de CI/CD?
Se estiver a utilizar o Azure DevOps, pode utilizar um [extensão de tarefas de laboratórios DevTest](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar o seu pipeline de lançamento no DevTest Labs. Algumas das tarefas que pode fazer com esta extensão incluem:

* Criar e implementar automaticamente uma VM. Também pode configurar a VM com a compilação mais recente com tarefas de cópia de ficheiros do Azure ou serviços de DevOps do Azure PowerShell.
* Capture automaticamente o estado de uma VM após os testes para reproduzir um erro na mesma VM para uma investigação mais aprofundada.
* Elimine a VM no final do pipeline de versões, quando já não for necessário.

As seguinte mensagens de blogue oferta orientações e informações sobre como utilizar a extensão de serviços de DevOps do Azure:

* [Laboratórios Dev/Test e a extensão do Azure DevOps](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Implementar uma nova VM num laboratório DevTest Labs existente dos serviços de DevOps do Azure](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Com o Azure DevOps Services release management para implementações contínuas DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outra integração contínua (CI) / cadeias de ferramentas de entrega contínua (CD), é possível obter os mesmos cenários por implantar [modelos Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) utilizando [cmdlets do Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Também pode utilizar [APIs REST para os laboratórios DevTest](https://aka.ms/dtlrestapis) para integrar com a sua coleção de ferramentas.  


**Máquinas virtuais**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Por que motivo não vejo as VMs no painel de máquinas virtuais que vejo no DevTest Labs?
Quando cria uma VM no DevTest Labs, tem permissão para aceder a essa VM. Pode ver a VM no painel de laboratórios e no **máquinas virtuais** painel. Utilizadores atribuídos à função de utilizador do laboratório DevTest Labs, podem ver todas as VMs que foram criadas no laboratório do laboratório **todas as máquinas virtuais** painel. No entanto, os utilizadores que têm a função de utilizador de laboratório do DevTest Labs não recebem automaticamente acesso de leitura aos recursos da VM que outros usuários tenham criado. Portanto, essas VMs não são apresentadas no **máquinas virtuais** painel.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>O que é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é um disco rígido virtual (VHD). Uma fórmula é uma imagem que pode ser configurado com definições adicionais e, em seguida, salvar e reproduzir. Uma imagem personalizada poderá ser preferível se pretender criar rapidamente vários ambientes usando a mesma imagem básica, imutável. Uma fórmula pode ser melhor se pretender reproduzir a configuração da sua VM com os bits mais recentes, como parte de uma rede virtual ou sub-rede, ou como uma VM de um tamanho específico. Para obter uma explicação mais aprofundada, veja [comparar imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como posso criar várias VMs do modelo mesmo ao mesmo tempo?
Tem duas opções para criar simultaneamente várias VMs a partir do mesmo modelo:
* Pode utilizar o [extensão de tarefas de DevOps do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Pode [gerar um modelo do Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) enquanto estiver a criar uma VM, e [implementar o modelo de Gestor de recursos do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como posso mover minhas VMs do Azure existentes em meu laboratório do DevTest Labs?
Para copiar as suas VMs existentes para o DevTest Labs:

1. Copiar o ficheiro VHD da sua VM existente com uma [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Criar a imagem personalizada](devtest-lab-create-template.md) dentro do seu laboratório do DevTest Labs.
3. Crie uma VM no laboratório de sua imagem personalizada.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Pode anexar vários discos para as minhas VMs?
Sim, pode anexar vários discos para as suas VMs.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se quiser utilizar uma imagem de SO do Windows para o meu teste, é necessário que adquirir uma assinatura do MSDN?
Para utilizar imagens de SO de cliente Windows (Windows 7 ou uma versão posterior) para o desenvolvimento ou teste no Azure, terá de efetuar um dos seguintes:

- [Adquira uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se tiver um Enterprise Agreement, criar uma subscrição do Azure com o [oferta Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos do Azure para cada oferta do MSDN, consulte [crédito mensal do Azure para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como posso automatizar o processo de carregamento de ficheiros do VHD para criar imagens personalizadas?
Para automatizar a carregar arquivos VHD para criar imagens personalizadas, tem duas opções:

* Uso [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) para copiar ou carregar ficheiros VHD para a conta de armazenamento associada ao laboratório.
* Uso [Explorador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Explorador de armazenamento é uma aplicação autónoma que é executado no Windows, OS X e Linux.   

Para localizar a conta de armazenamento de destino associada ao seu laboratório:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. No menu da esquerda, selecione **grupos de recursos**.
3. Localize e selecione o grupo de recursos associada ao seu laboratório.
4. Sob **descrição geral**, selecione uma das contas de armazenamento.
5. Selecione **Blobs**.
6. Procure carregamentos na lista. Se não existir, retornar para o passo 4 e tente outra conta de armazenamento.
7. Utilize o **URL** como o destino de seu comando do AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de eliminação de todas as VMs em meu laboratório?
Pode eliminar as VMs a partir do seu laboratório no portal do Azure. Também pode eliminar todas as VMs no seu laboratório com um script do PowerShell. No exemplo a seguir, sob o **valores mudem** comentar, modifique os valores de parâmetros. Pode recuperar os `subscriptionId`, `labResourceGroup`, e `labName` valores a partir do painel de laboratório no portal do Azure.

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

**Artefactos**
## <a name="what-are-artifacts"></a>Quais são os artefactos?
Artefactos são elementos personalizáveis, que pode utilizar para implementar os bits mais recentes ou implementar as suas ferramentas de desenvolvimento a uma VM. Anexe artefactos para a VM ao criar a VM. Depois da VM está aprovisionada, os artefactos de implementar e configurar a sua VM. Vários artefactos preexistentes estão disponíveis no nosso [repositório do GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Também pode [criar artefactos da sua própria](devtest-lab-artifact-author.md).


**Configuração do laboratório**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como criar um laboratório a partir de um modelo do Resource Manager?
Oferecemos uma [repositório do GitHub dos modelos do Azure Resource Manager de laboratório](https://aka.ms/dtlquickstarttemplate) que pode implementar como-é ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem um link para implementar o laboratório como-está em sua própria subscrição do Azure. Em alternativa, pode personalizar o modelo e [implementar com o PowerShell ou CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que as minhas VMs são criadas em grupos de recursos diferentes, com nomes arbitrários? É possível mudar o nome ou modificar estes grupos de recursos?
Grupos de recursos são criados dessa forma, para que o DevTest Labs pode gerir as permissões de utilizador e o acesso às VMs. Embora pode mover uma VM para outro grupo de recursos e utilize o nome que desejar, recomendamos que não faça alterações para os grupos de recursos. Estamos a trabalhar em melhorar esta experiência para permitir mais flexibilidade.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Laboratórios de quantos pode criar na mesma subscrição
Não existe um limite específico no número de laboratórios que podem ser criadas por subscrição. No entanto, a quantidade de recursos utilizados por subscrição é limitada. Pode ler sobre o [limites e quotas das subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>O número de VMs pode criar por laboratório?
Não existe nenhum limite específico no número de VMs que podem ser criadas por laboratório. No entanto, os recursos (núcleos VM, endereços IP públicos e assim por diante) que são utilizados são limitados por subscrição. Pode ler sobre o [limites e quotas das subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como posso partilhar uma ligação direta para meu laboratório?

1. No portal do Azure, vá para o laboratório.
2. Copie o URL de laboratório a partir do seu browser e, em seguida, partilhá-lo com os seus utilizadores de laboratório.

> [!NOTE]
> Se um utilizador de laboratório é um utilizador externo que tem um [conta Microsoft](#what-is-a-microsoft-account), mas que não seja um membro da instância do Active Directory da sua organização, o utilizador poderá ver uma mensagem de erro quando tentarem aceder a ligação partilhada. Se um utilizador externo vê uma mensagem de erro, pedir ao utilizador que selecione primeiro o respetivo nome no canto superior direito do portal do Azure. Em seguida, na **Directory** secção do menu, o utilizador pode selecionar o diretório onde existe o laboratório.
>
>

## <a name="what-is-a-microsoft-account"></a>O que é uma conta Microsoft?
Uma conta Microsoft é uma conta que utiliza para quase tudo o que fazer com o Microsoft dispositivos e serviços. É um endereço de e-mail e palavra-passe que utiliza para iniciar sessão no Skype, Outlook.com, OneDrive, Windows phone e Xbox Live. Uma única conta significa que seus arquivos, fotos, contactos e configurações podem acompanhar a em qualquer dispositivo.

> [!NOTE]
> Uma conta Microsoft era anteriormente denominado um *Windows Live ID*.
>
>


**Resolução de problemas**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefacto falhou durante a criação de VM. Como posso resolvê-lo?
Para saber como obter registos para o artefacto com falhas, veja [como diagnosticar falhas de artefactos no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que não é minha existente virtual de rede a guardar corretamente?
Uma possibilidade é que o seu nome de rede virtual contém períodos. Nesse caso, tente remover os períodos ou substituí-los com hífenes. Em seguida, tente novamente guardar a rede virtual.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que motivo recebo um erro "O recurso principal não encontrado" ao aprovisionar uma VM a partir do PowerShell?
Quando um recurso é um elemento principal para outro recurso, o recurso principal tem de existir antes de criar o recurso de subordinados. Se o recurso principal não existir, verá uma **ParentResourceNotFound** mensagem. Se não especificar uma dependência no recurso principal, o recurso subordinado pode ser implementado antes do elemento principal.

As VMs são recursos subordinados num laboratório num grupo de recursos. Ao utilizar modelos do Resource Manager para implementar VMs com o PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [resolver erros comuns de implementação do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações sobre o erro se uma implementação de VM falhar?
Erros de implementação de VM são capturados nos registos de atividades. Pode encontrar registos de atividade da VM no laboratório **registos de auditoria** ou **diagnósticos de máquinas virtuais** no menu de recursos no painel VM do laboratório (no painel que aparece depois de selecionar a VM a partir do **meu virtual máquinas** lista).

Às vezes, o erro de implementação ocorre antes do início da implementação da VM. Um exemplo é quando o limite de subscrição de um recurso que foi criado com a VM foi excedido. Neste caso, os detalhes do erro são capturados nos registos de atividades de nível de laboratório. Registos de atividades estão localizados na parte inferior a **Konfigurace a zásady** definições. Para obter mais informações sobre como utilizar a atividade de registos no Azure, consulte [ver registos de atividades para auditar as ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
