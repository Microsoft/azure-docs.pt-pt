---
title: Conceitos - Acesso e identidade nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o acesso e identidade no Serviço Azure Kubernetes (AKS), incluindo a integração do Azure Ative Directory, o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC), e papéis e encadernações.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105923"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Access and identity options for Azure Kubernetes Service (AKS) (Opções de acesso e de identidade do Azure Kubernetes Service (AKS))

Você pode autenticar, autorizar, proteger e controlar o acesso aos clusters Kubernetes de várias maneiras. 
* Utilizando o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC), pode conceder aos utilizadores, grupos e contas de serviço acesso apenas aos recursos de que necessitam. 
* Com o Azure Kubernetes Service (AKS), pode melhorar ainda mais a estrutura de segurança e permissões através do Azure Ative Directory e do Azure RBAC. 

Kubernetes RBAC e AKS ajudam-no a garantir o acesso ao cluster e a fornecer apenas as permissões mínimas necessárias aos desenvolvedores e operadores.

Este artigo introduz os conceitos fundamentais que o ajudam a autenticar e atribuir permissões em AKS.

## <a name="aks-service-permissions"></a>Permissões de serviço AKS

Ao criar um cluster, a AKS gera ou modifica recursos de que necessita (como VMs e NICs) para criar e executar o cluster em nome do utilizador. Esta identidade é distinta da permissão de identidade do cluster, que é criada durante a criação do cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Criação e operação de identidade das permissões de cluster

As seguintes permissões são necessárias pela identidade que cria e opera o cluster.

| Permissão | Razão |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Necessário para ler o ID do conjunto de encriptação do disco. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Necessário para atualizar grupos de colocação de proximidade. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessário para configurar os gateways de aplicações e juntar-se à sub-rede. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessário para configurar o Grupo de Segurança da Rede para a sub-rede quando utilizar um VNET personalizado.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Necessário para configurar os IPs públicos de saída no Balanceador de Carga Padrão. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | É necessário criar e atualizar os espaços de trabalho do Log Analytics e a monitorização do Azure para os contentores. |

### <a name="aks-cluster-identity-permissions"></a>Permissões de identidade de cluster AKS

As seguintes permissões são usadas pela identidade do cluster AKS, que é criada e associada ao cluster AKS. Cada permissão é utilizada pelas razões abaixo:

| Permissão | Razão |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Necessário para criar utilizadores e operar o cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Necessário para configurar o balançador de carga para um serviço LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Necessário para encontrar e configurar os IPs públicos para um serviço LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/join/action` | Necessário para configurar os IPs públicos para um serviço LoadBalancer. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | É necessário criar ou eliminar regras de segurança para um serviço LoadBalancer. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Obrigado a configurar AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Necessário para configurar contas de armazenamento para AzureFile ou AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Obrigado a configurar tabelas de rotas e rotas para nós. |
| `Microsoft.Compute/virtualMachines/read` | É necessário encontrar informações para máquinas virtuais num VMAS, tais como zonas, domínio de falhas, tamanho e discos de dados. |
| `Microsoft.Compute/virtualMachines/write` | É necessário ligar AzureDisks a uma máquina virtual num VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | É necessário encontrar informações para máquinas virtuais num conjunto de escala de máquina virtual, tais como zonas, domínio de avarias, tamanho e discos de dados. |
| `Microsoft.Network/networkInterfaces/write` | Necessário adicionar uma máquina virtual num VMAS a um conjunto de endereços de backend de balança de carga. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Necessário para adicionar uma balança de máquina virtual definida a um conjunto de endereços de backend de balança de carga e escalar os nós num conjunto de balança de máquina virtual. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Necessário para prender AzureDisks e adicionar uma máquina virtual de uma balança de máquina virtual definida ao equilibrador de carga. |
| `Microsoft.Network/networkInterfaces/read` | Necessário para pesquisar iPs internos e piscinas de endereços de backend de carregamento para máquinas virtuais em um VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Necessário para pesquisar iPs internos e piscinas de endereços de backend de carregamento para uma máquina virtual em um conjunto de escala de máquina virtual. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | É necessário encontrar iPs públicos para uma máquina virtual num conjunto de escala de máquina virtual. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | É necessário verificar se existe uma sub-rede para o balançador de carga interno noutro grupo de recursos. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Necessário para configurar instantâneos para AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | É necessário encontrar tamanhos de máquinas virtuais para encontrar limites de volume AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Permissões adicionais de identidade do cluster

Ao criar um cluster com atributos específicos, você precisará das seguintes permissões adicionais para a identidade do cluster. Uma vez que estas permissões não são atribuídas automaticamente, deve adicioná-las à identidade do cluster após a sua criação.

| Permissão | Razão |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Necessário se utilizar um grupo de segurança de rede em outro grupo de recursos. Obrigado a configurar regras de segurança para um serviço LoadBalancer. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessário se utilizar uma sub-rede noutro grupo de recursos, como um VNET personalizado. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Necessário se utilizar uma sub-rede associada a uma tabela de rotas em outro grupo de recursos, como um VNET personalizado com uma tabela de rotas personalizada. É necessário verificar se já existe uma sub-rede para a sub-rede do outro grupo de recursos. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Necessário se utilizar um equilibrador de carga interno noutro grupo de recursos. É necessário verificar se já existe uma sub-rede para o balançador de carga interno do grupo de recursos. |
| `Microsoft.Network/privatednszones/*` | Necessário se utilizar uma zona privada de DNS em outro grupo de recursos, como um privateDNSZone personalizado. |

## <a name="kubernetes-rbac"></a>RBAC do Kubernetes

Kubernetes RBAC fornece filtragem granular das ações do utilizador. Com este mecanismo de controlo:
* Atribui permissão a utilizadores ou grupos de utilizadores para criar e modificar recursos ou visualizar registos a partir da execução das cargas de trabalho da aplicação. 
* Você pode estender permissões para um único espaço de nome ou em todo o cluster AKS. 
* Cria *funções* para definir permissões e, em seguida, atribui essas funções aos utilizadores com *encadernações de papéis*.

Para obter mais informações, consulte [a autorização do RBAC da Kubernetes.][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

#### <a name="roles"></a>Funções
Antes de atribuir permissões aos utilizadores com o RBAC de Kubernetes, definirá as permissões do utilizador como uma *Função*. Conceda permissões dentro de um espaço de nome usando funções. 

> [!NOTE]
> As funções de Kubernetes *concedem* permissões; não *negam* permissões.

Para conceder permissões em todo o cluster ou para agrupar recursos fora de um determinado espaço de nome, você pode em vez disso usar *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

Um ClusterRole concede e aplica permissões a recursos em todo o cluster, e não num espaço de nome específico.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Uma vez definidas funções para conceder permissões a recursos, atribua essas permissões de NCC de Kubernetes com um *RoleBinding*. Se o seu cluster AKS [se integrar com o Azure Ative Directory (Azure AD)](#azure-ad-integration), roleBindings concede permissões aos utilizadores AZure AD para realizar ações dentro do cluster. Veja como no [Control acedem aos recursos de cluster utilizando o controlo de acesso baseado em funções da Kubernetes e as identidades do Azure Ative Directory](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Atribua funções aos utilizadores para um determinado espaço de nome utilizando RoleBindings. Com RoleBindings, você logicamente pode segregar um único cluster AKS, permitindo apenas que os utilizadores acedam aos recursos da aplicação no seu espaço de nome atribuído. 

Para ligar papéis em todo o cluster, ou para agrupar recursos fora de um determinado espaço de nome, você em vez disso usa *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Com um ClusterRoleBinding, liga-se aos utilizadores e aplica-se a recursos em todo o cluster, e não num espaço de nome específico. Esta abordagem permite conceder aos administradores ou engenheiros de apoio acesso a todos os recursos do cluster AKS.


> [!NOTE]
> A Microsoft/AKS executa quaisquer ações de cluster com o consentimento do utilizador sob uma função incorporada de Kubernetes `aks-service` e encadernação de funções `aks-service-rolebinding` incorporadas . 
> 
> Esta função permite que a AKS resolva problemas e diagnostice problemas de cluster, mas não pode modificar permissões, criar papéis ou encadernações de papéis, ou outras ações de alto privilégio. O acesso à função só é ativado em bilhetes de apoio ativos com acesso just-in-time (JIT). Leia mais sobre [as políticas de apoio à AKS.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Contas de serviço kubernetes

*As contas* de serviço são um dos principais tipos de utilizadores em Kubernetes. A API de Kubernetes detém e gere contas de serviço. As credenciais de conta de serviço são armazenadas como segredos de Kubernetes, permitindo que sejam usadas por cápsulas autorizadas para comunicar com o Servidor API. A maioria dos pedidos da API fornecem um símbolo de autenticação para uma conta de serviço ou uma conta de utilizador normal.

As contas normais de utilizador permitem um acesso mais tradicional a administradores ou desenvolvedores humanos, e não apenas serviços e processos. Embora a Kubernetes não forneça uma solução de gestão de identidade para armazenar contas e senhas de utilizador regulares, pode integrar soluções de identidade externas em Kubernetes. Para os clusters AKS, esta solução de identidade integrada é a Azure AD.

Para obter mais informações sobre as opções de identidade em Kubernetes, consulte [a autenticação de Kubernetes.][kubernetes-authentication]

## <a name="azure-ad-integration"></a>Integração AD AZure

Melhore a segurança do cluster AKS com a integração AZure AD. Construído com décadas de gestão de identidade empresarial, a Azure AD é um serviço multi-inquilino, diretório baseado na nuvem e serviço de gestão de identidade que combina serviços de diretório principal, gestão de acesso a aplicações e proteção de identidade. Com o Azure AD, pode integrar identidades no local em clusters AKS para fornecer uma única fonte de gestão de conta e segurança.

![Integração do Azure Ative Directory com clusters AKS](media/concepts-identity/aad-integration.png)

Com clusters AKS integrados em Azure, pode conceder aos utilizadores ou grupos acesso aos recursos de Kubernetes dentro de um espaço de nome ou em todo o cluster. 

1. Para obter um `kubectl` contexto de configuração, um utilizador executa o comando [az aks get-credentials.][az-aks-get-credentials] 
1. Quando um utilizador interage com o cluster AKS, `kubectl` é-lhes solicitado que assinem com as suas credenciais AD AZure. 

Esta abordagem fornece uma única fonte para a gestão da conta de utilizador e credenciais de senha. O utilizador só pode aceder aos recursos definidos pelo administrador do cluster.

A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][openid-connect]. A partir do interior do cluster Kubernetes, [a Autenticação Token Webhook][webhook-token-docs] é usada para verificar fichas de autenticação. A autenticação de ficha webhook é configurada e gerida como parte do cluster AKS.

### <a name="webhook-and-api-server"></a>Servidor Webhook e API

![Fluxo de autenticação de servidores Webhook e API](media/concepts-identity/auth-flow.png)

Como mostrado no gráfico acima, o servidor API chama o servidor webhook AKS e executa os seguintes passos:

1. `kubectl` utiliza a aplicação do cliente Azure AD para assinar em utilizadores com o fluxo de autorização do [dispositivo OAuth 2.0](../active-directory/develop/v2-oauth2-device-code.md).
2. A AZure AD fornece um access_token, id_token e um refresh_token.
3. O utilizador faz um pedido `kubectl` com um access_token de `kubeconfig` .
4. `kubectl` envia o access_token para o Servidor API.
5. O Servidor API está configurado com o Auth WebHook Server para realizar validação.
6. O servidor webhook de autenticação confirma que a assinatura JSON Web Token é válida verificando a chave de assinatura pública Azure AD.
7. A aplicação do servidor utiliza credenciais fornecidas pelo utilizador para consultar membros do grupo do utilizador iniciado a partir da API do Gráfico MS.
8. Uma resposta é enviada para o Servidor API com informações do utilizador, tais como a reivindicação principal do utilizador (UPN) do token de acesso e a adesão do grupo ao utilizador com base no ID do objeto.
9. A API executa uma decisão de autorização com base no Papel/RoleBinding da Kubernetes.
10. Uma vez autorizado, o servidor API retorna a `kubectl` .
11. `kubectl` fornece feedback ao utilizador.
 
Saiba como integrar a AKS com a AD Azure com a nossa [integração AZure AD gerida pela AKS como orientar.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

O Azure role-based access control (RBAC) é um sistema de autorização construído no [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso de granulado fino dos recursos Azure.

| Sistema RBAC | Description |
|---|---|
| RBAC do Kubernetes | Projetado para trabalhar em recursos Kubernetes dentro do seu cluster AKS. |
| RBAC do Azure | Projetado para trabalhar em recursos dentro da sua subscrição Azure. |

Com o Azure RBAC, cria-se uma *definição de papel* que descreve as permissões a aplicar. Em seguida, atribua a um utilizador ou agrupe esta definição de função através de uma *atribuição de funções* para um *determinado âmbito*. O âmbito pode ser um recurso individual, um grupo de recursos, ou em toda a subscrição.

Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?][azure-rbac]

Existem dois níveis de acesso necessários para operar plenamente um cluster AKS: 
* [Aceda ao recurso AKS na sua subscrição Azure.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Controlo de dimensionamento ou atualização do seu cluster utilizando as APIs AKS.
  * Puxe o `kubeconfig` seu.
* Acesso à API de Kubernetes. Este acesso é controlado por:
  * [Kubernetes RBAC](#kubernetes-rbac) (tradicionalmente).
  * [Integração do Azure RBAC com AKS para a autorização kubernetes](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC para autorizar o acesso ao recurso AKS

Com o Azure RBAC, pode fornecer aos seus utilizadores (ou identidades) acesso granular aos recursos AKS através de uma ou mais subscrições. Por exemplo, pode utilizar a [função contribuidora de serviçoS Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) para escalar e atualizar o seu cluster. Enquanto isso, outro utilizador com a [função Azure Kubernetes Service Cluster Admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) só tem permissão para puxar o Administrador `kubeconfig` .

Em alternativa, pode dar ao seu utilizador o papel [de Contribuinte](../role-based-access-control/built-in-roles.md#contributor) Geral. Com o papel de Contribuinte Geral, os utilizadores podem executar as permissões acima e todas as ações possíveis no recurso AKS, exceto a gestão de permissões.

[Utilize o Azure RBAC para definir o acesso ao ficheiro de configuração Kubernetes em AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC para autorização kubernetes (pré-visualização)

Com a integração do Azure RBAC, a AKS utilizará um servidor webhook de autorização kubernetes para que possa gerir permissões e atribuições de recursos de cluster de Azure AD integrados em Ad, utilizando definições de funções Azure e atribuições de funções.

![Azure RBAC para o fluxo de autorização de Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Como mostrado no diagrama acima, ao utilizar a integração Azure RBAC, todos os pedidos à API de Kubernetes seguirão o mesmo fluxo de autenticação que explicado na secção de integração do [Azure Ative Directory](#azure-ad-integration). 

Se a identidade que faz o pedido existir no Azure AD, a Azure irá equipar-se com a Kubernetes RBAC para autorizar o pedido. Se a identidade existir fora da Azure AD (ou seja, uma conta de serviço Kubernetes), a autorização irá dissuadir o RBAC normal de Kubernetes.

Neste cenário, você usa mecanismos e APIs Azure RBAC para atribuir aos utilizadores funções incorporadas ou criar papéis personalizados, tal como faria com os papéis de Kubernetes. 

Com esta funcionalidade, não só dá permissão aos utilizadores para o recurso AKS através de subscrições, como também configura o papel e permissões para dentro de cada um desses clusters que controlam o acesso à API da Kubernetes. Por exemplo, pode conceder o `Azure Kubernetes Service RBAC Viewer` papel no âmbito de subscrição. O destinatário da função será capaz de listar e obter todos os objetos Kubernetes de todos os clusters sem modificá-los.

> [!IMPORTANT]
> É necessário ativar o Azure RBAC para a autorização de Kubernetes antes de utilizar esta funcionalidade. Para mais detalhes e orientação passo a passo, siga o nosso [Use Azure RBAC para a Autorização kubernetes](manage-azure-rbac.md) como orientar.

#### <a name="built-in-roles"></a>Funções incorporadas

A AKS fornece os seguintes quatro papéis incorporados. São semelhantes aos [papéis incorporados de Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) com algumas diferenças, como apoiar CRDs. Consulte a lista completa de ações permitidas por cada [papel incorporado do Azure.](../role-based-access-control/built-in-roles.md)

| Função                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Viewer  | Permite o acesso apenas à leitura para ver a maioria dos objetos num espaço de nome. <br> Não permite visualizar papéis ou encadernações de papéis.<br> Não permite `Secrets` visualização. A leitura dos `Secrets` conteúdos permite o acesso a `ServiceAccount` credenciais no espaço de nomes, o que permitiria o acesso da API como qualquer um `ServiceAccount` no espaço de nomes (uma forma de escalada de privilégio).  |
| Azure Kubernetes Service RBAC Writer | Permite ler/escrever o acesso à maioria dos objetos num espaço de nome. <br> Não permite visualizar ou modificar papéis, ou encadernações de papéis. <br> Permite aceder `Secrets` e executar cápsulas como qualquer ServiceAccount no espaço de nomes, para que possa ser usado para obter os níveis de acesso API de qualquer ServiceAccount no espaço de nomes. |
| Azure Kubernetes Serviço RBAC Administrador  | Permite o acesso a administrador, destinado a ser concedido dentro de um espaço de nome. <br> Permite o acesso de leitura/escrita à maioria dos recursos num espaço de nome (ou âmbito de cluster), incluindo a capacidade de criar papéis e encadernações de papéis dentro do espaço de nomes. <br> Não permite escrever acesso à quota de recursos ou ao próprio espaço de nome. |
| Azure Kubernetes Serviço RBAC Cluster Admin  | Permite o acesso do super-utilizador para realizar qualquer ação em qualquer recurso. <br> Dá total controlo sobre todos os recursos do cluster e em todos os espaços de nome. |


## <a name="summary"></a>Resumo

Consulte a tabela para um resumo rápido de como os utilizadores podem autenticar em Kubernetes quando a integração AD do Azure estiver ativada. Em todos os casos, a sequência de comandos do utilizador é:
1. Corra `az login` para autenticar até Azure.
1. Corra `az aks get-credentials` para descarregar credenciais para o cluster em `.kube/config` .
1. Executar `kubectl` comandos. 
   * O primeiro comando pode desencadear a autenticação baseada no navegador para autenticar o cluster, conforme descrito na tabela seguinte.

No portal Azure, pode encontrar:
* A *Subvenção de Papel* (concessão de função Azure RBAC) referida na segunda coluna é mostrada no **separador Controlo de Acesso.** 
* O Grupo Admin Adure Cluster é apresentado no **separador Configuração.**
  * Também encontrado com nome de parâmetro `--aad-admin-group-object-ids` no Azure CLI.


| Description        | Subvenção de papel exigida| Grupo de administrador azure Ad cluster | Quando utilizar |
| -------------------|------------|----------------------------|-------------|
| Login de administração legado usando certificado de cliente| **Azure Kubernetes Admin Role**. Esta função permite `az aks get-credentials` ser utilizada com a `--admin` bandeira, que descarrega um [certificado de administração de cluster legacy (não-Azure AD)](control-kubeconfig-access.md) no do utilizador `.kube/config` . Este é o único propósito de "Azure Kubernetes Admin Role".|n/a|Se estiver permanentemente bloqueado por não ter acesso a um grupo AZure AD válido com acesso ao seu cluster.| 
| Azure AD com rolebindings manuais (Cluster)| **Papel de utilizador de Azure Kubernetes**. A função "Utilizador" permite `az aks get-credentials` ser utilizada sem a `--admin` bandeira. (Este é o único propósito de "Azure Kubernetes User Role".) O resultado, num cluster AD ativado pelo Azure, é o download de [uma entrada vazia](control-kubeconfig-access.md) em , que despoleta a `.kube/config` autenticação baseada no navegador quando é usada pela primeira vez por `kubectl` .| O utilizador não está em nenhum destes grupos. Como o utilizador não está em nenhum grupo de Administração cluster, os seus direitos serão controlados inteiramente por quaisquer RoleBindings ou ClusterRoleBindings que tenham sido criados por administradores de cluster. Os (Cluster)RoleBindings [nomeiam os utilizadores AD do Azure ou os grupos AD Azure](azure-ad-rbac.md) como seus `subjects` . Se não tiverem sido criadas tais ligações, o utilizador não poderá excortar quaisquer `kubectl` comandos.|Se quer um controlo de acesso fino e não está a usar o Azure RBAC para a Autorização Kubernetes. Note que o utilizador que configura as ligações deve iniciar sessão por um dos outros métodos listados nesta tabela.|
| Azure AD por membro do grupo de administração| Mesmo que acima|O utilizador é membro de um dos grupos listados aqui. AKS gera automaticamente um ClusterRoleBinding que liga todos os grupos listados ao `cluster-admin` papel de Kubernetes. Para que os utilizadores destes grupos possam executar todos os `kubectl` comandos como `cluster-admin` .|Se quiser conceder convenientemente aos utilizadores plenos direitos de administração, e _não_ estiver a utilizar o RBAC Azure para a autorização da Kubernetes.|
| Azure AD com Azure RBAC para autorização Kubernetes|Dois papéis: <br> Primeiro, **Azure Kubernetes User Role** (como acima). <br> Segundo, um dos "Azure Kubernetes Service **RBAC..."** papéis listados acima, ou a sua própria alternativa personalizada.|O campo de funções de administração no separador Configuração é irrelevante quando o RBAC de Azure para a Autorização de Kubernetes está ativado.|Está a usar o Azure RBAC para autorização kubernetes. Esta abordagem dá-lhe um controlo fino, sem a necessidade de configurar RoleBindings ou ClusterRoleBindings.|

## <a name="next-steps"></a>Passos seguintes

- Para começar com Azure AD e Kubernetes RBAC, consulte [Integrate Azure Ative Directory com AKS][aks-aad].
- Para as melhores práticas associadas, consulte [as melhores práticas de autenticação e autorização em AKS][operator-best-practices-identity].
- Para começar com o Azure RBAC para autorização kubernetes, consulte [Use Azure RBAC para autorizar o acesso dentro do Cluster Azure Kubernetes Service (AKS).](manage-azure-rbac.md)
- Para começar a proteger o seu `kubeconfig` ficheiro, consulte [limite de acesso ao ficheiro de configuração do cluster](control-kubeconfig-access.md)

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
