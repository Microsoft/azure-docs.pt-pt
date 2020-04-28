---
title: Inscreva-se na máquina virtual do Windows em Azure utilizando o Diretório Ativo Azure (Pré-visualização)
description: Azure AD inscreve-se num VM Azure a executar windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050068"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Inscreva-se na máquina virtual do Windows em Azure utilizando a autenticação do Diretório Ativo Azure (Pré-visualização)

As organizações podem agora utilizar a autenticação do Azure Ative Directory (AD) para as suas máquinas virtuais Azure (VMs) que executam a edição do **Windows Server 2019 Datacenter** ou **o Windows 10 1809** e mais tarde. A utilização da AD Azure para autenticar os VMs fornece-lhe uma forma de controlar centralmente e aplicar políticas. Ferramentas como o Azure Role-Based Access (RBAC) e o Azure AD Conditional Access permitem controlar quem pode aceder a um VM. Este artigo mostra-lhe como criar e configurar um VM Windows Server 2019 para utilizar a autenticação Azure AD.

|     |
| --- |
| O registo da Azure AD para Os VMs do Windows Azure é uma funcionalidade de pré-visualização pública do Diretório Ativo Azure. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

Existem muitos benefícios em usar a autenticação Azure AD para iniciar sessão nos VMs do Windows em Azure, incluindo:

- Utilize as mesmas credenciais de AD Azure federadas ou geridas que normalmente utiliza.
- Já não têm de gerir as contas dos administradores locais.
- O Azure RBAC permite-lhe conceder o acesso adequado aos VMs com base nas necessidades e removê-lo quando já não for necessário.
- Antes de permitir o acesso a um VM, o Azure AD Conditional Access pode impor requisitos adicionais tais como: 
   - Multi-factor authentication
   - Verificação de risco de entrada
- Automatizar e escalar o Azure AD junta-se aos VMs do Windows Azure que fazem parte das suas implementações VDI.

> [!NOTE]
> Assim que ativar esta capacidade, os seus VMs Windows em Azure serão aderidos ao Azure AD. Não pode aderir a outro domínio, como a AD ou a AD DS Azure. Se precisar de o fazer, terá de desligar o VM do seu inquilino Azure AD desinstalando a extensão.

## <a name="requirements"></a>Requisitos

### <a name="supported-azure-regions-and-windows-distributions"></a>Regiões azure suportadas e distribuição do Windows

As seguintes distribuições do Windows são atualmente suportadas durante a pré-visualização desta funcionalidade:

- Windows Server 2019 Datacenter
- Windows 10 1809 e mais tarde

> [!IMPORTANT]
> A ligação remota aos VMs aquecia-se ao Azure AD só é permitida a partir de PCs Do Windows 10 que são Azure AD ou Azure AD híbrido sounia do **mesmo** diretório que o VM. 

As seguintes regiões azure são atualmente apoiadas durante a pré-visualização desta funcionalidade:

- Todas as regiões globais de Azure

> [!IMPORTANT]
> Para utilizar esta funcionalidade de pré-visualização, basta implementar uma distribuição suportada pelo Windows e numa região azure suportada. A funcionalidade não é atualmente apoiada no Governo de Azure ou em nuvens soberanas.

### <a name="network-requirements"></a>Requisitos da rede

Para ativar a autenticação AD Azure para os seus VMs Windows em Azure, é necessário garantir que a configuração da rede VMs permite o acesso de saída aos seguintes pontos finais sobre a porta TCP 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Habilitar o Anúncio Azure para windows VM em Azure

Para utilizar o Acesso ao Azure AD para o Windows VM em Azure, é necessário ativar primeiro a opção de login azure AD para o seu Windows VM e, em seguida, precisa configurar as atribuições de funções RBAC para utilizadores autorizados a iniciar sessão no VM.
Existem várias formas de ativar o login da AD Azure para o seu Windows VM:

- Utilizando a experiência do portal Azure ao criar um Windows VM
- Utilizando a experiência Azure Cloud Shell ao criar um VM Windows **ou para um VM Windows existente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Utilizando o portal Azure, criar experiência VM para permitir o login da AD Azure

Pode ativar o login da AD Azure para o Windows Server 2019 Datacenter ou Windows 10 1809 e posteriores imagens VM. 

Para criar um Windows Server 2019 Datacenter VM em Azure com logon Azure AD: 

1. Inscreva-se no [portal Azure,](https://portal.azure.com)com uma conta que tenha acesso para criar VMs, e selecione **+ Criar um recurso**.
1. Digite **o Servidor windows** em Procurar a barra de pesquisa do Marketplace.
   1. Clique no **Windows Server** e escolha o **Datacenter do Windows Server 2019** a partir de Selecionar uma desistência do plano de software.
   1. Clique em **Criar**.
1. No separador "Gestão", ative a opção de **iniciar sessão com credenciais AAD (Pré-visualização)** sob a secção de Diretório Ativo Azure de Off to **On**.
1. Certifique-se de que o **Sistema atribuído à identidade gerida** ao abrigo da secção Identidade está definido para **On**. Esta ação deve acontecer automaticamente assim que ativar o Login com credenciais Azure AD.
1. Passe pelo resto da experiência de criar uma máquina virtual. Durante esta pré-visualização, terá de criar um nome de utilizador e uma senha de administrador para o VM.

![Login com credenciais Azure AD criar um VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Para iniciar sessão no VM utilizando a sua credencial Azure AD, primeiro terá de configurar atribuições de funções para o VM, conforme descrito numa das secções abaixo.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Utilizando a experiência Azure Cloud Shell para permitir o login da AD Azure

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Basta selecionar o botão Copiar para copiar o código, colá-lo no Cloud Shell e, em seguida, premir Enter para executá-lo. Existem algumas formas de abrir o Cloud Shell:

Selecione Experimentar no canto superior direito de um bloco de código.
Abrir o Cloud Shell no seu browser.
Selecione o botão Cloud Shell no menu, no canto superior direito do [portal do Azure](https://portal.azure.com).

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.31 ou posterior. Execute az --version para localizar a versão. Se precisar de instalar ou atualizar, consulte o artigo [Instale o Azure CLI](/cli/azure/install-azure-cli).

1. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). 
1. Crie um VM com [az vm criar](/cli/azure/vm#az-vm-create) usando uma distribuição apoiada numa região apoiada. 
1. Instale a extensão VM de login Azure AD. 

O exemplo seguinte implementa um VM chamado myVM que utiliza o Win2019Datacenter, num grupo de recursos chamado myResourceGroup, na região centro-sul. Nos seguintes exemplos, pode fornecer os nomes do seu próprio grupo de recursos e VM, conforme necessário.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> É necessário que ative a identidade gerida atribuída pelo Sistema na sua máquina virtual antes de instalar a extensão VM de login Azure AD.

São necessários alguns minutos para criar a VM e os recursos de suporte.

Por fim, instale a extensão VM de login Azure AD para ativar o login da AD Azure para o Windows VM. As extensões VM são pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em máquinas virtuais Azure. Utilize um conjunto de [extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão AADLoginForWindows no VM nomeado myVM no grupo de recursos myResourceGroup:

> [!NOTE]
> Pode instalar a extensão AADLoginForWindows num Windows Server 2019 ou Windows 10 1809 existente e posteriorMente VM para o permitir a autenticação de AD Azure. Um exemplo de AZ CLI é mostrado abaixo.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O `provisioningState` `Succeeded` de si é mostrado, uma vez instalada a extensão no VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de funções para o VM

Agora que criou o VM, precisa de configurar a política Azure RBAC para determinar quem pode iniciar sessão no VM. Duas funções RBAC são usadas para autorizar o login VM:

- **Login**do Administrador de Máquina Virtual : Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios de administrador.
- **Login**de utilizador de máquina virtual : Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios regulares do utilizador.

> [!NOTE]
> Para permitir que um utilizador inicie sessão no VM sobre RDP, deve atribuir o Login do Administrador da Máquina Virtual ou a função de Login de Utilizador de Máquina Virtual. Um utilizador Azure com as funções de Proprietário ou Colaborador atribuídas a um VM não tem automaticamente privilégios de iniciar sessão no VM sobre RDP. Isto é para proporcionar uma separação auditada entre o conjunto de pessoas que controlam máquinas virtuais contra o conjunto de pessoas que podem aceder a máquinas virtuais.

Existem várias formas de configurar atribuições de funções para VM:

- Utilizando a experiência Azure AD Portal
- Usando a experiência Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Utilizando a experiência do Portal Azure AD

Para configurar as atribuições de funções para o seu Azure AD ativaram os VMs do Datacenter Windows Server 2019:

1. Navegue para a página de visão geral da máquina virtual específica
1. Selecione controlo de **acesso (IAM)** a partir das opções do menu
1. Selecione **Adicionar**, **Adicionar atribuição de funções** para abrir o painel de atribuição de funções Adicionar.
1. Na lista de abandono de **funções,** selecione uma função como **Login** do Administrador de Máquina Virtual ou **Login de Utilizador**de Máquina Virtual .
1. No campo **Select,** selecione um utilizador, grupo, diretor de serviço ou identidade gerida. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.
1. Selecione **Guardar,** para atribuir a função.

Após alguns momentos, o diretor de segurança é designado o papel no âmbito selecionado.

![Atribuir funções aos utilizadores que acederão ao VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Usando a experiência Azure Cloud Shell

O exemplo seguinte utiliza a [atribuição de funções az para](/cli/azure/role/assignment#az-role-assignment-create) atribuir a função de Login do Administrador de Máquina Virtual ao VM para o seu utilizador atual Do IA. O nome de utilizador da sua conta Ative Azure é obtido com [a conta az show](/cli/azure/account#az-account-show), e o âmbito é definido para o VM criado em um passo anterior com [az vm show](/cli/azure/vm#az-vm-show). O âmbito também pode ser atribuído a um grupo de recursos ou a nível de subscrição, e aplicam-se permissões normais de herança RBAC. Para mais informações, consulte [Controlos de Acesso baseados em funções](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o seu domínio AAD e o seu domínio de nome de utilizador `--assignee-object-id`de logon não `--assignee`corresponderem, deve especificar o ID do objeto da sua conta de utilizador com o , e não apenas o nome de utilizador para . Pode obter o ID do objeto para a sua conta de utilizador com [a lista de utilizadores az ad](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como utilizar o RBAC para gerir o acesso aos seus recursos de subscrição do Azure, consulte os seguintes artigos:

- [Gerir o acesso aos recursos Azure utilizando o RBAC e o Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [Manage access to Azure resources using RBAC and the Azure portal](/azure/role-based-access-control/role-assignments-portal) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Gerencie o acesso aos recursos Azure utilizando o RBAC e o Azure PowerShell.](/azure/role-based-access-control/role-assignments-powershell)

## <a name="using-conditional-access"></a>Utilização de acesso condicional

Pode impor políticas de Acesso Condicional, tais como a autenticação de vários fatores ou a verificação de risco de entrada do utilizador antes de autorizar o acesso aos VMs do Windows em Azure que estão habilitados com o registo de AD Azure. Para aplicar a política de Acesso Condicional, deve selecionar a aplicação "Azure Windows VM Sign-In" a partir das aplicações ou opções de atribuição de ações na nuvem e, em seguida, utilizar o risco de entrada como condição e/ou exigir a autenticação de vários fatores como controlo de acesso de subvenção. 

> [!NOTE]
> Se utilizar a "Requerer a autenticação de vários fatores" como controlo de acesso de subvenção para solicitar o acesso à aplicação "Sign-In Do Windows VM" do Windows, então deve fornecer uma reclamação de autenticação de vários fatores como parte do cliente que inicia a sessão de RDP no target Windows VM em Azure. A única maneira de o conseguir num cliente do Windows 10 é utilizar o Windows Hello para Business PIN ou authenication biométrico com o cliente RDP. O suporte para autenticação biométrica foi adicionado ao cliente RDP na versão 1809 do Windows 1809. O ambiente de trabalho remoto que utiliza a autenticação do Windows Hello for Business só está disponível para implementações que utilizem o modelo cert trust e que atualmente não estão disponíveis para o modelo de confiança chave.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Inicie sessão utilizando credenciais de AD Azure para um VM do Windows

> [!IMPORTANT]
> A ligação remota aos VMs aquecia-se ao Azure AD só é permitida a partir de PCs Do Windows 10 que são Azure AD ou Azure AD híbrido sounia do **mesmo** diretório que o VM. Além disso, para rdp utilizando credenciais Azure AD, o utilizador deve pertencer a uma das duas funções RBAC, Login do Administrador de Máquina Virtual ou Login de Utilizador de Máquina Virtual. Neste momento, o Azure Bastion não pode ser utilizado para iniciar sessão utilizando a autenticação do Diretório Ativo Azure com a extensão AADLoginForWindows. Apenas o RDP direto é suportado.

Para iniciar sessão na sua máquina virtual Windows Server 2019 utilizando o Azure AD: 

1. Navegue para a página geral da máquina virtual que foi ativada com logon Azure AD.
1. Selecione **Ligar** para abrir a Ligação à lâmina de máquina virtual.
1. Selecione **Download RDP File**.
1. Selecione **Open** para lançar o cliente de Ligação remota de ambiente de trabalho.
1. Selecione **Connect** para lançar o diálogo de início de sessão do Windows.
1. Logon usando as suas credenciais De D.A. Azure.

Está agora inscrito na máquina virtual Do Windows Server 2019 Azure com as permissões de funções atribuídas, tais como Utilizador VM ou Administrador VM. 

> [!NOTE]
> Pode salvar o. Ficheiro RDP localmente no seu computador para lançar futuras ligações remotas de ambiente de trabalho à sua máquina virtual em vez de ter de navegar para a página de visão geral da máquina virtual no portal Azure e utilizar a opção de ligação.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

A extensão AADLoginForWindows deve instalar com sucesso para que o VM complete o processo de adesão do Azure AD. Execute os seguintes passos se a extensão VM não instalar corretamente.

1. RDP para o VM usando a conta de administrador local e examinar o commandExecuti'n.log em  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Se a extensão recomeçar após a falha inicial, o registo com o erro de implementação será guardado CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Abra um pedido de comando no VM e verifique estas consultas contra o Ponto final do Serviço de Metadados de Instância (IMDS) em execução nas devoluções do hospedeiro Azure:

   | Comando para correr | Resultado esperado |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informação correta sobre o Azure VM |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID de inquilino válido associado à Assinatura Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Ficha de acesso válida emitida pelo Azure Ative Directory para a identidade gerida que é atribuída a este VM |

   > [!NOTE]
   > O token de acesso pode ser [http://calebb.net/](http://calebb.net/)descodificado utilizando uma ferramenta como . Verifique se o "apendicido" no token de acesso corresponde à identidade gerida atribuída ao VM.

1. Certifique-se de que os pontos finais necessários estão acessíveis a partir do VM utilizando a linha de comando:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –

   > [!NOTE]
   > Substitua `<TenantID>` pelo ID do Inquilino Azure AD que está associado à subscrição azure.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. O Estado do Dispositivo `dsregcmd /status`pode ser visto executando . O objetivo é que o `AzureAdJoined : YES`Estado do Dispositivo mostre como .

   > [!NOTE]
   > A atividade de adesão do Azure AD é capturada no espectador do Evento sob o registo de registo de dispositivos de utilizador\Admin.

Se a extensão AADLoginForWindows falhar com determinado código de erro, pode executar os seguintes passos:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Número 1: A extensão AADLoginForWindows não é instalada com o código de erro do terminal '1007' e código de saída: -2145648574.

Este código de saída traduz-se em DSREG_E_MSI_TENANTID_UNAVAILABLE porque a extensão não é capaz de consultar a informação do Inquilino Azure AD.

1. Verifique se o Azure VM pode recuperar o Id ideinquilino do Serviço de Metadados de Instância.

   - RDP para o VM como administrador local e verificar se o ponto final devolve id de inquilino válido executando este comando a partir de uma linha de comando elevada no VM:
      
      - curl -H Metadados:verdadeirohttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. O administrador vM tenta instalar a extensão AADLoginForWindows, mas um sistema de identidade gerida não permitiu primeiro o VM. Navegue para a lâmina de identidade do VM. A partir do separador designado pelo Sistema, verifique se o Estado está alternado para On.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Número 2: A extensão AADLoginForWindows falha na instalação com código de saída: -2145648607

Este código de saída traduz-se em DSREG_AUTOJOIN_DISC_FAILED `https://enterpriseregistration.windows.net` porque a extensão não é capaz de chegar ao ponto final.

1. Verifique se os pontos finais necessários estão acessíveis a partir do VM utilizando a linha de comando:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –
   
   > [!NOTE]
   > Substitua `<TenantID>` pelo ID do Inquilino Azure AD que está associado à subscrição azure. Se precisar de encontrar o ID do inquilino, pode pairar sobre o nome da sua conta para obter o ID de diretório/inquilino, ou selecionar O Diretório Ativo da Azure > Propriedades > Id diretório no portal Azure.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Se algum dos comandos falhar com "Não consegui resolver o hospedeiro", `<URL>`tente executar este comando para determinar o servidor DNS que está a ser utilizado pelo VM.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Substitua-a `<URL>` pelos nomes de domínio totalmente qualificados utilizados pelos pontos finais, tais como "login.microsoftonline.com".

1. Em seguida, veja se a especificação de um servidor Público DNS permite que o comando tenha sucesso:

   `nslookup <URL> 208.67.222.222`

1. Se necessário, altere o servidor DNS que é atribuído ao grupo de segurança da rede a que o Azure VM pertence.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Número 3: A extensão AADLoginForWindows falha na instalação com código de saída: 51

O código de saída 51 traduz-se em "Esta extensão não é suportada no sistema operativo da VM".

Na Pré-visualização pública, a extensão AADLoginForWindows destina-se apenas a ser instalada no Windows Server 2019 ou no Windows 10 (Build 1809 ou posterior). Certifique-se de que a versão do Windows está suportada. Se a construção do Windows não for suportada, desinstale a extensão VM.

### <a name="troubleshoot-sign-in-issues"></a>Problemas de sessão de problemas

Alguns erros comuns quando se tenta rdp com credenciais De AD Azure incluem nenhuma função RBAC atribuída, cliente não autorizado ou método de login 2FA necessário. Utilize as seguintes informações para corrigir estas questões.

O Dispositivo e o Estado SSO podem ser vistos executando `dsregcmd /status`. O objetivo é que o `AzureAdJoined : YES` `SSO State` Estado `AzureAdPrt : YES`dispositivo mostre como e para mostrar.

Além disso, o Log-in RDP utilizando contas Azure AD é capturado no espectador de eventos sob os registos de eventos AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>Função RBAC não atribuída

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- A sua conta está configurada para evitar que utilize este dispositivo. Para mais informações, contacte o administrador do seu sistema

![A sua conta está configurada para evitar que utilize este dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verifique se [configuraas as políticas de RBAC](../../virtual-machines/linux/login-using-aad.md) para o VM que concede ao utilizador o Login do Administrador da Máquina Virtual ou a função de Login de Utilizador de Máquina Virtual:
 
#### <a name="unauthorized-client"></a>Cliente não autorizado

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- As suas credenciais não funcionaram.

![As suas credenciais não funcionaram.](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verifique se o PC do Windows 10 que está a utilizar para iniciar a ligação remota de ambiente de trabalho é aquele que é azure AD aquecido, ou o Azure AD híbrido juntou-se ao mesmo diretório Azure AD onde o seu VM está ligado. Para mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade de dispositivo.](/azure/active-directory/devices/overview)

> [!NOTE]
> O Windows 10 20H1 irá adicionar suporte para o PC registado pela Azure AD para iniciar a ligação remota de ambiente de trabalho ao seu VM. Junte-se ao Programa Insider do Windows para experimentar e explorar novas funcionalidades do Windows 10.

Além disso, verifique se a extensão AADLoginForWindows não foi desinstalada depois de a adesão do Azure AD ter sido concluída.
 
#### <a name="mfa-sign-in-method-required"></a>Método de inscrição no MFA necessário

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- O método de inscrição que está supor não é permitido. Experimente um método de inscrição diferente ou contacte o administrador do sistema.

![O método de inscrição que está supor não é permitido.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se configurar uma política de Acesso Condicional que requer a autenticação de vários fatores (MFA) antes de poder aceder ao recurso, então precisa de garantir que o PC do Windows 10 dá início à ligação remota de desktop aos sinais vM, utilizando um método de autenticação forte, como o Windows Hello. Se não utilizar um método de autenticação forte para a sua ligação remota ao ambiente de trabalho, verá o erro anterior.

Se ainda não implementou o Windows Hello for Business e se isso não for uma opção por enquanto, pode excluir o requisito do MFA configurando a política de Acesso Condicional que exclui a aplicação "Azure Windows VM Sign-In" da lista de aplicações na nuvem que requerem MFA. Para saber mais sobre o Windows Hello for Business, consulte [o Windows Hello for Business Overview](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> O Windows Hello for Business PIN autenticação com RDP foi suportado pelo Windows 10 para várias versões, no entanto foi adicionado suporte para autenticação biométrica com RDP na versão 1809 do Windows 10. A utilização do Windows Hello for Business auth durante o RDP só está disponível para implementações que utilizem o modelo cert trust e que atualmente não estão disponíveis para o modelo de confiança chave.
 
## <a name="preview-feedback"></a>Comentários sobre a pré-visualização

Partilhe o seu feedback sobre esta funcionalidade de pré-visualização ou reporte problemas que o utilizam no fórum de [feedback da AD Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Diretório Ativo azure, consulte [o What is Azure Ative Directory](/azure/active-directory/fundamentals/active-directory-whatis)
