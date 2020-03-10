---
title: Inicie sessão num Linux VM com credenciais de Diretório Ativo Azure
description: Aprenda a criar e configurar um VM Linux para iniciar sessão utilizando a autenticação do Diretório Ativo Azure.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: eb303ecb5657e9312445093841cfa6c501efda18
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944803"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Pré-visualização: Inicie sessão numa máquina virtual Linux em Azure utilizando autenticação de Diretório Ativo Azure

Para melhorar a segurança das máquinas virtuais Linux (VMs) em Azure, pode integrar-se com a autenticação do Azure Ative Directory (AD). Quando utiliza a autenticação Azure AD para VMs Linux, controla e aplica políticas que permitem ou negam o acesso aos VMs. Este artigo mostra-lhe como criar e configurar um VM Linux para usar a autenticação Azure AD.


> [!IMPORTANT]
> A autenticação do Diretório Ativo Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Utilize esta função numa máquina virtual de teste que espera descartar após os testes.
>


Existem muitos benefícios em usar a autenticação Azure AD para iniciar sessão nos VMs Linux em Azure, incluindo:

- **Segurança melhorada:**
  - Pode usar as suas credenciais de AD corporativas para iniciar sessão nos VMs do Azure Linux. Não há necessidade de criar contas de administrador local e gerir a vida útil da credencial.
  - Ao reduzir a sua dependência das contas do administrador local, não precisa de se preocupar com perdas/roubos credenciais, utilizadores que configuram credenciais fracas, etc.
  - A complexidade da palavra-passe e as políticas de vida de palavra-passe configuradas para o seu diretório Azure AD ajudam a proteger também os VMs linux.
  - Para garantir o login às máquinas virtuais Azure, pode configurar a autenticação de vários fatores.
  - A capacidade de iniciar sessão nos VMs linux com o Azure Ative Directory também funciona para clientes que utilizam [os Serviços da Federação.](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

- **Colaboração sem emenda:** Com o Controlo de Acesso Baseado em Funções (RBAC), pode especificar quem pode iniciar sessão num dado VM como utilizador regular ou com privilégios de administrador. Quando os utilizadores se juntarem ou saírem da sua equipa, pode atualizar a política RBAC para o VM conceder acesso conforme apropriado. Esta experiência é muito mais simples do que ter de esfregar VMs para remover chaves públicas desnecessárias do SSH. Quando os colaboradores deixam a sua organização e a sua conta de utilizador é desativada ou removida da Azure AD, já não têm acesso aos seus recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões apoiadas de Azure e distribuição linux

As seguintes distribuições linux são atualmente suportadas durante a pré-visualização desta funcionalidade:

| Distribuição | Versão |
| --- | --- |
| CentOS | Centos 6, Centos 7 |
| Debian | Debian 9 |
| openSUSE | abrir Salto SUSE 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 e Ubuntu Server 18.04 |


As seguintes regiões azure são atualmente apoiadas durante a pré-visualização desta funcionalidade:

- Todas as regiões globais de Azure

>[!IMPORTANT]
> Para utilizar esta função de pré-visualização, apenas implemente um distro Linux apoiado e numa região azure apoiada. A característica não é apoiada no Governo de Azure ou em nuvens soberanas.


Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.31 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Requisitos de rede

Para ativar a autenticação AD Azure para os seus VMs Linux em Azure, tem de garantir que a configuração da rede VMs permite o acesso de saída aos seguintes pontos finais sobre a porta TCP 443:

* https:\//login.microsoftonline.com
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Atualmente, os grupos de segurança da rede Azure não podem ser configurados para VMs ativados com autenticação AD Azure.

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Criar um grupo de recursos com grupo [Az criar,](/cli/azure/group#az-group-create)em seguida, criar um VM com [az vm criar](/cli/azure/vm#az-vm-create) usando um distro apoiado e em uma região apoiada. O exemplo seguinte implementa um VM chamado *myVM* que usa *Ubuntu 16.04 LTS* num grupo de recursos chamado *myResourceGroup* na região *sudoeste.* Nos seguintes exemplos, pode fornecer os nomes do seu próprio grupo de recursos e VM, conforme necessário.

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
> Se implementar esta extensão a um VM previamente criado, certifique-se de que a máquina tem pelo menos 1GB de memória atribuída, a extensão não será instalada.

Para iniciar sessão num VM Linux com credenciais Azure AD, instale a extensão VM de login do Diretório Ativo Azure. As extensões VM são pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em máquinas virtuais Azure. Utilize um conjunto de [extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão *AADLoginForLinux* no VM denominado *myVM* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O estado de *provisionamento* do *Sucesso* é mostrado uma vez que a extensão é instalada com sucesso no VM. O VM precisa de um agente VM em execução para instalar a extensão. Para mais informações, consulte a visão geral do [Agente VM](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de funções para o VM

A política de Controlo de Acesso baseado em Funções Azure (RBAC) determina quem pode iniciar sessão no VM. Duas funções RBAC são usadas para autorizar o login VM:

- **Login**do Administrador da Máquina Virtual : Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios de utilizador raiz do Windows Administrator ou Linux.
- **Login**de utilizador de máquina virtual : Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios regulares do utilizador.

> [!NOTE]
> Para permitir que um utilizador inicie sessão no VM através de SSH, deve atribuir o *Login* do Administrador da Máquina Virtual ou a função de Login de Utilizador de *Máquina Virtual.* Um utilizador Azure com as funções *de Proprietário* ou *Colaborador* atribuídas a um VM não tem automaticamente privilégios de iniciar sessão no VM sobre SSH.

O exemplo seguinte utiliza a [atribuição de funções az para](/cli/azure/role/assignment#az-role-assignment-create) atribuir a função de *Login* do Administrador de Máquina Virtual ao VM para o seu utilizador atual Do IA. O nome de utilizador da sua conta Ative Azure é obtido com [a conta az show](/cli/azure/account#az-account-show), e o *âmbito* é definido para o VM criado em um passo anterior com [az vm show](/cli/azure/vm#az-vm-show). O âmbito também pode ser atribuído a um grupo de recursos ou a nível de subscrição, e aplicam-se permissões normais de herança RBAC. Para mais informações, consulte [Controlos de Acesso Baseados em Funções](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o seu domínio AAD e o seu domínio de nome de utilizador de logon não corresponderem, deve especificar o ID do objeto da sua conta de utilizador com o *--designado-objecto-id*, e não apenas o nome de utilizador para *--designado*. Pode obter o ID do objeto para a sua conta de utilizador com [a lista de utilizadores az ad](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como utilizar o RBAC para gerir o acesso aos seus recursos de subscrição Azure, consulte a utilização do portal [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [Azure](../../role-based-access-control/role-assignments-portal.md)ou [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Também pode configurar o Azure AD para exigir a autenticação de vários fatores para um utilizador específico iniciar sessão na máquina virtual Linux. Para mais informações, consulte [Iniciar com autenticação Azure Multi-Factor na nuvem](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Inicie sessão na máquina virtual Linux

Em primeiro lugar, veja o endereço IP público do seu VM com [o az vm show:](/cli/azure/vm#az-vm-show)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Inicie sessão na máquina virtual Azure Linux utilizando as suas credenciais De AD Azure. O parâmetro `-l` permite especificar o seu próprio endereço de conta Azure AD. Substitua a conta de exemplo pela sua. Os endereços da conta devem ser introduzidos em todas as minúsculas. Substitua o endereço IP do exemplo pelo endereço IP público do seu VM do comando anterior.

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

É solicitado que aceda ao Azure AD com um código de utilização único em [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin). Copie e cole o código de utilização único na página de login do dispositivo.

Quando solicitado, introduza as credenciais de login do Azure AD na página de login. 

A seguinte mensagem é mostrada no navegador da Web quando tiver autenticado com sucesso: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Feche a janela do navegador, volte ao ssh e prima a tecla **Enter.** 

Está agora inscrito na máquina virtual Azure Linux com as permissões de funções atribuídas, tais como *Utilizador VM* ou *Administrador VM*. Se a sua conta de utilizador for atribuída a função de Login do Administrador de *Máquina Virtual,* pode utilizar `sudo` para executar comandos que requerem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Sessão sudo e AAD

A primeira vez que correr sudo, será convidado a autenticar uma segunda vez. Se não quiser ter de autenticar novamente para correr sudo, pode editar o seu ficheiro sudoers `/etc/sudoers.d/aad_admins` e substituir esta linha:

```bash
%aad_admins ALL=(ALL) ALL
```
com esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Problemas de sessão de problemas

Alguns erros comuns quando se tenta ssh com credenciais De AD Azure não incluem funções RBAC atribuídas, e solicitações repetidas para iniciar sessão. Utilize as seguintes secções para corrigir estas questões.

### <a name="access-denied-rbac-role-not-assigned"></a>Acesso negado: Função RBAC não atribuída

Se vir o seguinte erro no seu pedido SSH, verifique se configuraas as políticas rBAC para o VM que concede ao utilizador o *Login* do Administrador da Máquina Virtual ou a função de Login do Utilizador da *Máquina Virtual:*

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Continuação dos pedidos de inscrição em SSH

Se completar com sucesso o passo de autenticação num navegador web, poderá ser imediatamente solicitado a iniciar sessão com um novo código. Este erro é normalmente causado por um desfasamento entre o nome de entrada especificado no ssh e a conta com que assinou no Azure AD. Para corrigir esta questão:

- Verifique se o nome de início de sessão especificado no aviso SSH está correto. Um erro no nome de entrada pode causar uma incompatibilidade entre o nome de entrada especificado no pedido ssh e a conta com que assinou no Azure AD. Por exemplo, escreveu *\@contoso.onmicrosoft.com* em vez de *azureuser\@contoso.onmicrosoft.com*.
- Se tiver várias contas de utilizador, certifique-se de que não fornece uma conta de utilizador diferente na janela do navegador ao iniciar sessão no Azure AD.
- Linux é um sistema operativo sensível a casos. Há uma diferença entre "Azureuser@contoso.onmicrosoft.com" e 'azureuser@contoso.onmicrosoft.com', que pode causar um desfasamento. Certifique-se de que especifica a UPN com a sensibilidade de caso correta no aviso SSH.

### <a name="other-limitations"></a>Outras limitações

Os utilizadores que herdam direitos de acesso através de grupos aninhados ou atribuições de papéis não são atualmente suportados. O utilizador ou grupo deve ser diretamente atribuído às atribuições de [funções necessárias](#configure-role-assignments-for-the-vm). Por exemplo, o uso de grupos de gestão ou atribuições de papéis de grupo aninhados não concederá as permissões corretas para permitir que o utilizador assine.

## <a name="preview-feedback"></a>Feedback de pré-visualização

Partilhe o seu feedback sobre esta funcionalidade de pré-visualização ou reporte problemas que o utilizam no fórum de [feedback da AD Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre o Diretório Ativo azure, consulte [o What is Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md)
