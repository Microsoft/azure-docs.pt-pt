---
title: Iniciar seduca na máquina virtual do Windows em Azure utilizando o Azure Ative Directory (Preview)
description: Azure AD iniciar súmito para um Azure VM executando Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66958e275a6dee13244f0f283b1ee1dbb5d40470
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093457"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Iniciar sedutação na máquina virtual do Windows em Azure utilizando a autenticação do Azure Ative Directory (Preview)

As organizações podem agora utilizar a autenticação do Azure Ative Directory (AD) para as suas máquinas virtuais Azure (VMs) que executam a **edição do Datacenter 2019 do Windows Server 2019** ou **do Windows 10 1809** e posteriormente. A utilização do Azure AD para autenticar em VMs proporciona-lhe uma forma de controlar e impor políticas centralmente. Ferramentas como o controlo de acesso baseado em funções Azure (Azure RBAC) e acesso condicional Ad Ad Azure permitem-lhe controlar quem pode aceder a um VM. Este artigo mostra-lhe como criar e configurar um VM Windows Server 2019 para utilizar a autenticação AD AZure.

> [!NOTE]
> O Azure AD in for Azure Windows VMs é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, consulte  [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Existem muitos benefícios em utilizar a autenticação AD AZure para iniciar sessão no Windows VMs em Azure, incluindo:

- Utilize as mesmas credenciais AD federadas ou geridas que normalmente utiliza.
- Já não têm de gerir as contas de administradores locais.
- O Azure RBAC permite-lhe conceder o acesso adequado aos VMs com base na necessidade e removê-lo quando já não for necessário.
- Antes de permitir o acesso a um VM, o Acesso Condicionado Azure AD pode impor requisitos adicionais tais como: 
   - Autenticação multifator
   - Verificação de risco de entrada
- Automatize e dimensione Azure AD junte-se a VMs Azure Windows que fazem parte das suas implementações VDI.

> [!NOTE]
> Assim que ativar esta capacidade, os seus VMs windows em Azure serão aderidos ao Azure AD. Não pode junção a outros domínios, como AD ou AD DS Azure. Se precisar de o fazer, terá de desligar o VM do seu inquilino AZure AD desinstalando a extensão.

## <a name="requirements"></a>Requisitos

### <a name="supported-azure-regions-and-windows-distributions"></a>Regiões de Azure apoiadas e distribuição do Windows

As seguintes distribuições do Windows são atualmente suportadas durante a pré-visualização desta funcionalidade:

- Windows Server 2019 Datacenter
- Windows 10 1809 e mais tarde

> [!IMPORTANT]
> A ligação remota aos VMs aderidos ao Azure AD só é permitida a partir de PCs windows 10 que são registados a Azure AD (a partir do Windows 10 20H1), Azure AD unidos ou híbrido Azure AD unidos ao **mesmo** diretório que o VM. 

As seguintes regiões de Azure são atualmente apoiadas durante a pré-visualização desta funcionalidade:

- Todas as regiões globais do Azure

> [!IMPORTANT]
> Para utilizar esta funcionalidade de pré-visualização, apenas implemente uma distribuição suportada do Windows e numa região de Azure suportada. A funcionalidade não é atualmente apoiada no Governo de Azure ou em nuvens soberanas.

### <a name="network-requirements"></a>Requisitos de rede

Para ativar a autenticação AZure AD para os seus VMs windows em Azure, tem de garantir que a configuração da rede VMs permite o acesso de saída aos seguintes pontos finais sobre a porta TCP 443:

- https: \/ /enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https: \/ /device.login.microsoftonline.com
- https: \/ /pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Habilitar o Azure AD a iniciar sessão para o Windows VM em Azure

Para utilizar o login Azure AD para o Windows VM em Azure, tem de ativar primeiro a opção de login Azure AD para o seu Windows VM e depois tem de configurar atribuições de funções Azure para utilizadores autorizados a iniciar sessão no VM.
Existem várias formas de ativar o login AD AZure para o seu Windows VM:

- Utilizar a experiência do portal Azure ao criar um VM windows
- Utilizando a experiência Azure Cloud Shell ao criar um VM windows **ou para um VM do Windows existente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Utilizando o portal Azure criar experiência VM para ativar o login AD do Azure

Pode ativar o login AD AD do Windows Server 2019 ou do Windows 10 1809 e posteriormente imagens VM. 

Para criar um VM do Datacenter 2019 do Windows Server em Azure com o logon AD do Azure: 

1. Inscreva-se no [portal Azure,](https://portal.azure.com)com uma conta que tenha acesso à criação de VMs, e selecione **+ Criar um recurso.**
1. Digite **o Servidor do Windows** na barra de pesquisa do Marketplace.
   1. Clique no **Windows Server** e escolha o Centro **de Dados do Windows Server 2019** a partir de Selecionar um plano de software desacolhido.
   1. Clique em **Criar**.
1. No separador "Gestão", ative a opção de **Iniciar sessão com credenciais AAD (Pré-visualização)** na secção Azure Ative Directory de Off to **On**.
1. Certifique-se de que a **identidade gerida atribuída** pelo Sistema na secção Identidade está definida para **On**. Esta ação deve acontecer automaticamente assim que ativar o Login com credenciais AD AZure.
1. Veja o resto da experiência de criar uma máquina virtual. Durante esta pré-visualização, terá de criar um nome de utilizador e senha de administrador para o VM.

![Login com credenciais AD AZure criam um VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Para iniciar sessão no VM utilizando a sua credencial Azure AD, terá primeiro de configurar atribuições de funções para o VM, conforme descrito numa das secções abaixo.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Utilizando a experiência Azure Cloud Shell para ativar o login AZure AD

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Basta selecionar o botão Copiar para copiar o código, colá-lo no Cloud Shell e, em seguida, premir Enter para executá-lo. Existem algumas formas de abrir o Cloud Shell:

Selecione Experimentar no canto superior direito de um bloco de código.
Abrir o Cloud Shell no seu browser.
Selecione o botão Cloud Shell no menu, no canto superior direito do [portal do Azure](https://portal.azure.com).

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.31 ou posterior. Execute az --version para localizar a versão. Se necessitar de instalar ou atualizar, consulte o artigo Instalar O [Azure CLI](/cli/azure/install-azure-cli).

1. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). 
1. Crie um VM com [az vm criar](/cli/azure/vm#az-vm-create) usando uma distribuição suportada numa região apoiada. 
1. Instale a extensão VM de login Azure. 

O exemplo a seguir implementa um VM chamado myVM que utiliza o Win2019Datacenter, num grupo de recursos chamado myResourceGroup, na região sulcentralus. Nos seguintes exemplos, pode fornecer os nomes do seu próprio grupo de recursos e VM, conforme necessário.

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
> É necessário que ative a identidade gerida atribuída ao Sistema na sua máquina virtual antes de instalar a extensão VM de login Azure.

São necessários alguns minutos para criar a VM e os recursos de suporte.

Por fim, instale a extensão VM de login Azure AD para permitir o login Azure AD para o Windows VM. As extensões VM são pequenas aplicações que fornecem tarefas de configuração e automatização pós-implantação em máquinas virtuais Azure. Utilize o conjunto [de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão AADLoginForWindows no VM nomeado myVM no grupo de recursos myResourceGroup:

> [!NOTE]
> Pode instalar a extensão AADLoginForWindows num Windows Server 2019 ou Windows 10 1809 existente e mais tarde em VM para a permitir a autenticação AD do Azure. Um exemplo de AZ CLI é mostrado abaixo.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O `provisioningState` de `Succeeded` é mostrado, uma vez que a extensão é instalada no VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configure atribuições de funções para o VM

Agora que criou o VM, precisa configurar a política do RBAC para determinar quem pode iniciar sessão no VM. Duas funções Azure são usadas para autorizar o login em VM:

- **Início de entrada do Administrador de Máquina Virtual**: Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios de administrador.
- **Início de Sessão do Utilizador de Máquinas Virtuais**: Os utilizadores com esta função atribuída podem iniciar sessão numa máquina virtual Azure com privilégios regulares do utilizador.

> [!NOTE]
> Para permitir que um utilizador faça login no VM em rdp, tem de atribuir o Login do Administrador de Máquina Virtual ou a função de Login do Utilizador de Máquina Virtual. Um utilizador Azure com as funções Proprietário ou Contribuinte atribuídos a um VM não tem automaticamente privilégios de iniciar sessão no VM em vez de RDP. Isto é para proporcionar uma separação auditada entre o conjunto de pessoas que controlam máquinas virtuais contra o conjunto de pessoas que podem aceder a máquinas virtuais.

Existem várias formas de configurar atribuições de funções para VM:

- Utilizando a experiência do Portal AD Azure
- Usando a experiência Azure Cloud Shell

> [!NOTE]
> As funções de Login do Administrador de Máquina Virtual e de Início de Sessão do Utilizador de Máquina Virtual utilizam dataActions e, portanto, não podem ser atribuídas no âmbito do grupo de gestão. Atualmente, estas funções só podem ser atribuídas no âmbito de subscrição, grupo de recursos ou recursos.

### <a name="using-azure-ad-portal-experience"></a>Usando a experiência do Portal AD AZure

Para configurar atribuições de funções para o seu Azure AD ativado o Windows Server 2019 Datacenter VMs:

1. Navegue para a página geral específica da máquina virtual
1. Selecione o controlo de **acesso (IAM)** a partir das opções do menu
1. **Selecione Adicionar**, Adicione a **atribuição de funções** para abrir o painel de atribuição de funções Add.
1. Na lista de drop-down **role,** selecione uma função como **O Login do Administrador de Máquina Virtual** ou Login do Utilizador de Máquina **Virtual**.
1. No campo **Selecione,** selecione um utilizador, grupo, principal de serviço ou identidade gerida. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.
1. **Selecione Guardar,** para atribuir o papel.

Após alguns momentos, o diretor de segurança é atribuído o papel no âmbito selecionado.

![Atribuir funções aos utilizadores que acedam ao VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Usando a experiência Azure Cloud Shell

O exemplo a seguir utiliza [a atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função de Login do Administrador de Máquina Virtual ao VM para o seu utilizador Azure atual. O nome de utilizador da sua conta Azure ativa é obtido com [a conta AZ](/cli/azure/account#az-account-show), e o âmbito é definido para o VM criado em passo anterior com [a az vm show](/cli/azure/vm#az-vm-show). O âmbito também poderia ser atribuído a um grupo de recursos ou nível de subscrição, e as permissões de herança normal do Azure RBAC aplicam-se. Para obter mais informações, consulte [iniciar sessão numa máquina virtual Linux em Azure utilizando a autenticação do Azure Ative Directory](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o seu domínio AAD e o nome de utilizador do início de síltipo não coincidirem, tem de especificar o ID do objeto da sua conta de utilizador com o nome de `--assignee-object-id` utilizador para `--assignee` . Pode obter o ID do objeto para a sua conta de utilizador com [a lista de utilizadores de anúncios az](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como utilizar o Azure RBAC para gerir o acesso aos seus recursos de subscrição Azure, consulte os seguintes artigos:

- [Atribuir funções Azure usando Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Atribuir funções Azure usando o portal Azure](../../role-based-access-control/role-assignments-portal.md)
- [Atribua funções Azure utilizando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Usando acesso condicional

Pode impor políticas de Acesso Condicional, tais como autenticação de vários fatores ou verificação de risco de entrada no utilizador antes de autorizar o acesso a VMs do Windows em Azure que estão ativados com o sinal AD do Azure. Para aplicar a política de Acesso Condicional, tem de selecionar a aplicação "Azure Windows VM Sign-In" a partir da opção de atribuição de aplicações na nuvem ou ações e, em seguida, utilizar o risco de acesso como condição e/ou exigir a autenticação de vários fatores como controlo de acesso a subvenções. 

> [!NOTE]
> Se utilizar a "Requer autenticação multi-factor" como um controlo de acesso a subvenções para solicitar acesso à aplicação "Azure Windows VM Sign-In", então deve fornecer a reclamação de autenticação multi-factor como parte do cliente que inicia a sessão DE PDR para o windows VM alvo em Azure. A única forma de o conseguir num cliente do Windows 10 é utilizar o Windows Hello para Business PIN ou a autenticação biométrica com o cliente RDP. O suporte à autenticação biométrica foi adicionado ao cliente RDP na versão 1809 do Windows 10. O ambiente de trabalho remoto que utiliza a autenticação do Windows Hello for Business apenas está disponível para implementações que utilizam o modelo cert trust e que atualmente não estão disponíveis para o modelo de confiança chave.

> [!WARNING]
> Por utilizador Ativado/Aplicado Azure AD Multi-Factor Authentication não é suportado para o sismo VM.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Faça login usando credenciais AZure AD para um Windows VM

> [!IMPORTANT]
> A ligação remota aos VMs aderidos ao Azure AD só é permitida a partir de PCs windows 10 que estejam registados aZure AD (a construção mínima exigida é 20H1) ou a Azure AD unida ou híbrida Azure AD unida ao **mesmo** diretório que o VM. Além disso, para RDP utilizando credenciais AZure AD, o utilizador deve pertencer a uma das duas funções Azure, O Login do Administrador de Máquina Virtual ou Login do Utilizador de Máquina Virtual. Se utilizar um PC do Windows 10 registado no Azure AD, deve introduzir credenciais no formato AzureAD\UPN (por AzureAD\john@contoso.com exemplo). Neste momento, o Azure Bastion não pode ser utilizado para fazer login utilizando a autenticação do Azure Ative Directory com a extensão AADLoginForWindows; apenas é suportado RDP direto.

Para iniciar sessão na sua máquina virtual Windows Server 2019 utilizando Azure AD: 

1. Navegue para a página geral da máquina virtual que foi ativada com o logon AD Azure.
1. Selecione **Connect** para abrir a lâmina da máquina virtual Connect.
1. Selecione **Transferir Ficheiro RDP**.
1. Selecione **Open** para lançar o cliente Remote Desktop Connection.
1. Selecione **Connect** para lançar o diálogo de início de s logon do Windows.
1. Logor usando as suas credenciais AZure AD.

Está agora a fazer sessão no Windows Server 2019 Azure virtual machine com as permissões de função atribuídas, tais como Utilizador VM ou Administrador VM. 

> [!NOTE]
> Pode salvar o . O ficheiro RDP localmente no seu computador para lançar futuras ligações remotas de ambiente de trabalho à sua máquina virtual em vez de ter de navegar para a página geral da máquina virtual no portal Azure e utilizar a opção de ligação.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

A extensão AADLoginForWindows deve ser instalada com sucesso para que o VM complete o processo de ad AD Azure. Execute os seguintes passos se a extensão VM não for instalada corretamente.

1. RDP para o VM usando a conta de administrador local e examinar o CommandExecuti'n.log  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Se a extensão recomeçar após a falha inicial, o registo com o erro de implantação será guardado à medida que CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Abra uma solicitação de comando PowerShell no VM e verifique estas consultas contra o Serviço de Metadados de Instância (IMDS) que está a ser executada nas devoluções do anfitrião Azure:

   | Comando para correr | Resultado esperado |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informação correta sobre o Azure VM |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID de inquilino válido associado à Assinatura Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Ficha de acesso válida emitida pela Azure Ative Directory para a identidade gerida atribuída a este VM |

   > [!NOTE]
   > O token de acesso pode ser descodificado usando uma ferramenta como [http://calebb.net/](http://calebb.net/) . Verifique se o "appid" no token de acesso corresponde à identidade gerida atribuída ao VM.

1. Certifique-se de que os pontos finais necessários estão acessíveis a partir do VM utilizando a linha de comando:
   
   - Curl https: \/ /login.microsoftonline.com/ -D –
   - Curl https: \/ /login.microsoftonline.com/ `<TenantID>` / -D –

   > [!NOTE]
   > `<TenantID>`Substitua-se pelo ID do Inquilino Azure que está associado à assinatura Azure.

   - Curl https: \/ /enterpriseregistration.windows.net/ -D -
   - Curl https: \/ /device.login.microsoftonline.com/ -D -
   - Curl https: \/ /pas.windows.net/ -D -

1. O Estado do Dispositivo pode ser visto em funcionamento `dsregcmd /status` . O objetivo é que o Estado do Dispositivo mostre como `AzureAdJoined : YES` .

   > [!NOTE]
   > A atividade de ad AD azure é capturada no espectador de evento sob o registo do dispositivo de utilizador\Admin.

Se a extensão AADLoginForWindows falhar com determinado código de erro, pode executar os seguintes passos:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Emissão 1: A extensão AADLoginForWindows não é instalada com código de erro de terminal '1007' e código de saída: -2145648574.

Este código de saída traduz-se em DSREG_E_MSI_TENANTID_UNAVAILABLE porque a extensão não é capaz de consultar a informação do Azure AD Tenant.

1. Verifique se o Azure VM pode recuperar o TenantID do Serviço de Metadados de Exemplo.

   - RDP para o VM como administrador local e verificar o ponto final devolve o ID do inquilino válido executando este comando a partir de uma linha de comando elevada no VM:
      
      - curl -H Metadados:verdadeiro http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. O administrador VM tenta instalar a extensão AADLoginForWindows, mas um sistema atribuído identidade gerida não ativou primeiro o VM. Navegue até à lâmina de identidade do VM. A partir do separador Designado sistema, verifique se o Estado é alternado para On.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Edição 2: Extensão AADLoginForWindows falha na instalação com código de saída: -2145648607

Este código de saída traduz-se em DSREG_AUTOJOIN_DISC_FAILED porque a extensão não é capaz de chegar ao `https://enterpriseregistration.windows.net` ponto final.

1. Verifique se os pontos finais necessários estão acessíveis a partir do VM utilizando a linha de comando:

   - Curl https: \/ /login.microsoftonline.com/ -D –
   - Curl https: \/ /login.microsoftonline.com/ `<TenantID>` / -D –
   
   > [!NOTE]
   > `<TenantID>`Substitua-se pelo ID do Inquilino Azure que está associado à assinatura Azure. Se precisar de encontrar o ID do inquilino, pode pairar sobre o nome da sua conta para obter o ID do diretório/inquilino, ou selecionar O Diretório Ativo > Propriedades > ID do Diretório no portal Azure.

   - Curl https: \/ /enterpriseregistration.windows.net/ -D -
   - Curl https: \/ /device.login.microsoftonline.com/ -D -
   - Curl https: \/ /pas.windows.net/ -D -

1. Se algum dos comandos falhar com "Não conseguiu resolver o `<URL>` anfitrião", tente executar este comando para determinar o servidor DNS que está a ser utilizado pelo VM.
   
   `nslookup <URL>`

   > [!NOTE] 
   > `<URL>`Substitua-os pelos nomes de domínio totalmente qualificados utilizados pelos pontos finais, tais como "login.microsoftonline.com".

1. Em seguida, veja se especificar um servidor DNS público permite que o comando tenha sucesso:

   `nslookup <URL> 208.67.222.222`

1. Se necessário, altere o servidor DNS que é atribuído ao grupo de segurança de rede a que pertence o VM Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Edição 3: Extensão AADLoginForWindows falha na instalação com código de saída: 51

O código de saída 51 traduz-se em "Esta extensão não é suportada no sistema operativo do VM".

Na Visualização Pública, a extensão AADLoginForWindows destina-se apenas a ser instalada no Windows Server 2019 ou no Windows 10 (Build 1809 ou posterior). Certifique-se de que a versão do Windows está suportada. Se a construção do Windows não for suportada, desinstale a extensão VM.

### <a name="troubleshoot-sign-in-issues"></a>Problemas de resolução de problemas

Alguns erros comuns quando tentas pDR com credenciais AZure AD incluem nenhuma função Azure atribuída, cliente não autorizado ou método de login 2FA necessário. Utilize as seguintes informações para corrigir estas questões.

O Dispositivo e o Estado SSO podem ser vistos em funcionamento `dsregcmd /status` . O objetivo é que o Estado do Dispositivo mostre como `AzureAdJoined : YES` e `SSO State` mostrar `AzureAdPrt : YES` .

Além disso, o login RDP que utiliza as contas AZure AD é capturado no visualizador do Evento sob os registos de eventos AAD\Operational.

#### <a name="azure-role-not-assigned"></a>Papel de Azure não atribuído

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- A sua conta está configurada para evitar que utilize este dispositivo. Para mais informações, contacte o administrador do seu sistema

![A sua conta está configurada para evitar que utilize este dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verifique se [configuraram as políticas de RBAC do Azure](../../virtual-machines/linux/login-using-aad.md) para o VM que concede ao utilizador o Login do Administrador de Máquina Virtual ou a função de Login do Utilizador de Máquina Virtual:

> [!NOTE]
> Se estiver a ter problemas com atribuições de funções Azure, consulte [o Troubleshoot Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).
 
#### <a name="unauthorized-client"></a>Cliente não autorizado

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- As suas credenciais não funcionaram.

![As suas credenciais não funcionaram.](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verifique se o PC Windows 10 que está a utilizar para iniciar a ligação remota de ambiente de trabalho é aquele que está ligado ao Azure AD ou o AD híbrido Azure juntou-se ao mesmo diretório AD AD Azure onde o seu VM está ligado. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade do dispositivo](./overview.md).

> [!NOTE]
> O Windows 10 Build 20H1 adicionou suporte para um PC AD registado no Azure para iniciar a ligação RDP ao seu VM. Ao utilizar um AD AZure registado (não a Azure AD ligado ou híbrido Azure AD) PC como cliente RDP para iniciar ligações ao seu VM, deve introduzir credenciais no formato AzureAD\UPn (por AzureAD\john@contoso.com exemplo).

Verifique se a extensão AADLoginForWindows não foi desinstalada após a junção AD do Azure ter terminado.

Além disso, certifique-se de que a política de segurança "Segurança da rede: permitir que os pedidos de autenticação PKU2U a este computador utilizem identidades online" esteja ativada tanto no *servidor* como no cliente.
 
#### <a name="mfa-sign-in-method-required"></a>Método de inscrição do MFA necessário

Se vir a seguinte mensagem de erro quando iniciar uma ligação remota de ambiente de trabalho ao seu VM: 

- O método de inscrição que está a tentar usar não é permitido. Experimente um método de inscrição diferente ou contacte o administrador do sistema.

![O método de inscrição que está a tentar usar não é permitido.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se tiver configurado uma política de Acesso Condicional que exija autenticação de vários fatores (MFA) antes de poder aceder ao recurso, então tem de garantir que o PC do Windows 10 inicia a ligação remota de ambiente de trabalho aos sinais de VM utilizando um método de autenticação forte, como o Windows Hello. Se não utilizar um método de autenticação forte para a sua ligação remota ao ambiente de trabalho, verá o erro anterior.

Se não tiver implementado o Windows Hello for Business e se isso não for uma opção por enquanto, pode excluir a exigência de MFA configurando a política de Acesso Condicional que exclui a aplicação "Azure Windows VM Sign-In" da lista de aplicações na nuvem que requerem MFA. Para saber mais sobre o Windows Hello for Business, consulte [o Windows Hello for Business Overview](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> A autenticação do Windows Hello for Business PIN com RDP foi suportada pelo Windows 10 para várias versões, no entanto o suporte para autenticação biométrica com RDP foi adicionado na versão 1809 do Windows 10. A utilização do Windows Hello for Business auth durante o RDP só está disponível para implementações que utilizem o modelo cert trust e que atualmente não estão disponíveis para o modelo de confiança chave.
 
## <a name="preview-feedback"></a>Comentários sobre a pré-visualização

Partilhe o seu feedback sobre esta funcionalidade de pré-visualização ou informe os problemas que o utilizam no fórum de feedback da [AD Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Diretório Ativo Azure, consulte [o que é o Diretório Ativo Azure](../fundamentals/active-directory-whatis.md)
