---
title: Faça logon em uma VM do Linux com credenciais de Azure Active Directory | Microsoft Docs
description: Saiba como criar e configurar uma VM do Linux para entrar usando Azure Active Directory autenticação.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: b473844f1507285e0052ca1f8de00f6ca3207e6f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327085"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Pré-visualização: Faça logon em uma máquina virtual Linux no Azure usando a autenticação Azure Active Directory

Para melhorar a segurança das VMs (máquinas virtuais) do Linux no Azure, você pode integrar com a autenticação do Azure Active Directory (AD). Quando você usa a autenticação do Azure AD para VMs do Linux, controla e aplica centralmente políticas que permitem ou negam o acesso às VMs. Este artigo mostra como criar e configurar uma VM do Linux para usar a autenticação do Azure AD.


> [!IMPORTANT]
> A autenticação Azure Active Directory está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Use esse recurso em uma máquina virtual de teste que você espera descartar após o teste.
>


Há muitos benefícios em usar a autenticação do Azure AD para fazer logon em VMs do Linux no Azure, incluindo:

- **Segurança aprimorada:**
  - Você pode usar suas credenciais corporativas do AD para fazer logon em VMs Linux do Azure. Não é necessário criar contas de administrador local e gerenciar o tempo de vida da credencial.
  - Ao reduzir sua dependência em contas de administrador local, você não precisa se preocupar com perda/roubo de credenciais, usuários Configurando credenciais fracas etc.
  - A complexidade de senha e as políticas de tempo de vida de senha configuradas para seu diretório do Azure AD ajudam a proteger VMs Linux também.
  - Para proteger ainda mais o logon nas máquinas virtuais do Azure, você pode configurar a autenticação multifator.
  - A capacidade de fazer logon em VMs do Linux com o Azure Active Directory também funciona para clientes que usam [serviços de Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Colaboração direta:** Com o RBAC (controle de acesso baseado em função), você pode especificar quem pode entrar em uma determinada VM como um usuário comum ou com privilégios de administrador. Quando os usuários ingressam ou deixam sua equipe, você pode atualizar a política de RBAC para a VM para conceder acesso conforme apropriado. Essa experiência é muito mais simples do que ter de limpar VMs para remover chaves públicas SSH desnecessárias. Quando os funcionários deixam sua organização e sua conta de usuário é desabilitada ou removida do Azure AD, eles não têm mais acesso aos seus recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões do Azure e distribuições do Linux com suporte

No momento, há suporte para as seguintes distribuições do Linux durante a versão prévia deste recurso:

| Distribuição | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14, 4 LTS, Ubuntu Server 16, 4 e Ubuntu Server 18, 4 |


No momento, há suporte para as seguintes regiões do Azure durante a versão prévia deste recurso:

- Todas as regiões globais do Azure

>[!IMPORTANT]
> Para usar esse recurso de visualização, implante apenas um distribuição do Linux com suporte e em uma região do Azure com suporte. Não há suporte para o recurso em nuvens do Azure governamental ou soberanas.


Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando o CLI do Azure versão 2.0.31 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um grupo de recursos com [AZ Group Create](/cli/azure/group#az-group-create)e crie uma VM com [AZ VM Create](/cli/azure/vm#az-vm-create) usando um distribuição com suporte e em uma região com suporte. O exemplo a seguir implanta uma VM chamada *myVM* que usa o *Ubuntu 16, 4 LTS* em um grupo de recursos chamado *MyResource* Group na região *southcentralus* . Nos exemplos a seguir, você pode fornecer seu próprio grupo de recursos e nomes de VM, conforme necessário.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalar a extensão de VM de logon do Azure AD

> [!NOTE]
> Se estiver implantando essa extensão em uma VM criada anteriormente, verifique se a máquina tem pelo menos 1GB de memória alocada. caso contrário, a extensão falhará na instalação

Para fazer logon em uma VM do Linux com credenciais do Azure AD, instale a extensão de VM de logon Azure Active Directory. Extensões de VM são pequenos aplicativos que fornecem tarefas de automação e configuração pós-implantação em máquinas virtuais do Azure. Use [AZ VM Extension Set](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão *AADLoginForLinux* na VM denominada *myVM* no grupo de recursos *MyResource* Group:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O *provisioningState* de *Succeeded* é mostrado depois que a extensão é instalada com êxito na VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função para a VM

A política do RBAC (controle de acesso baseado em função) do Azure determina quem pode fazer logon na VM. Duas funções RBAC são usadas para autorizar o logon da VM:

- **Logon de administrador da máquina virtual**: Os usuários com essa função atribuída podem fazer logon em uma máquina virtual do Azure com privilégios de administrador do Windows ou de usuário raiz do Linux.
- **Logon de usuário da máquina virtual**: Os usuários com essa função atribuída podem fazer logon em uma máquina virtual do Azure com privilégios de usuário regulares.

> [!NOTE]
> Para permitir que um usuário faça logon na VM por meio de SSH, você deve atribuir a função de logon de *administrador de máquina* virtual ou de *usuário de máquina virtual* . Um usuário do Azure com as funções de *proprietário* ou *colaborador* atribuídas a uma VM não tem privilégios automaticamente para fazer logon na VM por SSH.

O exemplo a seguir usa [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função de *logon de administrador de máquina virtual* à VM para o usuário atual do Azure. O nome de usuário da sua conta ativa do Azure é obtido com [AZ Account show](/cli/azure/account#az-account-show)e o *escopo* é definido como a VM criada em uma etapa anterior com [AZ VM show](/cli/azure/vm#az-vm-show). O escopo também pode ser atribuído em um nível de assinatura ou grupo de recursos, e as permissões de herança RBAC normais se aplicam. Para obter mais informações, consulte [controles de acesso baseado em função](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o domínio do AAD e o domínio de nome de usuário de logon não corresponderem, você deverá especificar a ID de objeto da sua conta de usuário com o *--atribuir-Object-ID*, não apenas o nome de usuário para *--destinatário*. Você pode obter a ID de objeto para sua conta de usuário com [AZ ad user list](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como usar o RBAC para gerenciar o acesso aos recursos de sua assinatura do Azure, consulte usando o [CLI do Azure](../../role-based-access-control/role-assignments-cli.md), [portal do Azure](../../role-based-access-control/role-assignments-portal.md)ou [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Você também pode configurar o Azure AD para exigir a autenticação multifator para um usuário específico entrar na máquina virtual Linux. Para obter mais informações, consulte Introdução [à autenticação multifator do Azure na nuvem](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Faça logon na máquina virtual Linux

Primeiro, exiba o endereço IP público de sua VM com [AZ VM show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Faça logon na máquina virtual Linux do Azure usando suas credenciais do Azure AD. O `-l` parâmetro permite que você especifique seu próprio endereço de conta do Azure AD. Substitua a conta de exemplo pela sua. Os endereços de conta devem ser inseridos em letras minúsculas. Substitua o endereço IP de exemplo pelo endereço IP público da sua VM do comando anterior.

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Você será solicitado a entrar no Azure AD com um código de uso único em [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin). Copie e cole o código de uso único na página de logon do dispositivo.

Quando solicitado, insira suas credenciais de logon do Azure AD na página de logon. 

A seguinte mensagem é mostrada no navegador da Web quando você se autenticou com êxito:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Feche a janela do navegador, retorne ao prompt do SSH e pressione a tecla **Enter** . 

Agora você está conectado à máquina virtual Linux do Azure com as permissões de função atribuídas, como *usuário da VM* ou *administrador da VM*. Se sua conta de usuário for atribuída à função de *logon de administrador da máquina virtual* , você poderá usar `sudo` o para executar comandos que exigem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Logon do sudo e do AAD

Na primeira vez que você executar o sudo, você será solicitado a autenticar uma segunda vez. Se você não quiser que o autentique novamente para executar o sudo, poderá editar o arquivo `/etc/sudoers.d/aad_admins` do sudoers e substituir essa linha:

```bash
%aad_admins ALL=(ALL) ALL
```
com esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Alguns erros comuns ao tentar usar SSH com as credenciais do Azure AD não incluem nenhuma função RBAC atribuída e prompts repetidos para entrar. Use as seções a seguir para corrigir esses problemas.

### <a name="access-denied-rbac-role-not-assigned"></a>Acesso negado: Função RBAC não atribuída

Se você vir o seguinte erro no prompt do SSH, verifique se você configurou as políticas de RBAC para a VM que concede ao usuário o *logon de administrador da máquina virtual* ou a função de *logon de usuário da máquina virtual* :

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Prompts de entrada SSH contínuos

Se você concluiu com êxito a etapa de autenticação em um navegador da Web, você pode ser imediatamente solicitado a entrar novamente com um novo código. Esse erro é geralmente causado por uma incompatibilidade entre o nome de entrada especificado no prompt do SSH e a conta com a qual você entrou no Azure AD. Para corrigir esse problema:

- Verifique se o nome de entrada especificado no prompt do SSH está correto. Uma grafia no nome de entrada pode causar uma incompatibilidade entre o nome de entrada especificado no prompt do SSH e a conta com a qual você entrou no Azure AD. Por exemplo, você digitou *azuresuer\@contoso.onmicrosoft.com* em vez de *azureuser\@contoso.onmicrosoft.com*.
- Se você tiver várias contas de usuário, certifique-se de não fornecer uma conta de usuário diferente na janela do navegador ao entrar no Azure AD.
- O Linux é um sistema operacional que diferencia maiúsculas de minúsculas. Há uma diferença entre 'Azureuser@contoso.onmicrosoft.com' e 'azureuser@contoso.onmicrosoft.com', o que pode causar uma incompatibilidade. Certifique-se de especificar o UPN com a diferenciação de maiúsculas e minúsculas correta no prompt do SSH.

## <a name="preview-feedback"></a>Visualizar comentários

Compartilhe seus comentários sobre este recurso de visualização ou relate problemas usando-os no [Fórum de comentários do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Azure Active Directory, consulte [o que é Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
