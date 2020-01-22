---
title: Solucionar problemas de arquivos do Azure no Windows | Microsoft Docs
description: Solucionando problemas de arquivos do Azure no Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 97fdb0b243f71701491f2d2424c04dbd19208ef0
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291198"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Solucionar problemas de arquivos do Azure no Windows

Este artigo lista os problemas comuns relacionados a Microsoft Azure arquivos quando você se conecta de clientes Windows. Ele também fornece possíveis causas e resoluções para esses problemas. Além das etapas de solução de problemas neste artigo, você também pode usar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente de cliente do Windows tenha pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo e ajuda a configurar seu ambiente para obter um desempenho ideal. Você também pode encontrar essas informações no [solucionador de problemas de compartilhamentos de arquivos do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece etapas para ajudá-lo com problemas de conexão/mapeamento/montagem de compartilhamentos de arquivos do Azure.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 ao montar um compartilhamento de arquivos do Azure

Ao tentar montar um compartilhamento de arquivos, você pode receber o seguinte erro:

- Ocorreu o erro de sistema 5. Acesso negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canal de comunicação não criptografado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada sistema negociam pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se de um cliente que dá suporte à criptografia SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte-se de uma máquina virtual no mesmo datacenter que a conta de armazenamento do Azure que é usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, erro 67 ou erro 87 ao montar ou desmontar um compartilhamento de arquivos do Azure

Ao tentar montar um compartilhamento de arquivos do local ou de um datacenter diferente, você pode receber os seguintes erros:

- Ocorreu o erro de sistema 53. Caminho de rede não encontrado.
- Ocorreu o erro de sistema 67. Nome da rede não encontrado.
- Ocorreu o erro de sistema 87. Parâmetro incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: a porta 445 está bloqueada

O erro de sistema 53 ou erro de sistema 67 pode ocorrer se a porta 445 de comunicação de saída para um datacenter de arquivos do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se o firewall ou o ISP está bloqueando a porta 445, use a ferramenta [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ou o cmdlet `Test-NetConnection`. 

Para usar o cmdlet `Test-NetConnection`, o módulo Azure PowerShell deve ser instalado, consulte [instalar Azure PowerShell Module](/powershell/azure/install-Az-ps) para obter mais informações. Não se esqueça de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes relevantes para a sua conta de armazenamento.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Se a ligação for bem-sucedida, deverá ver o resultado seguinte:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> O comando acima devolve o endereço IP atual da conta de armazenamento. Não é garantido que este endereço IP permaneça igual e poderá sofrer alterações em qualquer altura. Não codifique este endereço IP em scripts nem numa configuração de firewall.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1-usar Sincronização de Arquivos do Azure
Sincronização de Arquivos do Azure pode transformar seu Windows Server local em um cache rápido do seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Sincronização de Arquivos do Azure funciona na porta 443 e, portanto, pode ser usado como uma solução alternativa para acessar os arquivos do Azure de clientes que têm a porta 445 bloqueada. [Saiba como configurar o sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2 – usar VPN
Ao configurar uma VPN para sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à Internet. Siga as [instruções para configurar a VPN](storage-files-configure-p2s-vpn-windows.md) para acessar os arquivos do Azure do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3-desbloquear a porta 445 com a ajuda do seu ISP/administrador de ti
Trabalhe com seu departamento de ti ou ISP para abrir a porta 445 de saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4-usar ferramentas baseadas na API REST, como o Gerenciador de Armazenamento/PowerShell
Os arquivos do Azure também dão suporte ao REST, além do SMB. O acesso REST funciona pela porta 443 (TCP padrão). Há várias ferramentas que são escritas usando a API REST que possibilitam uma experiência de interface do usuário rica. [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) é um deles. [Baixe e instale o Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se ao compartilhamento de arquivos apoiado pelos arquivos do Azure. Você também pode usar o [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) que também a API REST do usuário.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está habilitado

O erro de sistema 53 ou erro de sistema 87 pode ocorrer se a comunicação NTLMv1 estiver habilitada no cliente. Os Ficheiros do Azure suportam apenas a autenticação NTLMv2. Ter o NTLMv1 ativado cria um cliente menos seguro. Como tal, a comunicação é bloqueada para os Ficheiros do Azure. 

Para determinar se se trata da causa do erro, verifique se a seguinte subchave do registo está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, veja o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tópico no TechNet.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Reverta o valor **LmCompatibilityLevel** para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "não há cota suficiente disponível para processar este comando" ao copiar para um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

O erro 1816 ocorre quando você atinge o limite superior de identificadores abertos simultâneos que são permitidos para um arquivo no computador em que o compartilhamento de arquivos está sendo montado.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tente novamente. Para obter mais informações, consulte [armazenamento do Microsoft Azure lista de verificação de desempenho e escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para exibir identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) do PowerShell.  

Para fechar identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erro "sem acesso" ao navegar para um compartilhamento de arquivos do Azure no portal

Ao navegar até um compartilhamento de arquivos do Azure no portal, você pode receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: sua conta de usuário não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Navegue até a conta de armazenamento onde o compartilhamento de arquivos do Azure está localizado, clique em **controle de acesso (iam)** e verifique se sua conta de usuário tem acesso à conta de armazenamento. Para saber mais, confira [como proteger sua conta de armazenamento com o RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não é possível excluir um arquivo ou diretório em um compartilhamento de arquivos do Azure
Ao tentar excluir um arquivo, você pode receber o seguinte erro:

O recurso especificado está marcado para exclusão por um cliente SMB.

### <a name="cause"></a>Causa
Esse problema normalmente ocorre se o arquivo ou diretório tiver um identificador aberto. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todos os identificadores abertos e o problema continuar ocorrendo, execute o seguinte:

- Use o cmdlet do PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) para exibir identificadores abertos.

- Use o cmdlet do PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) para fechar identificadores abertos. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia de arquivo lenta de e para arquivos do Azure no Windows

Você pode ver um desempenho lento ao tentar transferir arquivos para o serviço de arquivos do Azure.

- Se você não tiver um requisito de tamanho mínimo de e/s específico, recomendamos o uso de 1 MiB como o tamanho de e/s para um desempenho ideal.
-   Se você souber o tamanho final de um arquivo que está sendo estendido com gravações e o software não tiver problemas de compatibilidade quando a parte final não gravada no arquivo contiver zeros, defina o tamanho do arquivo com antecedência em vez de fazer com que cada gravação faça uma gravação de extensão.
-   Use o método de cópia correto:
    -   Use [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre dois compartilhamentos de arquivos.
    -   Use o [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) entre compartilhamentos de arquivos em um computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações sobre o Windows 8.1 ou o Windows Server 2012 R2

Para clientes que executam o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Esse hotfix melhora o desempenho de identificadores de criação e fechamento.

Você pode executar o script a seguir para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix estiver instalado, a seguinte saída será exibida:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão, a partir de dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "Meu Computador" ou "este computador"

Se você mapear um compartilhamento de arquivos do Azure como administrador usando net use, o compartilhamento parece estar ausente.

### <a name="cause"></a>Causa

Por padrão, o explorador de arquivos do Windows não é executado como administrador. Se você executar net use a partir de um prompt de comando administrativo, mapeie a unidade de rede como administrador. Como as unidades mapeadas são centradas no usuário, a conta de usuário que está conectada não exibirá as unidades se elas forem montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando que não seja de administrador. Como alternativa, você pode seguir [Este tópico do TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registro **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando net use falhará se a conta de armazenamento contiver uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comando. Se o nome da sua conta de usuário começar com uma barra, o mapeamento da unidade falhará.

### <a name="solution"></a>Solução

Você pode usar qualquer uma das seguintes etapas para solucionar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Em um arquivo em lotes, você pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Coloque aspas duplas em volta da chave para contornar esse problema, a menos que a barra "Avançar" seja o primeiro caractere. Se for, use o modo interativo e insira sua senha separadamente ou gere novamente as chaves para obter uma chave que não comece com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>O aplicativo ou serviço não pode acessar uma unidade de arquivos do Azure montada

### <a name="cause"></a>Causa

As unidades são montadas por usuário. Se seu aplicativo ou serviço estiver sendo executado em uma conta de usuário diferente daquela que montou a unidade, o aplicativo não verá a unidade.

### <a name="solution"></a>Solução

Use uma das seguintes soluções:

-   Monte a unidade da mesma conta de usuário que contém o aplicativo. Você pode usar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome de usuário e senha do comando net use.
- Use o comando cmdkey para adicionar as credenciais ao Gerenciador de credenciais. Execute-o em uma linha de comando no contexto da conta de serviço, seja por meio de um logon interativo ou usando runas.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapeie o compartilhamento diretamente sem usar uma letra de unidade mapeada. Alguns aplicativos podem não se reconectar à letra da unidade corretamente, portanto, usar o caminho UNC completo pode ser mais confiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, você poderá receber a seguinte mensagem de erro ao executar net use para a conta de serviço de rede/sistema: "ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Ele pode já ter sido encerrado. " Se isso ocorrer, verifique se o nome de usuário que é passado para net use inclui informações de domínio (por exemplo: "[nome da conta de armazenamento]. File. Core. Windows. net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "você está copiando um arquivo em um destino que não oferece suporte à criptografia"

Quando um arquivo é copiado pela rede, o arquivo é descriptografado no computador de origem, transmitido em texto sem formatação e criptografado novamente no destino. No entanto, você pode ver o seguinte erro ao tentar copiar um arquivo criptografado: "você está copiando o arquivo para um destino que não oferece suporte à criptografia".

### <a name="cause"></a>Causa
Esse problema pode ocorrer se você estiver usando o Encrypting File System (EFS). Os arquivos criptografados pelo BitLocker podem ser copiados para os arquivos do Azure. No entanto, os arquivos do Azure não dão suporte ao EFS do NTFS.

### <a name="workaround"></a>Solução
Para copiar um arquivo pela rede, primeiro você deve descriptografá-lo. Utilize um dos métodos seguintes:

- Use o comando **Copy/d** . Ele permite que os arquivos criptografados sejam salvos como arquivos descriptografados no destino.
- Defina a seguinte chave do registro:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Valor = 1

Lembre-se de que a configuração da chave do registro afeta todas as operações de cópia feitas aos compartilhamentos de rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta de arquivos e pastas

### <a name="cause"></a>Causa

Esse problema pode ocorrer se não houver cache suficiente no computador cliente para diretórios grandes.

### <a name="solution"></a>Solução

Para resolver esse problema, ajuste o valor do registro **DirectoryCacheEntrySizeMax** para permitir o armazenamento em cache de listagens de diretórios maiores no computador cliente:

- Local: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Valor Mane: DirectoryCacheEntrySizeMax 
- Tipo de valor: DWORD
 
 
Por exemplo, você pode defini-lo como 0x100000 e ver se o desempenho se tornou melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erro AadDsTenantNotFound ao habilitar a autenticação do AAD DS (serviço de domínio Azure Active Directory) para arquivos do Azure "não é possível localizar locatários ativos com a ID de locatário AAD-Tenant-ID"

### <a name="cause"></a>Causa

O erro AadDsTenantNotFound ocorre quando você tenta [habilitar a autenticação do AAD DS (serviço de domínio Azure Active Directory) para arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) em uma conta de armazenamento em que o [AAD DS (serviço de domínio do AAD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado no locatário do AAD da assinatura associada.  

### <a name="solution"></a>Solução

Habilite o AAD DS no locatário do AAD da assinatura na qual sua conta de armazenamento está implantada. Você precisa de privilégios de administrador do locatário do AAD para criar um domínio gerenciado. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [habilitar Azure Active Directory Domain Services usando o portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Erro ' ocorreu o erro de sistema 1359. Um erro interno ' recebido por meio de acesso SMB a compartilhamentos de arquivos com a autenticação AAD DS (Azure Active Directory Domain Service) habilitada

### <a name="cause"></a>Causa

Erro ' ocorreu o erro de sistema 1359. Um erro interno ' ocorre quando você tenta se conectar ao compartilhamento de arquivos com a autenticação do AAD DS habilitada em um AAD DS com um nome DNS de domínio começando com um caractere numérico. Por exemplo, se o nome DNS do domínio do AAD DS for "1domain", você receberá esse erro ao tentar montar o compartilhamento de arquivos usando as credenciais do AAD. 

### <a name="solution"></a>Solução

No momento, você pode considerar reimplantar seu AAD DS usando um novo nome DNS de domínio que se aplica com as regras abaixo:
- Os nomes não podem começar com um caractere numérico.
- Os nomes devem ter de 3 a 63 caracteres de comprimento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.
