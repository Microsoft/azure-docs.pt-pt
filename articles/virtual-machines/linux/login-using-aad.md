---
title: Faça login num Linux VM com credenciais de Diretório Ativo Azure
description: Aprenda a criar e configurar um Linux VM para iniciar sessão com a autenticação do Azure Ative Directory.
author: SanDeo-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: sandeo
ms.openlocfilehash: 882df9d0dcb01d6321455b845fed087a5e14ccc6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016170"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Pré-visualização: Inicie sessão numa máquina virtual Linux em Azure utilizando a autenticação do Azure Ative Directory

Para melhorar a segurança das máquinas virtuais Linux (VMs) em Azure, pode integrar-se com a autenticação do Azure Ative Directory (AD). Quando utiliza a autenticação AZure AD para Os VMs Linux, controla centralmente e aplica políticas que permitem ou negam o acesso aos VMs. Este artigo mostra-lhe como criar e configurar um Linux VM para usar a autenticação AD AZure.


> [!IMPORTANT]
> A autenticação do Azure Ative Directory está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Utilize esta funcionalidade numa máquina virtual de teste que espera descartar após o teste.
>


Existem muitos benefícios de usar a autenticação AZure AD para iniciar sessão em Linux VMs em Azure, incluindo:

- **Segurança melhorada:**
  - Pode utilizar as suas credenciais de AD corporativas para iniciar sessão nos VMs Azure Linux. Não há necessidade de criar contas de administrador local e gerir a vida útil das credenciais.
  - Ao reduzir a sua dependência das contas de administrador local, não precisa de se preocupar com a perda/roubo de credenciais, com os utilizadores a configurarem credenciais fracas, etc.
  - A complexidade da palavra-passe e as políticas de vida de senha configuradas para o seu diretório AD Azure ajudam a proteger os VMs do Linux também.
  - Para garantir o início de sessão nas máquinas virtuais Azure, pode configurar a autenticação de vários fatores.
  - A capacidade de iniciar sessão em VMs Linux com Azure Ative Directory também funciona para clientes que usam [serviços da Federação.](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

- **Colaboração sem emenda:** Com o controlo de acesso baseado em funções Azure (Azure RBAC), pode especificar quem pode iniciar sação num determinado VM como utilizador regular ou com privilégios de administrador. Quando os utilizadores se juntam ou deixam a sua equipa, pode atualizar a política do Azure RBAC para que o VM conceda acesso conforme apropriado. Esta experiência é muito mais simples do que ter de esfregar VMs para remover chaves públicas desnecessárias do SSH. Quando os colaboradores deixam a sua organização e a sua conta de utilizador é desativada ou removida da Azure AD, já não têm acesso aos seus recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões de Azure apoiadas e distribuição de Linux

As seguintes distribuições linux são atualmente suportadas durante a pré-visualização desta funcionalidade:

| Distribuição | Versão |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 e Ubuntu Server 18.04 |


As seguintes regiões de Azure são atualmente apoiadas durante a pré-visualização desta funcionalidade:

- Todas as regiões globais de Azure

>[!IMPORTANT]
> Para utilizar esta funcionalidade de pré-visualização, apenas implemente um destro Linux suportado e numa região de Azure suportada. A característica não é apoiada no Governo Azure ou em nuvens soberanas.
>
> Não é suportado para usar esta extensão em clusters Azure Kubernetes Service (AKS). Para obter mais informações, consulte [as políticas de suporte para AKS](../../aks/support-policies.md).


Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.31 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Requisitos de rede

Para ativar a autenticação Azure AD para os seus VMs Linux em Azure, tem de garantir que a configuração da rede VMs permite o acesso de saída aos seguintes pontos finais sobre a porta TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> Atualmente, os grupos de segurança da rede Azure não podem ser configurados para VMs habilitados com autenticação AD AZure.

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um grupo de recursos com [o grupo AZ criar,](/cli/azure/group#az-group-create)em seguida, criar um VM com [az vm criar](/cli/azure/vm#az-vm-create) usando um distro suportado e em uma região apoiada. O exemplo a seguir implementa um VM chamado *myVM* que utiliza *Ubuntu 16.04 LTS* num grupo de recursos chamado *myResourceGroup* na região *sulcentralus.* Nos seguintes exemplos, pode fornecer os nomes do seu próprio grupo de recursos e VM, conforme necessário.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instale a extensão VM de login Azure AD

> [!NOTE]
> Se implementar esta extensão para um VM previamente criado, certifique-se de que a máquina tem pelo menos 1GB de memória atribuída mais que a extensão deixará de instalar

Para iniciar sessão num Linux VM com credenciais AZure AD, instale a extensão VM de login do Azure Ative. As extensões VM são pequenas aplicações que fornecem tarefas de configuração e automatização pós-implantação em máquinas virtuais Azure. Utilize [o conjunto de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão *AADLoginForLinux* no VM nomeado *myVM* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O *Estado de Provisionamento* de *Sucesso* é mostrado uma vez que a extensão é instalada com sucesso no VM. O VM precisa de um agente VM em execução para instalar a extensão. Para mais informações, consulte [a visão geral do agente VM.](../extensions/agent-windows.md)

## <a name="configure-role-assignments-for-the-vm"></a>Configure atribuições de funções para o VM

A política de controlo de acesso baseado em funções (Azure RBAC) determina quem pode iniciar sessão no VM. Duas funções Azure são usadas para autorizar o login em VM:

- **Início de entrada do Administrador de Máquina Virtual**: Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios do Windows Administrator ou linux root user privileges.
- **Início de Sessão do Utilizador de Máquinas Virtuais**: Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios regulares do utilizador.

> [!NOTE]
> Para permitir que um utilizador faça login no VM em vez de SSH, tem de atribuir o *Login do Administrador de Máquina Virtual* ou a função de Login do Utilizador de Máquina *Virtual.* Um utilizador Azure com as funções *Proprietário* ou *Contribuinte* atribuídos a um VM não tem automaticamente privilégios de iniciar sessão no VM sobre SSH.

O exemplo a seguir utiliza [a atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função *de Login do Administrador de Máquina Virtual* ao VM para o seu utilizador Azure atual. O nome de utilizador da sua conta Azure ativa é obtido com [a conta AZ](/cli/azure/account#az-account-show), e o *âmbito* é definido para o VM criado em passo anterior com [a az vm show](/cli/azure/vm#az-vm-show). O âmbito também poderia ser atribuído a um grupo de recursos ou nível de subscrição, e as permissões de herança normal do Azure RBAC aplicam-se. Para mais informações, consulte [a Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o seu domínio AAD e o nome de utilizador do logon não coincidirem, tem de especificar o ID do objeto da sua conta de utilizador com o *id de objecto-destinatário*, e não apenas o nome de utilizador para *--designado*. Pode obter o ID do objeto para a sua conta de utilizador com [a lista de utilizadores de anúncios az](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como utilizar o Azure RBAC para gerir o acesso aos seus recursos de subscrição Azure, consulte utilizando o [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [portal Azure](../../role-based-access-control/role-assignments-portal.md)ou [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Também pode configurar a Azure AD para exigir a autenticação de vários fatores para um utilizador específico iniciar sedutação na máquina virtual Linux. Para obter mais informações, consulte [Começar com a autenticação multi-factor Azure AD na nuvem.](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="log-in-to-the-linux-virtual-machine"></a>Faça login na máquina virtual Linux

Em primeiro lugar, consulte o endereço IP público do seu VM com [a az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Faça login na máquina virtual Azure Linux usando as suas credenciais AZure AD. O `-l` parâmetro permite especificar o seu próprio endereço de conta Azure AD. Substitua a conta de exemplo pela sua. Os endereços da conta devem ser introduzidos em todas as minúsculas. Substitua o endereço IP exemplo pelo endereço IP público do seu VM do comando anterior.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

É-lhe pedido que entre em Azure AD com um código de utilização único em [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Copie e cole o código de utilização de uma vez na página de início de sessão do dispositivo.

Quando solicitado, insira as suas credenciais de login AZure AD na página de login. 

A seguinte mensagem é mostrada no navegador web quando tiver autenticado com sucesso: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Feche a janela do navegador, volte ao pedido SSH e pressione a tecla **Enter.** 

Está agora inscrito na máquina virtual Azure Linux com as permissões de função atribuídas, tais como *VM User* ou *VM Administrator*. Se a sua conta de utilizador for atribuída à função *de Login do Administrador de Máquina Virtual,* pode utilizar para executar `sudo` comandos que requerem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Login de Sudo e AAD

A primeira vez que correr sudo, será solicitado a autenticar uma segunda vez. Se não quiser ter de autenticar novamente para executar o sudo, pode editar o ficheiro dos seus sudoers `/etc/sudoers.d/aad_admins` e substituir esta linha:

```bash
%aad_admins ALL=(ALL) ALL
```

Com esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Problemas de resolução de problemas

Alguns erros comuns quando tenta sSH com credenciais AD Azure não incluem nenhuma função Azure atribuída, e repetidos pedidos para iniciar sessão. Utilize as seguintes secções para corrigir estas questões.

### <a name="access-denied-azure-role-not-assigned"></a>Acesso negado: Função Azure não atribuída

Se vir o seguinte erro na sua solicitação SSH, verifique se configura as políticas Azure RBAC para o VM que concede ao utilizador o *Login do Administrador de Máquina Virtual* ou a função de Login do Utilizador de Máquina *Virtual:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Continuação das solicitações de inscrição do SSH

Se completar com sucesso o passo de autenticação num browser web, poderá ser imediatamente solicitado que volte a iniciar súm. Este erro é normalmente causado por uma incompatibilidade entre o nome de inscrição especificado na solicitação SSH e a conta com a qual inscreveu no AZure AD. Para corrigir esta questão:

- Verifique se o nome de inscrição especificado na decisão do SSH está correto. Um erro no nome de inscrição pode causar um desfasamento entre o nome de inscrição especificado no pedido de SSH e a conta com a qual inscreveu no Azure AD. Por exemplo, você *dactilografou azuresuer \@ contoso.onmicrosoft.com* em vez de *azureuser \@ contoso.onmicrosoft.com*.
- Se tiver várias contas de utilizador, certifique-se de que não fornece uma conta de utilizador diferente na janela do navegador ao iniciar sessão no Azure AD.
- Linux é um sistema operativo sensível a casos. Há uma diferença entre Azureuser@contoso.onmicrosoft.com ' e azureuser@contoso.onmicrosoft.com '' ', o que pode causar um desfasamento. Certifique-se de que especifica a UPN com a sensibilidade correta ao caso na decisão SSH.

### <a name="other-limitations"></a>Outras limitações

Os utilizadores que herdam os direitos de acesso através de grupos aninhados ou atribuições de funções não são suportados atualmente. O utilizador ou grupo deve ser diretamente designado para as [atribuições de funções necessárias](#configure-role-assignments-for-the-vm). Por exemplo, a utilização de grupos de gestão ou atribuições de funções de grupo aninhadas não concederá as permissões corretas para permitir que o utilizador entre.

## <a name="preview-feedback"></a>Comentários sobre a pré-visualização

Partilhe o seu feedback sobre este recurso de pré-visualização ou reporte problemas que o utilizem no fórum de feedback da [AD Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Diretório Ativo Azure, consulte [o que é o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-whatis.md)
