---
title: Azure DevTest Labs FAQ Microsoft Docs
description: Este artigo fornece respostas a algumas das perguntas frequentes (FAQ) sobre o Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270787"
---
# <a name="azure-devtest-labs-faq"></a>FAQ de Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre o Azure DevTest Labs.

## <a name="blog-post"></a>Post de blog
O nosso blog da DevTest Labs Team foi retirado a partir de 20 de março de 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Onde posso rastrear as atualizações de funcionalidades a partir de agora?
A partir de agora, vamos publicar atualizações de funcionalidades e publicações de blogs informativos no blog do Azure e atualizações do Azure. Estas publicações de blog também se ligarão à nossa documentação sempre que necessário.

Subscreva as atualizações [do DevTest Labs Azure Blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) e [DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) para se manter informado sobre novas funcionalidades nos Laboratórios DevTest.

### <a name="what-happens-to-the-existing-blog-posts"></a>O que acontece com as publicações de blogs existentes?
Estamos neste momento a trabalhar na migração de posts de blogs existentes (excluindo atualizações de falhas) para a documentação da [Nossa DevTest Labs.](devtest-lab-overview.md) Quando o blog da MSDN for depreciado, será redirecionado para a visão geral da documentação para a DevTest Labs. Uma vez redirecionado, pode pesquisar o artigo que procura no título 'Filter by'. Ainda não emigrámos todos os postos, mas devemos estar prontos até ao final deste mês. 


### <a name="where-do-i-see-outage-updates"></a>Onde vejo atualizações de interrupções?
Vamos publicar atualizações de outage usando o nosso cabo do Twitter a partir de agora. Siga-nos no Twitter para obter as últimas atualizações sobre interrupções e bugs conhecidos.

### <a name="twitter"></a>Twitter
A nossa alça do Twitter:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?
Se a sua pergunta não estiver listada aqui, avise-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final desta FAQ.
- Para chegar a um público mais vasto, publique uma pergunta no [fórum Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Envolva-se com a equipa azure DevTest Labs e outros membros da comunidade.
- Para pedidos de funcionalidades, envie os seus pedidos e ideias para a Voz do Utilizador do [Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>O que é uma conta microsoft?
Uma conta Microsoft é uma conta que utiliza para quase tudo o que faz com dispositivos e serviços da Microsoft. É um endereço de e-mail e senha que usas para assinar no Skype, Outlook.com, OneDrive, Windows phone, Azure e Xbox Live. Uma única conta significa que os seus ficheiros, fotos, contactos e definições podem segui-lo em qualquer dispositivo.

> [!NOTE]
> Uma conta da Microsoft costumava ser chamada de ID do Windows Live.

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar laboratórios Azure DevTest?
A Azure DevTest Labs pode poupar tempo e dinheiro à sua equipa. Os desenvolvedores podem criar os seus próprios ambientes usando várias bases diferentes. Também podem usar artefactos para implementar e configurar rapidamente aplicações. Ao utilizar imagens e fórmulas personalizadas, pode guardar máquinas virtuais (VMs) como modelos e reproduzi-las facilmente em toda a equipa. A DevTest Labs também oferece várias políticas configuráveis que os administradores de laboratório podem usar para reduzir o desperdício e gerir os ambientes de uma equipa. Estas políticas incluem a paragem automática, o limiar de custos, os VMs máximos por utilizador e o tamanho máximo de VM. Para obter uma explicação mais aprofundada da DevTest Labs, consulte a [visão geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "autosserviço sem preocupações"?
O autosserviço sem preocupações significa que os desenvolvedores e testadores criam os seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que a DevTest Labs pode ajudar a definir o acesso adequado, minimizar os custos de resíduos e de controlo. Os administradores podem especificar quais os tamanhos de VM permitidos, o número máximo de VMs e quando os VMs são iniciados e desligados. A DevTest Labs também facilita a monitorização de custos e alertas, para ajudá-lo a manter-se atento à forma como os recursos de laboratório estão a ser usados.

### <a name="how-can-i-use-devtest-labs"></a>Como posso usar os Laboratórios DevTest?
O DevTest Labs é útil sempre que necessitar de ambientes de dev ou teste, e quer reproduzi-los rapidamente, ou geri-los usando políticas de poupança de custos.
Aqui estão alguns cenários que os nossos clientes usam DevTest Labs para:

- Gerencie ambientes de dev e teste num só local. Use políticas para reduzir custos e criar imagens personalizadas para partilhar construções em toda a equipa.
- Desenvolver uma aplicação utilizando imagens personalizadas para salvar o estado do disco em todas as fases de desenvolvimento.
- Acompanhar o custo em relação ao progresso.
- Crie ambientes de teste em massa para testes de garantia de qualidade.
- Utilize artefactos e fórmulas para configurar e reproduzir facilmente uma aplicação em vários ambientes.
- Distribua VMs para hackathons (trabalho colaborativo ou trabalho de teste), e, em seguida, facilmente os desprovisionie quando o evento terminar.

### <a name="how-am-i-billed-for-devtest-labs"></a>Como é que eu estou a cobrar pelos Laboratórios DevTest?
A DevTest Labs é um serviço gratuito. Criar laboratórios e configurar políticas, modelos e artefactos em DevTest Labs é gratuito. Paga-se apenas pelos recursos azure utilizados nos seus laboratórios, como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo dos recursos laboratoriais, consulte o preço dos [Laboratórios Azure DevTest.](https://azure.microsoft.com/pricing/details/devtest-lab/)

## <a name="security"></a>Segurança

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os diferentes níveis de segurança nos Laboratórios DevTest?
O acesso à segurança é determinado pelo Controlo de Acesso baseado em Funções (RBAC). Para aprender como funciona o acesso, ajuda a aprender as diferenças entre uma permissão, um papel e um âmbito, conforme definido pelo RBAC.

- **Permissão**: Uma permissão é um acesso definido a uma ação específica. Por exemplo, uma permissão pode ser lida acesso a todos os VMs.
- **Função**: Um conjunto de permissões que podem ser agruparadas e atribuídas a um utilizador. Por exemplo, um utilizador com uma função de proprietário de subscrição tem acesso a todos os recursos dentro de uma subscrição.
- **Âmbito**: Um âmbito é um nível dentro da hierarquia de um recurso Azure. Por exemplo, um âmbito pode ser um grupo de recursos, um único laboratório, ou toda a subscrição.

No âmbito da DevTest Labs, existem dois tipos de funções que definem as permissões dos utilizadores:

- **Dono**de laboratório: Um dono de laboratório tem acesso a todos os recursos do laboratório. Um dono de laboratório pode modificar políticas, ler e escrever a quaisquer VMs, alterar a rede virtual, e assim por diante.
- **Utilizador de laboratório**: Um utilizador de laboratório pode ver todos os recursos de laboratório, tais como VMs, políticas e redes virtuais. No entanto, um utilizador de laboratório não pode modificar políticas ou quaisquer VMs que tenham sido criados por outros utilizadores.

Também pode criar papéis personalizados em DevTest Labs. Para aprender a criar papéis personalizados em DevTest Labs, consulte [as permissões](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)dos utilizadores do Grant para políticas específicas de laboratório.

Como os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, o utilizador recebe automaticamente essas permissões em todos os âmbitos de nível inferior no âmbito. Por exemplo, se um utilizador for atribuído ao papel de proprietário de subscrição, o utilizador tem acesso a todos os recursos numa subscrição. Estes recursos incluem VMs, redes virtuais e laboratórios. Um proprietário de subscrição herda automaticamente o papel de dono de laboratório. No entanto, o contrário não é verdade. Um dono de laboratório tem acesso a um laboratório, que é um âmbito inferior ao nível de subscrição. Então, um dono de laboratório não pode ver VMs, redes virtuais ou quaisquer outros recursos que estejam fora do laboratório.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Como posso definir o controlo de acesso baseado em papéis para os meus ambientes DevTest Labs para garantir que as TI podem governar enquanto os desenvolvedores/testes podem fazer o seu trabalho?
Há um padrão amplo, no entanto o detalhe depende da sua organização.

As TI centrais devem possuir apenas o necessário e permitir que as equipas de projeto e aplicação tenham o nível de controlo necessário. Normalmente, significa que o centro de TI é dono da subscrição e lida com funções de TI fundamentais, tais como configurações de networking. O conjunto de **proprietários** para uma subscrição deve ser pequeno. Estes proprietários podem nomear proprietários adicionais quando há necessidade, ou aplicar políticas de nível de subscrição, por exemplo "No PUBLIC IP".

Pode haver um subconjunto de utilizadores que requerem acesso através de uma subscrição, como suporte tier1 ou tier 2. Neste caso, recomendamos que dê a estes utilizadores o acesso ao **contribuinte** para que possam gerir os recursos, mas não fornecer acesso ao utilizador ou ajustar políticas.

O recurso DevTest Labs deve ser propriedade de proprietários próximos da equipa de projeto/aplicação. É porque compreendem os seus requisitos para máquinas e software necessário. Na maioria das organizações, o proprietário deste recurso DevTest Labs é comumente o líder de projeto/desenvolvimento. Este proprietário pode gerir utilizadores e políticas dentro do ambiente de laboratório e pode gerir todos os VMs no ambiente DevTest Labs.

Os membros da equipa de projeto/aplicação devem ser adicionados à função **de Utilizadores de DevTest Labs.** Estes utilizadores podem criar máquinas virtuais (em linha com as políticas de laboratório e nível de subscrição). Também podem gerir as suas próprias máquinas virtuais. Não conseguem gerir máquinas virtuais que pertencem a outros utilizadores.

Para mais informações, consulte [o andaime da empresa Azure – documentação prescritiva de governação por subscrição.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Como posso criar um papel que permita aos utilizadores fazeruma tarefa específica?
Para um artigo abrangente sobre como criar papéis personalizados e atribuir permissões a um papel, consulte [as permissões dos utilizadores](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)do Grant para políticas específicas do laboratório . Aqui está um exemplo de um script que cria o papel **DevTest Labs Advanced User,** que tem permissão para iniciar e parar todos os VMs no laboratório:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Como pode uma organização garantir que as políticas de segurança corporativa estão em vigor?
Uma organização pode alcançá-lo fazendo as seguintes ações:

- Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associadas ao software de utilização, ativos em nuvem. Define também o que viola claramente a política.
- Desenvolver uma imagem personalizada, artefactos personalizados e um processo de implantação que permita a orquestração dentro do reino de segurança que é definido com diretório ativo. Esta abordagem impõe a fronteira das empresas e estabelece um conjunto comum de controlos ambientais. Estes controlos contra o ambiente que um desenvolvedor pode considerar à medida que se desenvolvem e seguem um ciclo de vida seguro para o desenvolvimento como parte do seu processo global. O objetivo também é proporcionar um ambiente que não seja excessivamente restritivo que possa dificultar o desenvolvimento, mas um conjunto razoável de controlos. As políticas do grupo na unidade de organização (OU) que contém máquinas virtuais de laboratório podem ser um subconjunto das políticas totais de grupo que são encontradas na produção. Em alternativa, podem ser um conjunto adicional para mitigar adequadamente quaisquer riscos identificados.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Como pode uma organização garantir a integridade dos dados para garantir que os desenvolvedores em remo não podem remover código ou introduzir malware ou software não aprovado?
Existem várias camadas de controlo para mitigar a ameaça de consultores externos, empreiteiros ou funcionários que estão a colaborar na DevTest Labs.

Como já foi referido, o primeiro passo deve ter uma política de utilização aceitável elaborada e definida que delineie claramente as consequências quando alguém viola a política.

A primeira camada de controlos para acesso remoto é aplicar uma política de acesso remoto através de uma ligação VPN que não esteja diretamente ligada ao laboratório.

A segunda camada de controlos é aplicar um conjunto de objetos de política de grupo que impedem a cópia e a pasta através de ambiente seletiva remota. Poderia ser implementada uma política de rede para não permitir serviços de saída do ambiente, como serviços FTP e RDP fora do ambiente. O encaminhamento definido pelo utilizador poderia forçar todo o tráfego da rede Azure de volta ao local, mas o encaminhamento não poderia explicar todos os URLs que poderiam permitir o upload de dados a menos que fosse controlado através de um proxy para digitalizar conteúdo e sessões. Os IPs públicos poderiam ser restringidos dentro da rede virtual que suporta a DevTest Labs para não permitir a ponte de um recurso de rede externo.

Em última análise, o mesmo tipo de restrições deve ser aplicada em toda a organização, o que explicaria todos os métodos possíveis de meios amovíveis ou URLs externos que pudessem aceitar um post de conteúdo. Consulte o seu profissional de segurança para rever e implementar uma política de segurança. Para mais recomendações, consulte [a Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configuração do laboratório

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como posso criar um laboratório a partir de um modelo de Gestor de Recursos?
Oferecemos um [repositório GitHub de modelos](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) do Gestor de Recursos do Laboratório Azure que você pode implementar como está ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem um link para implantar o laboratório como está na sua própria subscrição Azure. Ou, pode personalizar o modelo e [implementar utilizando powerShell ou Azure CLI](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Posso ter todas as máquinas virtuais para serem criadas num grupo comum de recursos, em vez de ter cada máquina no seu próprio grupo de recursos?
Sim, como dono de laboratório, pode deixar o laboratório lidar com a atribuição de recursos para si ou ter todas as máquinas virtuais criadas num grupo comum de recursos que especifice.

Cenário de grupo de recursos separado:
-   A DevTest Labs cria um novo grupo de recursos para cada máquina virtual IP pública/privada que você gira
-   A DevTest Labs cria um grupo de recursos para máquinas IP partilhadas que pertencem ao mesmo tamanho.

Cenário de grupo de recursos comuns:
-   Todas as máquinas virtuais são fiadas no grupo de recursos comuns que especifica. Saiba mais alocação de [grupo de recursos para o laboratório.](https://aka.ms/RGControl)

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Como mantenho uma convenção de nomeação no meu ambiente de Laboratórios DevTest?
Você pode querer estender as convenções de nomeação da empresa atual para operações Azure e torná-las consistentes em todo o ambiente DevTest Labs. Ao implementar o DevTest Labs, recomendamos que tenha políticas de início específicas. Você implementa estas políticas através de um script central e modelos JSON para impor a consistência. As políticas de nomeação podem ser implementadas através de políticas azure aplicadas ao nível da subscrição. Para obter amostras json para a Política Azure, consulte [as amostras da Política Azure](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios posso criar sob a mesma subscrição?
Não há um limite específico no número de laboratórios que podem ser criados por subscrição. No entanto, a quantidade de recursos utilizados por subscrição é limitada. Pode ler sobre os [limites e quotas para as assinaturas Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e [como aumentar estes limites.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)


### <a name="how-many-vms-can-i-create-per-lab"></a>Quantos VMs posso criar por laboratório?
Não existe um limite específico para o número de VMs que podem ser criados por laboratório. No entanto, os recursos (núcleos VM, endereços IP públicos, e assim por diante) que são utilizados são limitados por subscrição. Pode ler sobre os [limites e quotas para as assinaturas Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e [como aumentar estes limites.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Como determino a proporção de utilizadores por laboratório e o número total de laboratórios que são necessários em toda uma organização?
Recomendamos que unidades de negócio e grupos de desenvolvimento associados ao mesmo projeto de desenvolvimento estejam associados ao mesmo laboratório. Permite que sejam aplicadas aos dois grupos os mesmos tipos de políticas, imagens e políticas de encerramento.

Também pode ter de considerar os limites geográficos. Por exemplo, os desenvolvedores no Nordeste dos Estados Unidos (EUA) podem usar um laboratório aprovisionado no Leste dos EUA. E, desenvolvedores em Dallas, Texas, e Denver, Colorado podem ser direcionados para usar um recurso na Central Sul dos EUA. Se houver um esforço colaborativo com um terceiro externo, eles podem ser atribuídos a um laboratório que não é usado por desenvolvedores internos.

Você também pode usar um laboratório para um projeto específico dentro do Projeto Azure DevOps. Em seguida, aplica a segurança através de um determinado grupo de DirectórioActivo Azure, que permite o acesso a ambos os conjuntos de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar os utilizadores.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Como podemos impedir a supressão de recursos dentro de um laboratório?
Recomendamos que estabeleça permissões adequadas ao nível do laboratório para que apenas os utilizadores autorizados possam eliminar recursos ou alterar as políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **DevTest Labs Users.** O promotor principal ou o chumbo da infraestrutura devem ser o **Proprietário de Laboratórios DevTest.** Recomendamos que só tenha dois donos de laboratório. Esta política estende-se ao repositório de códigos para evitar a corrupção. Os utilizadores de laboratório têm direito suster recursos, mas não conseguem atualizar as políticas do laboratório. Consulte o seguinte artigo que lista as funções e direitos que cada grupo incorporado tem dentro de um laboratório: [Adicione proprietários e utilizadores em Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como partilho uma ligação direta com o meu laboratório?

1. No [portal Azure,](https://portal.azure.com)vá ao laboratório.
2. Copie o URL do **laboratório** do seu navegador e, em seguida, partilhe-o com os seus utilizadores do laboratório.

> [!NOTE]
> Se um utilizador de laboratório for um utilizador externo que tenha uma conta Microsoft, mas que não seja membro da instância de Diretório Ativo da sua organização, o utilizador poderá ver uma mensagem de erro quando tentar aceder ao link partilhado. Se um utilizador externo vir uma mensagem de erro, peça ao utilizador para primeiro selecionar o seu nome no canto superior direito do portal Azure. Em seguida, na secção de Diretório do menu, o utilizador pode selecionar o diretório onde o laboratório existe.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Por que não vejo VMs na página de Máquinas Virtuais que vejo nos Laboratórios DevTest?
Quando crias um VM nos Laboratórios DevTest, é-te dada permissão para acederes a esse VM. Pode ver o VM tanto na página dos laboratórios como na página das **Máquinas Virtuais.** Os utilizadores atribuídos à função **de Proprietário de Laboratórios DevTest** podem ver todos os VMs que foram criados em laboratório na página **All Virtual Machines** do laboratório. No entanto, os utilizadores que possuam a função **DevTest Labs User** não têm acesso de leitura automática aos recursos VM que outros utilizadores criaram. Assim, esses VMs não são exibidos na página **de Máquinas Virtuais.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como posso criar vários VMs do mesmo modelo ao mesmo tempo?
Você tem duas opções para criar simultaneamente vários VMs a partir do mesmo modelo:

- Pode utilizar a extensão de [tarefas Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Você pode [gerar um modelo de Gestor](devtest-lab-add-vm.md#save-azure-resource-manager-template) de Recursos enquanto você está criando um VM, e implementar o modelo de Gestor de Recursos a partir do Windows [PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Também pode especificar mais de um caso de uma máquina a ser criada durante a criação de máquinas virtuais. Para saber mais sobre a criação de múltiplas instâncias de máquinas virtuais, consulte o médico na criação de uma máquina virtual de [laboratório.](devtest-lab-add-vm.md)

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como posso mover os meus VMs Azure existentes para o meu laboratório de Laboratórios DevTest?
Para copiar os seus VMs existentes para DevTest Labs:

1.  Copie o ficheiro VHD do vM existente utilizando um [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Crie a [imagem personalizada](devtest-lab-create-template.md) dentro do laboratório de DevTest Labs.
3.  Crie um VM no laboratório a partir da sua imagem personalizada.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Posso ligar vários discos aos meus VMs?

Sim, pode anexar vários discos aos seus VMs.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se eu quiser usar uma imagem do Windows OS para os meus testes, tenho de comprar uma subscrição MSDN?
Para utilizar imagens do Cliente Windows (Windows 7 ou uma versão posterior) para o seu desenvolvimento ou teste no Azure, tome uma das seguintes etapas:

- [Compre uma subscrição MSDN.](https://www.visualstudio.com/products/how-to-buy-vs)
- Se tiver um Acordo de Empresa, crie uma subscrição Azure com a [oferta Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos Azure para cada oferta da MSDN, consulte o [crédito Mensal Azure para assinantes do Estúdio Visual.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como automatizar o processo de apagar todos os VMs do meu laboratório?
Como dono de laboratório, pode eliminar vMs do seu laboratório no portal Azure. Também pode eliminar todos os VMs do seu laboratório usando um script PowerShell. No exemplo seguinte, sob os **valores para alterar** comentários, modificar os valores do parâmetro. Você pode recuperar os valores de subscriçãoId, labResourceGroup e labName do painel de laboratório no portal Azure.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Como posso usar modelos de Gestor de Recursos no meu Ambiente de Laboratórios DevTest?
Você implementa os seus modelos de Gestor de Recursos em um ambiente DevTest Labs usando passos mencionados na funcionalidade Ambientes no artigo [DevTest Labs.](devtest-lab-test-env.md) Basicamente, você verifica os seus modelos de Gestor de Recursos num Repositório Git (ou Azure Repos ou GitHub), e adiciona um [repositório privado para os seus modelos](devtest-lab-test-env.md) ao laboratório. Este cenário pode não ser útil se estiver a usar o DevTest Labs para hospedar máquinas de desenvolvimento, mas pode ser útil se estiver a construir um ambiente de encenação, que é representativo da produção.

Também vale a pena notar que o número de máquinas virtuais por laboratório ou por opção de utilizador apenas limita o número de máquinas criadas de forma nativa no próprio laboratório, e não por quaisquer ambientes (modelos de Gestor de Recursos).

## <a name="custom-images"></a>Imagens Personalizadas

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Como posso configurar um processo facilmente repetível para trazer as minhas imagens organizacionais personalizadas para um ambiente de Laboratórios DevTest?
Veja este [vídeo no padrão image Factory](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Este cenário é um cenário avançado, e os scripts fornecidos são apenas scripts de amostra. Se forem necessárias alterações, é necessário gerir e manter os scripts utilizados no seu ambiente.

Para obter informações detalhadas sobre a criação de uma fábrica de imagem, consulte Criar uma fábrica de [imagem personalizada em Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é uma imagem gerida. Uma fórmula é uma imagem que pode configurar com configurações adicionais e, em seguida, poupar e reproduzir. Uma imagem personalizada pode ser preferível se quiser criar rapidamente vários ambientes usando a mesma imagem básica e imutável. Uma fórmula pode ser melhor se quiser reproduzir a configuração do seu VM com as partes mais recentes, como parte de uma rede virtual ou subnet, ou como um VM de um tamanho específico. Para obter uma explicação mais aprofundada, consulte [Comparar imagens e fórmulas personalizadas em DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando devo usar uma fórmula vs. imagem personalizada?
Tipicamente, o fator decisivo neste cenário é o custo e a reutilização. Se tiver um cenário em que muitos utilizadores/laboratórios necessitem de uma imagem com muito software em cima da imagem base, então poderia reduzir custos criando uma imagem personalizada. Significa que a imagem é criada uma vez. Reduz o tempo de configuração da máquina virtual e o custo incorrido devido ao funcionamento da máquina virtual quando ocorre a configuração.

No entanto, um fator adicional a notar é a frequência de alterações no seu pacote de software. Se executar diariamente as construções e exigir que esse software esteja nas máquinas virtuais dos seus utilizadores, considere usar uma fórmula em vez de uma imagem personalizada.

Para obter uma explicação mais aprofundada, consulte [Comparar imagens e fórmulas personalizadas](devtest-lab-comparing-vm-base-image-types.md) em DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como automatizar o processo de upload de ficheiros VHD para criar imagens personalizadas?

Para automatizar o upload de ficheiros VHD para criar imagens personalizadas, tem duas opções:

- Utilize o [AzCopy](../storage/common/storage-use-azcopy-v10.md) para copiar ou carregar ficheiros VHD para a conta de armazenamento que está associada ao laboratório.
- Utilize o Explorador de [Armazenamento Azure.](../vs-azure-tools-storage-manage-with-storage-explorer.md) Storage Explorer é uma aplicação autónoma que funciona no Windows, OS X e Linux.

Para encontrar a conta de armazenamento de destino que está associada ao seu laboratório:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu esquerdo, selecione **Grupos de Recursos**.
3.  Encontre e selecione o grupo de recursos que está associado ao seu laboratório.
4.  Em **resumo,** selecione uma das contas de armazenamento.
5.  Selecione **Blobs**.
6.  Procure uploads na lista. Se não existir, volte ao passo 4 e tente outra conta de armazenamento.
7.  Utilize o **URL** como destino no seu comando AzCopy.

Quando devo usar uma imagem do Azure Marketplace vs. a minha própria imagem organizacional personalizada?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quando devo usar uma imagem do Azure Marketplace vs. a minha própria imagem organizacional personalizada?
O Azure Marketplace deve ser utilizado por defeito, a menos que tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração complexa de software que requer que uma aplicação seja incluída como parte da imagem base.
- A instalação e configuração de uma aplicação podem demorar muitas horas, o que não é um uso eficiente do tempo de computação a ser adicionado numa imagem do Azure Marketplace.
- Os desenvolvedores e testadores requerem acesso a uma máquina virtual rapidamente, e querem minimizar o tempo de configuração de uma nova máquina virtual.
- Conformidade ou condições regulamentares (por exemplo, políticas de segurança) que devem estar em vigor para todas as máquinas.
- O uso de imagens personalizadas não deve ser considerado de ânimo leve. Introduzem uma complexidade extra, já que agora deve gerir ficheiros VHD para as imagens base subjacentes. Também precisa de remendar rotineiramente essas imagens base com atualizações de software. Estas atualizações incluem novas atualizações do sistema operativo (OS) e quaisquer atualizações ou alterações de configuração necessárias para o próprio pacote de software.

## <a name="artifacts"></a>Artefactos

### <a name="what-are-artifacts"></a>O que são artefactos?
Os artefactos são elementos personalizáveis que pode usar para implementar as suas últimas partes ou implementar as suas ferramentas de dev para um VM. Prenda artefactos ao seu VM quando criar o VM. Após o fornecimento do VM, os artefactos implantam e configuram o seu VM. Vários artefactos pré-existentes estão disponíveis no nosso [repositório público GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) Também pode [ser autor dos seus próprios artefactos.](devtest-lab-artifact-author.md)

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>O meu artefacto falhou durante a criação da VM. Como posso resolver isto?
Para aprender a obter registos para o seu artefacto falhado, veja como diagnosticar falhas de [artefactos em DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando deve uma organização usar um repositório de artefactos públicos vs. repositório de artefactos privados em DevTest Labs?
O [repositório de artefactos públicos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são mais utilizados. Ajuda na rápida implantação sem ter de investir tempo para reproduzir ferramentas e add-ins comuns. Pode optar por implantar o seu próprio repositório privado. Você pode usar um rescrito público e privado em paralelo. Pode também optar por desativar o repositório público. Os critérios para a implantação de um repositório privado devem ser orientados pelas seguintes perguntas e considerações:

- A organização tem a obrigação de ter software licenciado corporativo como parte da oferta da DevTest Labs? Se a resposta for sim, então deve ser criado um repositório privado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é necessária como parte do processo global de provisionamento? Se a resposta for sim, então um repositório privado deve ser implementado.
- Se a política de governação da organização requer isolamento, e os repositórios externos não estão sob gestão de configuração direta pela organização, um repositório de artefactos privados deve ser implementado. Como parte deste processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Depois, o repositório público pode ser desativado para que ninguém dentro da organização possa mais acessá-lo. Esta abordagem obriga todos os utilizadores dentro da organização a ter apenas um único repositório que é aprovado pela organização e minimizar a deriva de configuração.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Deve uma organização planear um único repositório ou permitir múltiplos repositórios?
Como parte da estratégia global de governação e gestão de configurações da sua organização, recomendamos que utilize um repositório centralizado. Quando se utiliza vários repositórios, podem tornar-se silos de software não gerido ao longo do tempo. Com um repositório central, várias equipas podem consumir artefactos deste repositório para os seus projetos. Impõe normalização, segurança, facilidade de gestão e elimina a duplicação de esforços. Como parte da centralização, as seguintes ações são práticas recomendadas para a gestão e sustentabilidade a longo prazo:

- Associe o Azure Repos ao mesmo inquilino do Azure Ative Directory que a assinatura Azure está a utilizar para autenticação e autorização.
- Crie um `All DevTest Labs Developers` grupo nomeado em Azure Ative Directory que seja gerido centralmente. Qualquer desenvolvedor que contribua para o desenvolvimento de artefactos deve ser colocado neste grupo.
- O mesmo grupo azure Ative Diretório pode ser usado para fornecer acesso ao repositório Azure Repos e ao laboratório.
- Em Azure Repos, a ramificação ou a forrking devem ser utilizadas para separar um repositório em desenvolvimento do repositório de produção primário. O conteúdo só é adicionado ao ramo principal com um pedido de puxão após uma revisão adequada do código. Uma vez que o revisor de código aprova a alteração, um desenvolvedor principal, responsável pela manutenção da sucursal principal, funde o código atualizado.

## <a name="cicd-integration"></a>Integração CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>A DevTest Labs integra-se com a minha cadeia de ferramentas CI/CD?
Se estiver a usar O Azure DevOps, pode utilizar uma [extensão de Tarefas DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar o seu pipeline de libertação nos Laboratórios DevTest. Algumas das tarefas que pode fazer com esta extensão incluem:

- Criar e implementar um VM automaticamente. Também pode configurar o VM com a mais recente construção utilizando tarefas Azure File Copy ou PowerShell Azure DevOps Services.
- Capture automaticamente o estado de um VM após testes para reproduzir um bug no mesmo VM para uma investigação mais aprofundada.
- Elimine o VM no final do gasoduto de libertação, quando já não for necessário.

As seguintes publicações de blog oferecem orientação e informações sobre a utilização da extensão dos Serviços Azure DevOps:

- [DevTest Labs e a extensão Azure DevOps](integrate-environments-devops-pipeline.md)
- [Implemente um novo VM num laboratório existente da DevTest Labs da Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Utilização da gestão de lançamento da Azure DevOps Services para implementações contínuas na DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outras cadeias de ferramentas de integração contínua (CI)/entrega contínua (CD), pode alcançar os mesmos cenários implantando modelos de [Gestor de Recursos Azure](https://azure.microsoft.com/resources/templates/) utilizando [cmdlets Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Também pode utilizar [APIs REST para DevTest Labs](https://aka.ms/dtlrestapis) para integrar com o seu sistema de ferramentas.

## <a name="networking"></a>Redes

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando devo criar uma nova rede virtual para o meu ambiente DevTest Labs vs. usando uma rede virtual existente?
Se os seus VMs precisam interagir com a infraestrutura existente, então considere usar uma rede virtual existente dentro do seu ambiente DevTest Labs. Se utilizar o ExpressRoute, poderá querer minimizar a quantidade de VNets/Subnets para que não fragmente o seu espaço de endereço IP que seja atribuído para utilização nas subscrições.

Considere usar o padrão de observação VNet aqui ([modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) também. Esta abordagem permite a comunicação vnet/subnet através de subscrições. Caso contrário, cada ambiente da DevTest Labs poderia ter a sua própria rede virtual.

Existem [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) para o número de redes virtuais por subscrição. O valor padrão é de 50, embora este limite possa ser aumentado para 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando devo usar um IP partilhado vs. IP público vs. IP privado?

Se utilizar uma VPN ou Rota Expresso de site-a-site, considere utilizar iPs privados para que as suas máquinas estejam acessíveis através da sua rede interna e inacessíveis através da internet pública.

> [!NOTE]
> Os donos de laboratóriopodem alterar esta política de sub-rede para garantir que ninguém cria acidentalmente endereços IP públicos para os seus VMs. O proprietário da subscrição deve criar uma política de subscrição que impeça a criação de IPs públicos.

Ao usar iPs públicos partilhados, as máquinas virtuais de um laboratório partilham um endereço IP público. Esta abordagem pode ser útil quando precisa evitar violar os limites dos endereços IP públicos para uma determinada subscrição.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Como posso garantir que o desenvolvimento e o teste das máquinas virtuais não conseguem chegar à internet pública? Existem padrões recomendados para configurar a configuração da rede?

Sim. Há dois aspetos a ter em conta : tráfego de entrada e saída.

- **Tráfego de entrada** – Se a máquina virtual não tiver um endereço IP público, então não pode ser contactado pela internet. Uma abordagem comum é garantir que seja definida uma política de nível de subscrição, de modo a que nenhum utilizador possa criar um endereço IP público.
- **Tráfego de saída** – Se pretender evitar que as máquinas virtuais acedam diretamente à internet pública e force o tráfego através de uma firewall corporativa, então pode encaminhar o tráfego no local através de rota expressa ou VPN, utilizando o encaminhamento forçado.

> [!NOTE]
> Se tiver um servidor proxy que bloqueie o tráfego sem configurações de procuração, não se esqueça de adicionar exceções à conta de armazenamento de artefactos do laboratório.

Também pode utilizar grupos de segurança de rede para máquinas virtuais ou subredes. Este passo adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Porque é que a minha rede virtual não está a poupar corretamente?
Uma possibilidade é que o seu nome de rede virtual contenha períodos. Em caso afirmativo, tente remover os períodos ou substituí-los por hífenes. Então, tente novamente salvar a rede virtual.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Porque é que recebo um erro de "recurso dos pais não encontrado" quando proto um VM da PowerShell?
Quando um recurso é pai de outro recurso, o recurso-mãe deve existir antes de criar o recurso para a criança. Se o recurso-mãe não existir, vê uma mensagem **ParentResourceNotFound.** Se não especificar uma dependência do recurso-mãe, o recurso da criança pode ser implantado antes do progenitor.

VMs são recursos infantis sob um laboratório de um grupo de recursos. Quando utiliza os modelos do Gestor de Recursos para implementar VMs utilizando o PowerShell, o nome do grupo de recursos fornecido no script PowerShell deve ser o nome do grupo de recursos do laboratório. Para mais informações, consulte os erros comuns de implantação do [Azure.](../azure-resource-manager/templates/common-deployment-errors.md)

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações de erro se uma implementação vm falhar?
Os erros de implementação da VM são capturados em registos de atividade. Pode encontrar registos de atividade de VM de laboratório em **registos** de auditoria ou diagnósticos de **máquinas virtuais** no menu de recursos na página VM do laboratório (a página aparece depois de selecionar o VM da lista das minhas máquinas virtuais).

Por vezes, o erro de implantação ocorre antes do início da implantação da VM. Um exemplo é quando o limite de subscrição de um recurso criado com o VM é excedido. Neste caso, os detalhes do erro são capturados nos registos de atividade ao nível do laboratório. Os registos de atividade estão localizados na parte inferior das definições de **Configuração e políticas.** Para obter mais informações sobre a utilização de registos de atividade sintetizá-lo em Azure, consulte os registos de [atividade do View para auditar ações sobre recursos](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Porque é que eu tenho um erro de "localização não está disponível para o tipo de recurso" quando tento criar um laboratório?
Pode ver uma mensagem de erro semelhante à seguinte quando tentar criar um laboratório:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Pode resolver este erro tomando um dos seguintes passos:

#### <a name="option-1"></a>Opção 1
Verifique a disponibilidade do tipo de recursos nas regiões do Azure nos Produtos disponíveis por página da [região.](https://azure.microsoft.com/global-infrastructure/services/) Se o tipo de recursos não estiver disponível numa determinada região, a DevTest Labs não apoia a criação de um laboratório naquela região. Selecione outra região ao criar o seu laboratório.

#### <a name="option-2"></a>Opção 2
Se o tipo de recursos estiver disponível na sua região, verifique se está registado na sua subscrição. Pode ser feito ao nível do proprietário da subscrição, como mostra [este artigo.](../azure-resource-manager/management/resource-providers-and-types.md)
