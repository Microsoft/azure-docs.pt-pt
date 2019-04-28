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
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: 91c598bde0912cffb8aa1dd7ba022c86a9084faa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127400"
---
# <a name="azure-devtest-labs-faq"></a>FAQ de Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre o Azure DevTest Labs.

## <a name="blog-post"></a>Mensagem do blogue
O blog da equipe de laboratórios DevTest foi preterido a partir de 20 de Março de 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Onde posso rastrear as atualizações de funcionalidades de agora em diante?
De agora em diante, publicaremos atualizações de funcionalidades e postagens em blog informativo no blogue do Azure e atualizações do Azure. Estas publicações no blogue também vão ligar a nossa documentação onde necessário.

Subscrever o [Blog de Azure DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) e [DevTest Labs Azure atualiza](https://azure.microsoft.com/updates/?product=devtest-lab) para se manter informado sobre as novas funcionalidades no DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>O que acontece com as postagens de blog existente?
Atualmente estamos migrando postagens de blog existente (excluindo as atualizações de interrupção) para nosso [documentação dos laboratórios DevTest](devtest-lab-overview.md). Quando o blog do MSDN foi preterido, ele será redirecionado para a descrição geral da documentação para os laboratórios DevTest. Depois de redirecionada, pode procurar o artigo que está procurando no título "Filtrar por". Podemos ainda não migrados todas as mensagens ainda, mas deve ser feitos ao final deste mês. 


### <a name="where-do-i-see-outage-updates"></a>Onde posso ver as atualizações de indisponibilidade?
Publicaremos atualizações de interrupção usando nosso identificador do Twitter daqui em diante. Siga-no Twitter para obter as atualizações mais recentes sobre as falhas e erros conhecidos.

### <a name="twitter"></a>Twitter
Nosso identificador do Twitter: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não respondida aqui?
Se sua pergunta não estiver listada aqui, conte para nós, portanto, podemos ajudar a encontrar uma resposta.

- Poste uma pergunta no final nestas perguntas frequentes. Interaja com a equipe de Cache do Azure e outros membros da Comunidade sobre este artigo.
- Para aceder um vasto público, postar uma pergunta sobre o [fórum MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Interaja com a equipe de laboratórios DevTest do Azure e outros membros da Comunidade.
- Para pedidos de funcionalidades, submeter os pedidos e idéias para [voz do utilizador do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>O que é uma conta Microsoft?
Uma conta Microsoft é uma conta que utiliza para quase tudo o que fazer com o Microsoft dispositivos e serviços. É um endereço de e-mail e palavra-passe que utilizou para iniciar sessão no Skype, Outlook.com, OneDrive, Windows phone, o Azure e Xbox Live. Uma única conta significa que seus arquivos, fotos, contactos e configurações podem acompanhar a em qualquer dispositivo.
 
> [!NOTE]
> Uma conta Microsoft era anteriormente denominado um Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que motivo devo utilizar o Azure DevTest Labs?
O Azure DevTest Labs pode economizar tempo da Equipe e dinheiro. Os desenvolvedores podem criar seus próprios ambientes, utilizando várias bases de diferentes. Também podem utilizar artefactos para implementar e configurar aplicativos rapidamente. Ao utilizar imagens personalizadas e fórmulas, pode guardar máquinas virtuais (VMs) como modelos e reproduzi-los facilmente em toda a equipe. DevTest Labs oferece também várias políticas configuráveis, esse laboratório, os administradores podem usar para reduzir o desperdício e gerir ambientes de uma equipe. Estas políticas incluem o encerramento automático, o limiar de custo, VMs máximos por utilizador e o tamanho máximo da VM. Para obter uma explicação mais aprofundada do DevTest Labs, consulte a [descrição geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "livre gestão personalizada"?
Livre Self-Service significa que os desenvolvedores e testadores criam seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que DevTest Labs pode ajudar a definir o acesso adequado, minimize o desperdício e controlar os custos. Os administradores podem especificar quais os tamanhos de VM são permitidos, o número máximo de VMs, e quando as VMs estão a utilizar e encerrar. DevTest Labs também torna mais fácil monitorizar os custos e definir alertas, que o ajudarão a ficar ciente de como os recursos do laboratório estão sendo usados.

### <a name="how-can-i-use-devtest-labs"></a>Como posso utilizar os laboratórios DevTest?
DevTest Labs é útil sempre que necessita de desenvolvimento ou os ambientes de teste e desejar reproduzi-los rapidamente ou geri-los ao utilizar políticas de poupança de custos.
Seguem-se alguns cenários em que os nossos clientes utilizam o DevTest Labs para:

- Gerir o desenvolvimento e os ambientes de teste num único lugar. Utilizar políticas para reduzir os custos e criar imagens personalizadas para partilhar baseia-se entre a equipe.
- Desenvolva uma aplicação ao utilizar imagens personalizadas para guardar o estado do disco em todas as fases de desenvolvimento.
- Controle de custos em relação ao progresso.
- Crie ambientes de teste em massa para fins de teste de garantia de qualidade.
- Utilize artefactos e fórmulas facilmente configurar e reproduzir uma aplicação em vários ambientes.
- Distribuir VMs aos programadores dos encontros (trabalho colaborativo de desenvolvimento ou teste) e, em seguida, desaprovisioná-las facilmente quando o evento terminar.

### <a name="how-am-i-billed-for-devtest-labs"></a>Como me são faturadas laboratórios DevTest?
DevTest Labs é um serviço gratuito. Criar laboratórios e configurar políticas, modelos e artefactos no DevTest Labs são gratuito. Paga apenas pelos recursos do Azure utilizados em seus laboratórios, como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo dos recursos de laboratório, consulte [preços de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Segurança

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os níveis de segurança diferentes no DevTest Labs?
Acesso de segurança é determinado pelo controlo de acesso baseado em funções (RBAC). Para saber como funciona o acesso, ele ajuda a saber as diferenças entre uma permissão, uma função e um âmbito, conforme definido pelo RBAC.

- **Permissão**: Uma permissão é um acesso definidas para uma ação específica. Por exemplo, uma permissão pode ser o acesso de leitura a todas as VMs.
- **Função**: Uma função é um conjunto de permissões que podem ser agrupados e atribuídos a um usuário. Por exemplo, um utilizador com uma função de proprietário da subscrição tem acesso a todos os recursos numa subscrição.
- **Âmbito**: Um âmbito é um nível na hierarquia de um recurso do Azure. Por exemplo, um âmbito pode ser um grupo de recursos, um único laboratório ou a subscrição completa.

Dentro do escopo do DevTest Labs, existem dois tipos de funções que definem as permissões de utilizador:

- **Proprietário do laboratório**: Proprietário de um laboratório tem acesso a todos os recursos no laboratório. Proprietário de um laboratório pode modificar as políticas, ler e escrever todas as VMs, altere a rede virtual e assim por diante.
- **Utilizador do laboratório**: Um utilizador de laboratório, pode ver todos os recursos de laboratório, como VMs, políticas e redes virtuais. No entanto, um utilizador de laboratório não é possível modificar as políticas ou de VMS para as quais foram criados por outros utilizadores.

Também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, veja [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, o utilizador é concedido automaticamente essas permissões em cada âmbito de nível inferior no âmbito. Por exemplo, se um utilizador é atribuído a função de proprietário da subscrição, o utilizador tem acesso a todos os recursos numa subscrição. Esses recursos incluem VMs, redes virtuais e laboratórios. Um proprietário da subscrição herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdade. Proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de subscrição. Por isso, o proprietário de um laboratório não consegue ver VMs, redes virtuais ou outros recursos que estão fora do laboratório.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Como definir o controlo de acesso baseado em funções para meu DevTest Labs ambientes para se certificar de que o departamento de TI pode governam enquanto os desenvolvedores/teste pode fazer seu trabalho?
Há um amplo padrão, no entanto, o detalhe depende da sua organização.

Central IT deve apenas o que é necessário o proprietário e ativar as equipes de projeto e a aplicação para ter o nível necessário de controle. Normalmente, isso significa que central IT proprietária da subscrição e as funções de TI, como configurações de redes de principais de identificadores. O conjunto de **proprietários** para uma subscrição deve ser pequena. Estes proprietários podem nomeie os proprietários adicionais quando for necessário, ou aplicar políticas ao nível da subscrição, por exemplo "sem IP público".

Pode haver um subconjunto de utilizadores que necessitam de acesso através de uma subscrição, como o suporte de nível 1 ou camada 2. Neste caso, recomendamos que dê estes utilizadores a **contribuinte** para que eles podem gerir os recursos, mas não fornecer acesso de utilizador ou ajustar as políticas de acesso.

O recurso de DevTest Labs deve ser propriedade de proprietários que estiverem prestes a equipe de projeto/aplicação. É porque entendem os respetivos requisitos para máquinas e de software necessárias. Na maioria das organizações, o proprietário deste recurso do DevTest Labs geralmente é o líder de projeto/desenvolvimento. Este proprietário pode gerir utilizadores e as políticas de dentro do ambiente de laboratório e pode gerir todas as VMs no ambiente do DevTest Labs.

Os membros da equipe de projeto/aplicação devem ser adicionados para o **DevTest Labs utilizadores** função. Estes utilizadores podem criar máquinas virtuais (em linha com o laboratório e as políticas ao nível da subscrição). Também podem gerir suas próprias máquinas virtuais. Eles não consegue gerir máquinas virtuais que pertencem a outros utilizadores.

Para obter mais informações, consulte [estrutura empresarial do Azure – documentação de governação de subscrições prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Como posso criar uma função para permitir que os usuários façam uma tarefa específica?
Para obter um artigo abrangente sobre como criar funções personalizadas e atribua permissões a uma função, veja [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Eis um exemplo de um script que cria a função **utilizador de avançada do DevTest Labs**, que tem permissão para iniciar e parar todas as VMs no laboratório:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Como pode uma organização Certifique-se de políticas de segurança empresarial no local?
Uma organização pode obtê-lo ao fazer as seguintes ações:

- Desenvolver e publicar uma política de segurança abrangente. As regras de uso aceitável associado à utilização de articule toda a política de software, os recursos na cloud. Também define o que claramente viola a política.
- Desenvolva uma imagem personalizada, artefacto personalizado e um processo de implantação que permite a orquestração dentro de território de segurança, que é definido com o active directory. Essa abordagem impõe limite da empresa e define um conjunto comum de controlos ambientais. Esses controles contra o ambiente de um desenvolvedor pode considerar à medida que desenvolveram e seguem um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo é também a disponibilizar um ambiente que não seja excessivamente restritivo esse Maio atrapalham desenvolvimento, mas um conjunto razoável de controles. As políticas de grupo, a unidade organizacional (UO) que contém as máquinas virtuais do laboratório podem ser um subconjunto das políticas de grupo total que se encontram na produção. Em alternativa, eles podem ser um conjunto adicional para corretamente atenuar quaisquer riscos identificados.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Como uma organização pode garantir a integridade dos dados para se certificar de que os desenvolvedores de comunicação remota não é possível remover o código ou introduzir malware ou software não aprovado?
Existem várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de serviços ou funcionários que estão a comunicação remota no colaborar no DevTest Labs.

Conforme indicado anteriormente, o primeiro passo tem de ter uma política de uso aceitável esboçada e definidos que descreve claramente as conseqüências quando alguém viola a política.

A primeira camada de controles para acesso remoto é aplicar uma política de acesso remoto através de uma ligação de VPN que não está ligada diretamente ao laboratório.

A segunda camada de controles é aplicar um conjunto de objetos de política de grupo que evitar copiar e colar através do ambiente de trabalho remoto. Uma política de rede poderão ser implementada por não permitir a saída dos serviços de ambiente, como o FTP e serviços RDP fora do ambiente. Encaminhamento definido pelo utilizador foi forçar todo o tráfego de rede do Azure para o local, mas o roteamento não foi possível conta para todos os URLs que podem permitir o carregamento de dados, a menos que controlado através de um proxy para analisar o conteúdo e sessões. IPs públicos poderia haver restrições dentro da rede virtual que suporta o DevTest Labs para não permitir o protocolo de bridge de um recurso de rede externa.

Por fim, o mesmo tipo de restrições de tem de ser aplicado em toda a organização, que seria de conta para todos os métodos possíveis de mídia removível ou URLs externos que podem aceitar uma publicação de conteúdo. Consulte a segurança profissional para rever e implementar uma política de segurança. Para obter mais recomendações, veja [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configuração do laboratório

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como criar um laboratório a partir de um modelo do Resource Manager?
Oferecemos uma [repositório do GitHub dos modelos do Azure Resource Manager de laboratório](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) que pode implementar como-é ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem uma ligação para implementar o laboratório, porque está na sua própria subscrição do Azure. Em alternativa, pode personalizar o modelo e [implementar com o PowerShell ou CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Posso ter todas as máquinas virtuais a ser criado num grupo de recursos comuns em vez disso, tendo cada máquina no seu próprio grupo de recursos? 
Sim, como um proprietário de laboratório, pode optar por permitir que o laboratório processam a alocação de grupo de recursos para ou ter todas as máquinas virtuais criado num grupo de recursos comuns que especificar. 

Cenário de grupo de recursos separado:
-   DevTest Labs cria um novo grupo de recursos para todas as máquinas de virtuais IP público/privado que acelerar
-   DevTest Labs cria um grupo de recursos para as máquinas IP compartilhados que pertencem ao mesmo tamanho.

Cenário de grupo de recursos comuns:
-   Todas as máquinas virtuais são criadas no grupo de recursos comuns que especificar. Saiba mais [alocação de grupo de recursos para o laboratório](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Como posso manter uma convenção de nomenclatura em meu ambiente de laboratórios DevTest?
Talvez queira estender atual convenções de nomenclatura de enterprise para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs. Ao implementar o DevTest Labs, recomendamos que tenha políticas específicas de partida. Implemente estas políticas por um script central e modelos JSON para impor a consistência. Políticas de atribuição de nomes pode ser implementada através de políticas do Azure aplicadas-se ao nível da subscrição. Para exemplos JSON para o Azure Policy, veja [exemplos do Azure Policy](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Laboratórios de quantos pode criar na mesma subscrição
Não existe um limite específico no número de laboratórios que podem ser criadas por subscrição. No entanto, a quantidade de recursos utilizados por subscrição é limitada. Pode ler sobre o [limites e quotas das subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>O número de VMs pode criar por laboratório?
Não existe nenhum limite específico no número de VMs que podem ser criadas por laboratório. No entanto, os recursos (núcleos VM, endereços IP públicos e assim por diante) que são utilizados são limitados por subscrição. Pode ler sobre o [limites e quotas das subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Como posso determinar a taxa de usuários por laboratório e o número geral de laboratórios que são necessários em toda a organização?
Recomendamos unidades de negócios e os grupos de desenvolvimento que estão associados com o mesmo projeto de desenvolvimento que estão associados com o mesmo laboratório. Permite mesmos tipos de políticas, imagens e políticas de encerramento para ser aplicada a ambos os grupos.

Também poderá considerar limites geográficos. Por exemplo, os desenvolvedores no Norte Leste dos Estados Unidos (EUA) podem usar um laboratório aprovisionado em e.u.a. Leste 2. Além disso, os desenvolvedores em Dallas, no Texas e Denver, Colorado podem ser direcionados para utilizar um recurso no e.u.a. centro-Sul. Se houver um esforço colaborativo com um terceiro externo, eles podem ser atribuídos a um laboratório que não seja utilizado por programadores internos.

Também pode usar um laboratório para um projeto específico nos projetos de DevOps do Azure. Em seguida, aplicar segurança através de um grupo do Azure Active Directory especificado, que permite o acesso a ambos os conjunto de recursos. A rede virtual atribuída ao laboratório pode ser outro limite a consolidar os utilizadores.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Como podemos impedir a eliminação de recursos dentro de um laboratório?
Recomendamos que defina permissões adequadas ao nível do laboratório, para que somente usuários autorizados possam eliminar recursos ou alterar as políticas de laboratório. Os desenvolvedores devem ser colocados dentro de **os utilizadores de laboratórios DevTest** grupo. O gerente de desenvolvimento ou o líder de infra-estrutura deve ser o **DevTest Labs proprietário**. Recomendamos que tenha apenas dois proprietários de laboratório. Esta política se estende para o repositório de código para evitar danos. Os utilizadores de laboratório tem direitos para utilizar recursos, mas não é possível atualizar as políticas de laboratório. Consulte o artigo seguinte que lista as funções e direitos de que tem de cada grupo incorporado dentro de um laboratório: [Adicionar proprietários e utilizadores no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como posso partilhar uma ligação direta para meu laboratório?

1. Na [portal do Azure](https://portal.azure.com), vá para o laboratório.
2. Copiar o **URL de laboratório** do seu navegador e, em seguida, partilhá-lo com os seus utilizadores de laboratório.

> [!NOTE]
> Se um utilizador de laboratório é um utilizador externo, quem tem uma conta Microsoft, mas que não seja um membro da instância do Active Directory da sua organização, o utilizador poderá ver uma mensagem de erro quando tentarem aceder a ligação partilhada. Se um utilizador externo vê uma mensagem de erro, pedir ao utilizador que selecione primeiro o respetivo nome no canto superior direito do portal do Azure. Em seguida, na secção de diretório do menu, o usuário pode selecionar o diretório onde existe o laboratório.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Por que motivo não vejo as VMs na página de máquinas virtuais que vejo no DevTest Labs?
Quando cria uma VM no DevTest Labs, é-lhe fornecido permissão para aceder a essa VM. Pode ver a VM na página de laboratórios e no **máquinas virtuais** página. Utilizadores atribuídos para o **proprietário do DevTest Labs** função pode ver todas as VMs que foram criadas no laboratório do laboratório **todas as máquinas virtuais** página. No entanto, os utilizadores que têm o **utilizador de DevTest Labs** função não recebem automaticamente acesso de leitura aos recursos da VM que outros usuários tenham criado. Então, essas VMs não são apresentadas no **máquinas virtuais** página.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como posso criar várias VMs do modelo mesmo ao mesmo tempo?
Tem duas opções para criar simultaneamente várias VMs a partir do mesmo modelo:

- Pode utilizar o [extensão de tarefas de DevOps do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Pode [gerar um modelo do Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) enquanto estiver a criar uma VM, e [implementar o modelo de Gestor de recursos do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Também é possível especificar mais de uma instância de uma máquina a ser criada durante a criação da máquina virtual. Para saber mais sobre a criação de várias instâncias de máquinas virtuais, consulte o documento sobre [criar uma máquina virtual de laboratório](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como posso mover minhas VMs do Azure existentes em meu laboratório do DevTest Labs?
Para copiar as suas VMs existentes para o DevTest Labs:

1.  Copiar o ficheiro VHD da sua VM existente com uma [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Criar a [imagem personalizada](devtest-lab-create-template.md) dentro do seu laboratório do DevTest Labs.
3.  Crie uma VM no laboratório de sua imagem personalizada.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Pode anexar vários discos para as minhas VMs?

Sim, pode anexar vários discos para as suas VMs.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se quiser utilizar uma imagem de SO do Windows para o meu teste, é necessário que adquirir uma assinatura do MSDN?
Para utilizar imagens de SO de cliente Windows (Windows 7 ou uma versão posterior) para o desenvolvimento ou teste no Azure, siga um dos seguintes passos:

- [Adquira uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se tiver um Enterprise Agreement, criar uma subscrição do Azure com o [oferta Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos do Azure para cada oferta do MSDN, consulte [crédito mensal do Azure para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de eliminação de todas as VMs em meu laboratório?
Como proprietário de um laboratório, pode eliminar as VMs a partir do seu laboratório no portal do Azure. Também pode eliminar todas as VMs no seu laboratório com um script do PowerShell. No exemplo a seguir, sob o **valores mudem** comentar, modifique os valores de parâmetros. Pode recuperar o subscriptionId labResourceGroup e labName valores de painel de laboratório no portal do Azure.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Como posso utilizar modelos do Resource Manager no meu ambiente de laboratórios DevTest?
Implementar modelos do Resource Manager num ambiente de DevTest Labs, utilizando os passos mencionados na [funcionalidade de ambientes no DevTest Labs](devtest-lab-test-env.md) artigo. Basicamente, verifique os modelos do Resource Manager para um repositório de Git (repositórios do Azure ou GitHub) e adicione um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório. Este cenário não pode ser útil se estiver a utilizar o DevTest Labs para máquinas de desenvolvimento do host, mas pode ser útil se estiver criando um ambiente de teste, que é representativo da produção.

Também vale a pena observar que o número de máquinas virtuais por laboratório ou por opção do usuário apenas limita o número de máquinas criadas de forma nativa no laboratório em si e não por qualquer ambientes (modelos do Resource Manager).

## <a name="custom-images"></a>Imagens Personalizadas

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Como posso configurar um processo repetível facilmente para trazer as minhas imagens organizacionais personalizadas num ambiente de laboratórios DevTest?
Ver isso [vídeo no padrão de fábrica de imagem](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Este cenário é um cenário avançado e os scripts fornecidos são apenas os scripts de exemplo. Se forem necessárias quaisquer alterações, precisa gerenciar e manter os scripts utilizados no seu ambiente.

Para obter informações detalhadas sobre como criar uma fábrica de imagem, consulte [crie uma fábrica de imagem personalizada no Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>O que é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é uma imagem gerida. Uma fórmula é uma imagem que pode ser configurado com definições adicionais e, em seguida, salvar e reproduzir. Uma imagem personalizada poderá ser preferível se pretender criar rapidamente vários ambientes usando a mesma imagem básica, imutável. Uma fórmula pode ser melhor se pretender reproduzir a configuração da sua VM com os bits mais recentes, como parte de uma rede virtual ou sub-rede, ou como uma VM de um tamanho específico. Para obter uma explicação mais aprofundada, veja [comparar imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando devo utilizar uma fórmula versus a imagem personalizada?
Normalmente, o fator decisivo neste cenário, o custo é e reutilizar. Se tiver um cenário em que muitos utilizadores/laboratórios requerem uma imagem com muita software parte superior da imagem base, poderia reduzir os custos através da criação de uma imagem personalizada. Isso significa que a imagem é criada uma vez. Ele reduz o tempo de configuração de máquina virtual e o custo incorrido porque a máquina virtual em execução quando ocorre a configuração.

No entanto, um fator adicional a observar é a frequência das alterações ao seu pacote de software. Se executar o diária baseia-se e exigir que o software seja em máquinas de virtuais dos seus utilizadores, considere a utilização de uma fórmula em vez de uma imagem personalizada.

Para obter uma explicação mais aprofundada, veja [comparar imagens personalizadas e fórmulas](devtest-lab-comparing-vm-base-image-types.md) no DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como posso automatizar o processo de carregamento de ficheiros do VHD para criar imagens personalizadas?

Para automatizar a carregar arquivos VHD para criar imagens personalizadas, tem duas opções:

- Uso [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) para copiar ou carregar ficheiros VHD para a conta de armazenamento associada ao laboratório.
- Uso [Explorador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Explorador de armazenamento é uma aplicação autónoma que é executado no Windows, OS X e Linux.

Para localizar a conta de armazenamento de destino associada ao seu laboratório:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu da esquerda, selecione **grupos de recursos**.
3.  Localize e selecione o grupo de recursos associada ao seu laboratório.
4.  Sob **descrição geral**, selecione uma das contas de armazenamento.
5.  Selecione **Blobs**.
6.  Procure carregamentos na lista. Se não existir, retornar para o passo 4 e tente outra conta de armazenamento.
7.  Utilize o **URL** como o destino de seu comando do AzCopy.

Quando devo utilizar uma imagem do Marketplace do Azure vs. minha própria imagem personalizada do organizacional?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quando devo utilizar uma imagem do Marketplace do Azure vs. minha própria imagem personalizada do organizacional?
O Azure Marketplace deve ser utilizado por predefinição, a menos que tem preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração do software complexo que precisa de uma aplicação para ser incluído como parte da imagem base.
- Instalação e configuração de um aplicativo podem demorar várias horas, o que não são um uso eficiente de tempo de computação para ser adicionado a uma imagem do Azure Marketplace.
- Os desenvolvedores e testadores necessitam de acesso a uma máquina virtual rapidamente e desejam minimizar o tempo de configuração de uma nova máquina virtual.
- Conformidade ou regulamentação condições (por exemplo, as políticas de segurança) que devem ser cumpridos para todas as máquinas.
- Utilizar imagens personalizadas não deve ser considerado apenas superficialmente. Eles introduzem a complexidade extra, como agora tem de gerir os ficheiros VHD para tais subjacentes imagens base. Também tem de corrigir regularmente as imagens bases com atualizações de software. Estas atualizações incluem novas atualizações de sistema operativo (SO) e quaisquer atualizações ou alterações de configuração necessárias para o pacote de software propriamente dito.

## <a name="artifacts"></a>Artefactos

### <a name="what-are-artifacts"></a>Quais são os artefactos?
Artefactos são elementos personalizáveis, que pode utilizar para implementar os bits mais recentes ou implementar as suas ferramentas de desenvolvimento a uma VM. Anexe artefactos para a VM ao criar a VM. Depois da VM está aprovisionada, os artefactos de implementar e configurar a sua VM. Vários artefactos existentes estão disponíveis no nosso [repositório do GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Também pode [criar artefactos da sua própria](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefacto falhou durante a criação de VM. Como posso resolvê-lo?
Para saber como obter registos para o artefacto com falhas, veja [como diagnosticar falhas de artefactos no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando uma organização utilizar um repositório público de artefactos vs. o repositório de artefactos privados no DevTest Labs?
O [repositório público de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são utilizados mais frequentemente. Ajuda com a implementação rápida sem ter de investir tempo para reproduzir as ferramentas de desenvolvedor comuns e suplementos. Pode optar por implementar seu próprio repositório privado. Pode usar um público e um repositório privado em paralelo. Também pode optar por desativar o repositório público. Os critérios para implementar um repositório privado devem ser controlados pelas seguintes perguntas e considerações:

- A organização tem um requisito para que o software licenciado empresarial como parte da sua oferta de laboratórios DevTest? Se a resposta é Sim, deve ser criado um repositório privado.
- A organização desenvolver software personalizado que fornece uma operação específica, o que é necessária como parte do processo geral de aprovisionamento? Se a resposta for Sim, em seguida, um repositório privado deve ser implementado.
- Se a política de Governança da organização requer isolamento e repositórios externos não estão sob a gestão da configuração direta pela organização, um repositório de artefactos privados deve ser implementado. Como parte deste processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desativado para que ninguém dentro da organização consegue aceder-lhe mais. Essa abordagem força todos os utilizadores dentro da organização ter apenas um único repositório for aprovado pela organização e minimizar os descompassos de configuração.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Deve-se uma organização planear para um único repositório ou permitir que vários repositórios?
Como parte da estratégia de gestão de configuração e de governação de global da sua organização, recomendamos que utilize um repositório centralizado. Quando usa vários repositórios, pode-se tornar silos de software não-gerenciado no tempo. Com um repositório central, várias equipes podem consumir artefactos deste repositório para seus projetos. Impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte de relação à centralização, as seguintes ações são recomendadas práticas recomendadas para gerenciamento a longo prazo e de sustentabilidade:

- Associe os repositórios do Azure com o mesmo inquilino do Azure Active Directory que a subscrição do Azure está a utilizar para autenticação e autorização.
- Crie um grupo chamado `All DevTest Labs Developers` no Azure Active Directory que será gerido centralmente. Qualquer desenvolvedor que contribui para o desenvolvimento de artefacto deve ser colocado neste grupo.
- O mesmo grupo de Azure Active Directory pode ser utilizado para fornecer acesso para o repositório de repositórios do Azure e para o laboratório.
- No Azure repositórios, ramificação ou criar o fork deve ser utilizado para um repositório separado no desenvolvimento do repositório principal de produção. Conteúdo só é adicionado ao ramo principal com um pedido pull após uma revisão de código adequado. Assim que o revisor de código aprova a alteração, um desenvolvedor-chefe, o que é responsável pela manutenção do ramo principal, mescla o código atualizado.

## <a name="cicd-integration"></a>Integração de CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrar o DevTest Labs com minha coleção de ferramentas de CI/CD?
Se estiver a utilizar o Azure DevOps, pode utilizar um [extensão de tarefas de laboratórios DevTest](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar o seu pipeline de lançamento no DevTest Labs. Algumas das tarefas que pode fazer com esta extensão incluem:

- Criar e implementar automaticamente uma VM. Também pode configurar a VM com a compilação mais recente com tarefas de cópia de ficheiros do Azure ou serviços de DevOps do Azure PowerShell.
- Capture automaticamente o estado de uma VM após os testes para reproduzir um erro na mesma VM para uma investigação mais aprofundada.
- Elimine a VM no final do pipeline de versões, quando já não for necessário.

As seguinte mensagens de blogue oferta orientações e informações sobre como utilizar a extensão de serviços de DevOps do Azure:

- [Laboratórios Dev/Test e a extensão do Azure DevOps](integrate-environments-devops-pipeline.md)
- [Implementar uma nova VM num laboratório DevTest Labs existente dos serviços de DevOps do Azure](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Com o Azure DevOps Services release management para implementações contínuas DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outra integração contínua (CI) / cadeias de ferramentas de entrega contínua (CD), é possível obter os mesmos cenários por implantar [modelos Azure Resource Manager](https://azure.microsoft.com/resources/templates/) utilizando [cmdlets do Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Também pode utilizar [APIs REST para os laboratórios DevTest](https://aka.ms/dtlrestapis) para integrar com a sua coleção de ferramentas.

## <a name="networking"></a>Redes

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando devo criar uma nova rede virtual para meu ambiente de DevTest Labs vs. utilizar uma rede virtual existente?
Se as suas VMs precisam interagir com a infraestrutura existente, considere utilizar uma rede virtual existente no seu ambiente do DevTest Labs. Se utilizar o ExpressRoute, pode querer minimizar a quantidade de VNets / sub-redes, de modo a que não fragmentados seu espaço de endereços IP que é atribuído para utilização nas subscrições. 

Considere utilizar aqui o padrão de peering de VNet ([modelo de Hub-and-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) também. Esta abordagem permite a comunicação de vnet/sub-rede em várias subscrições. Caso contrário, cada ambiente de DevTest Labs poderia ter sua própria rede virtual. 

Existem [limites](../azure-subscription-service-limits.md) no número de redes virtuais por subscrição. O período predefinido é 50, apesar deste limite pode ser gerado a 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando devo utilizar um IP partilhado versus o IP público versus privado IP?
 
Se utilizar um site-site VPN ou Expressroute, considere a utilização de IPs privados para que as suas máquinas são acessíveis por meio de sua rede interna e acessível pela internet pública.

> [!NOTE]
> Os proprietários de laboratório podem alterar esta política de sub-rede para se certificar de que ninguém acidental cria endereços IP públicos para as suas VMs. O proprietário da subscrição deve criar uma política de subscrição para impedir que os IPs públicos que está sendo criado.

Quando utilizar IPs públicos partilhado, as máquinas virtuais num laboratório de partilhar um endereço IP público. Essa abordagem pode ser útil quando precisa evitar que viola os limites em endereços IP públicos de uma determinada subscrição.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Como posso Certifique-se de que o desenvolvimento e teste de máquinas virtuais são não é possível aceder à internet pública? Existem quaisquer padrões recomendados para definir a configuração de rede?

Sim. Existem dois aspetos a considerar – o tráfego de entrada e saído.

- **Tráfego de entrada** – se a máquina virtual não tem um endereço IP público, em seguida, ele não pode ser acessado pela internet. Uma abordagem comum é garantir que está definida uma política de nível de assinatura, que nenhum utilizador pode criar um endereço IP público.
- **Tráfego de saída** -se de que pretende impedir que as máquinas virtuais aceder diretamente à internet pública e forçar o tráfego através de uma firewall empresarial, em seguida, pode encaminhar o tráfego no local através de express route ou VPN, através da utilização forçada encaminhamento.

> [!NOTE]
> Se tiver um servidor proxy que bloqueia o tráfego sem as definições de proxy, não se esqueça de adicionar exceções para a conta de armazenamento de artefactos do laboratório.

Também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Este passo adiciona uma camada adicional de proteção para permitir / bloquear o tráfego.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que não é minha existente virtual de rede a guardar corretamente?
Uma possibilidade é que o seu nome de rede virtual contém períodos. Nesse caso, tente remover os períodos ou substituí-los com hífenes. Em seguida, tente novamente guardar a rede virtual.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que motivo recebo um erro "O recurso principal não encontrado" ao aprovisionar uma VM a partir do PowerShell?
Quando um recurso é um elemento principal para outro recurso, o recurso principal tem de existir antes de criar o recurso de subordinados. Se o recurso principal não existir, verá uma **ParentResourceNotFound** mensagem. Se não especificar uma dependência no recurso principal, o recurso subordinado pode ser implementado antes do elemento principal.

As VMs são recursos subordinados num laboratório num grupo de recursos. Ao utilizar modelos do Resource Manager para implementar VMs com o PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [resolver erros comuns de implementação do Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações sobre o erro se uma implementação de VM falhar?
Erros de implementação de VM são capturados nos registos de atividades. Pode encontrar registos de atividade da VM no laboratório **registos de auditoria** ou **diagnósticos de máquinas virtuais** no menu de recursos na página VM do laboratório (a página é apresentada depois de selecionar a VM a partir da minha lista de máquinas virtuais).

Às vezes, o erro de implementação ocorre antes do início da implementação da VM. Um exemplo é quando o limite de subscrição de um recurso que foi criado com a VM foi excedido. Neste caso, os detalhes do erro são capturados nos registos de atividades de nível de laboratório. Registos de atividades estão localizados na parte inferior a **Konfigurace a zásady** definições. Para obter mais informações sobre como utilizar a atividade de registos no Azure, consulte [ver registos de atividades para auditar as ações em recursos](../azure-resource-manager/resource-group-audit.md).




