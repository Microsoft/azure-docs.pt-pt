---
title: Iniciar sessão a uma VM do Linux com as credenciais do Azure Active Directory | Documentos da Microsoft
description: Este foquei, saiba como criar e configurar uma VM do Linux para utilizar a autenticação do Azure Active Directory para inícios de sessão do utilizador
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: cbe9e5c734708701828b2b70f062d88c960dc224
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432690"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Inicie sessão na máquina virtual Linux no Azure utilizando a autenticação do Azure Active Directory (pré-visualização)

Para melhorar a segurança de máquinas de virtuais do Linux (VMs) no Azure, pode integrar com a autenticação do Azure Active Directory (AD). Quando utiliza a autenticação do Azure AD para VMs do Linux, centralmente controlar e impor políticas que permitem ou negam o acesso às VMs. Este artigo mostra-lhe como criar e configurar uma VM do Linux utilizar autenticação do Azure AD.

> [!NOTE]
> Esta funcionalidade está em pré-visualização e não é recomendada para utilização com máquinas virtuais de produção ou cargas de trabalho. Utilize esta funcionalidade numa máquina virtual de teste que pretende eliminar depois de teste.

Há muitos benefícios de usar a autenticação do Azure AD para iniciar sessão VMs do Linux no Azure, incluindo:

- **Segurança melhorada:**
  - Pode utilizar as credenciais empresariais do AD para iniciar sessão para VMs Linux do Azure. Não é necessário para criar contas de administrador local e gerir o ciclo de vida de credencial.
  - Ao reduzir a dependência em contas de administrador local, não é necessário se preocupar sobre perda/roubo de credenciais, os utilizadores que configurar credenciais fracas etc.
  - A complexidade de palavra-passe e as políticas de tempo de vida de palavra-passe configuradas para o diretório do Azure AD ajudam a proteger VMs do Linux também.
  - Para o início de sessão mais seguro para máquinas virtuais do Azure, pode configurar a autenticação multifator.
  - A capacidade de iniciar sessão para VMs do Linux no Azure Active Directory também funciona para os clientes que utilizam [serviços de Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Colaboração direta:** Com baseada em funções controlo de acesso (RBAC), pode especificar quem pode iniciar sessão determinada VM como um usuário normal ou com privilégios de administrador. Quando os utilizadores aderir ou sair de sua equipe, é possível atualizar a política RBAC para a VM conceder acesso conforme apropriado. Esta experiência é muito simples do que que limpar as VMs para remover as de chaves públicas SSH desnecessárias. Quando os funcionários deixam a sua organização e a respetiva conta de utilizador é desabilitada ou removida do Azure AD, deixam de ter acesso aos seus recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões do Azure suportadas e distribuições do Linux

As seguintes distribuições de Linux são atualmente suportadas durante a pré-visualização desta funcionalidade:

| Distribuição | Versão |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04, and Ubuntu Server 18.04 |


As seguintes regiões do Azure atualmente são suportadas durante a pré-visualização desta funcionalidade:

- Todas as regiões globais do Azure

>[!IMPORTANT]
> Para utilizar esta funcionalidade de pré-visualização, apenas implantar uma distribuição Linux suportada e, numa região do Azure suportada. A funcionalidade não é suportada no Azure Government ou clouds soberanas.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.31 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Criar uma Máquina Virtual do Linux

Criar um grupo de recursos com [criar grupo az](/cli/azure/group#az-group-create), em seguida, crie uma VM com [az vm criar](/cli/azure/vm#az-vm-create) usando uma distribuição suportada e numa região suportada. O exemplo seguinte implementa uma VM com o nome *myVM* que utiliza *Ubuntu 16.04 LTS* para um grupo de recursos com o nome *myResourceGroup* no *southcentralus*  região. Nos exemplos a seguir, pode fornecer seu próprio grupo de recursos e os nomes VM conforme necessário.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalar o início de sessão do Azure AD a extensão de VM

Para iniciar sessão a uma VM do Linux com credenciais do Azure AD, instale o registo do Azure Active Directory na extensão VM. Extensões de VM são pequenos aplicativos que fornecem as tarefas de automatização e configuração de pós-implementação em máquinas virtuais do Azure. Uso [conjunto de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar o *AADLoginForLinux* extensão na VM com o nome *myVM* no *myResourceGroup* recursos grupo:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O *provisioningState* dos *Succeeded* é mostrada uma vez que a extensão é instalada na VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função de VM

Política de controlo de acesso baseado em funções (RBAC) do Azure determina quem pode iniciar sessão na VM. Duas funções RBAC são utilizadas para autorizar o início de sessão da VM:

- **Início de sessão de administrador de máquinas virtuais**: Os utilizadores com esta função atribuída, podem iniciar sessão a uma máquina virtual do Azure com o administrador do Windows ou privilégios de utilizador de raiz do Linux.
- **Início de sessão de utilizador de máquina virtual**: Os utilizadores com esta função atribuída, podem iniciar sessão a uma máquina virtual do Azure com privilégios de utilizador normais.

> [!NOTE]
> Para permitir que um utilizador inicie sessão VM através de SSH, deve atribuir a *início de sessão de administrador do Virtual Machine* ou *início de sessão de utilizador de Máquina Virtual* função. Um utilizador do Azure com o *proprietário* ou *contribuinte* funções atribuídas para uma VM automaticamente não tem privilégios para iniciar sessão VM através de SSH.

O exemplo seguinte utiliza [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a *início de sessão de administrador de máquinas virtuais* função para a VM para o seu utilizador do Azure atual. O nome de utilizador da sua conta do Azure Active Directory é obtido com [show de conta de az](/cli/azure/account#az-account-show)e o *âmbito* está definida para a VM criada no passo anterior com [show de vm de az](/cli/azure/vm#az-vm-show). O âmbito também poderia ser atribuído a um nível de subscrição ou grupo de recursos e aplicam as permissões de herança de RBAC normais. Para obter mais informações, consulte [controlos de acesso baseado em funções](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o seu domínio do AAD e o domínio do nome de utilizador de início de sessão não corresponderem, tem de especificar o ID de objeto da sua conta de utilizador com o *– o id de objeto detentor*, não apenas o nome de utilizador para *– detentor*. Pode obter o ID de objeto para a sua conta de utilizador com [lista de utilizadores do ad az](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como utilizar o RBAC para gerir o acesso aos recursos da sua subscrição do Azure, veja a utilizar o [CLI do Azure](../../role-based-access-control/role-assignments-cli.md), [portal do Azure](../../role-based-access-control/role-assignments-portal.md), ou [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Também pode configurar o Azure AD para exigir autenticação multifator para um utilizador específico iniciar sessão na máquina virtual do Linux. Para obter mais informações, consulte [introdução à Azure multi-factor Authentication na cloud](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Inicie sessão na máquina virtual do Linux

Em primeiro lugar, ver o endereço IP público da VM com [show de vm de az](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Inicie sessão na máquina virtual Linux do Azure com as suas credenciais do Azure AD. O `-l` parâmetro permite-lhe especificar o seu endereço de conta do Azure AD. Endereços de conta devem ser inseridos em letras minúsculas. Utilize o endereço IP público da sua VM do comando anterior:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Lhe for pedido para iniciar sessão no Azure AD com um código de utilização única em [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Copie e cole o código de utilização única página de início de sessão do dispositivo, conforme mostrado no exemplo a seguir:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Quando lhe for pedido, introduza as credenciais de início de sessão do Azure AD a página de início de sessão. A seguinte mensagem de erro é apresentada no navegador da web, quando tem de ser autenticado com êxito:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Feche a janela do browser, devolva o SSH linha de comandos e prima a **Enter** chave. Tem agora sessão iniciada para a máquina virtual do Linux do Azure com as permissões de função como atribuído, como *utilizador de VM* ou *administrador da VM*. Se a sua conta de utilizador é atribuída a *início de sessão de administrador do Virtual Machine* função, pode usar o `sudo` para executar comandos que exigem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Início de sessão Sudo e AAD

A primeira vez que executar o sudo, será solicitado para autenticar uma segunda vez. Se não quiser tem de autenticar novamente para executar o sudo, pode editar o ficheiro de sudoers `/etc/sudoers.d/aad_admins` e substitua esta linha:

```bash
%aad_admins ALL=(ALL) ALL
```
com esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Resolver problemas de início de sessão

Alguns erros comuns quando tenta colocar SSH com credenciais do Azure AD incluem não existem funções RBAC atribuídas e repetido pedidos para iniciar sessão. Utilize as secções seguintes para corrigir esses problemas.

### <a name="access-denied-rbac-role-not-assigned"></a>Acesso negado: Função RBAC não atribuída

Se vir o seguinte erro na sua linha de comandos SSH, certifique-se de que configurou políticas RBAC para a VM que concede ao usuário ou o *início de sessão de administrador do Virtual Machine* ou *início de sessão do utilizador de Máquina Virtual*função:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Contínuos pedidos de início de sessão do SSH

Se concluir com êxito a etapa de autenticação num navegador da web, poderão ser imediatamente a pedido para iniciar sessão novamente com um novo código. Este erro normalmente é causado por um erro de correspondência entre o nome de início de sessão que especificou na linha de comandos SSH e a conta que iniciou sessão no Azure AD com. Para corrigir este problema:

- Certifique-se de que o nome de início de sessão que especificou na linha de comandos SSH está correto. Um erro de digitação no nome do início de sessão pode causar um erro de correspondência entre o nome de início de sessão que especificou na linha de comandos SSH e a conta que iniciou sessão no Azure AD com. Por exemplo, que escreveu *azuresuer@contoso.onmicrosoft.com* em vez de *azureuser@contoso.onmicrosoft.com*.
- Se tiver várias contas de utilizador, certifique-se de que não fornecer uma conta de utilizador diferente na janela do browser, ao iniciar sessão com o Azure AD.
- Linux é um sistema de operativo diferencia maiúsculas de minúsculas. Existe uma diferença entre "Azureuser@contoso.onmicrosoft.com"e"azureuser@contoso.onmicrosoft.com', que pode causar um erro de correspondência. Certifique-se de que especifica o UPN com a sensibilidade correta na linha de comandos SSH.

## <a name="preview-feedback"></a>Comentários de pré-visualização

Partilhe os seus comentários sobre esta pré-visualização funcionalidade ou reportar problemas usá-lo no [fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure Active Directory, consulte [o que é o Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
