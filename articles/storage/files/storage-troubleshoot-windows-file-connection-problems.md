---
title: Resolução de problemas de ficheiros do Azure no Windows | Documentos da Microsoft
description: Resolução de problemas de ficheiros do Azure no Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 41eed6bc878bff4c9d847f9a449ca693274bf234
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195511"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Resolução de problemas de ficheiros do Azure no Windows

Este artigo lista problemas comuns relacionados com ficheiros do Microsoft Azure quando se liga a partir de clientes do Windows. Ele também fornece possíveis causas e resoluções para esses problemas. Além dos passos de resolução de problemas neste artigo, pode também usar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente de cliente do Windows tem pré-requisitos corretos. AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionados neste artigo e ajuda a configurar o ambiente para obter um desempenho ideal. Também pode encontrar estas informações no [solucionador de problemas de partilhas de ficheiros do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece os passos para ajudá-lo com problemas de partilhas de ficheiros do Azure de ligar/mapeamento/montagem.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro de 5 ao montar uma partilha de ficheiros do Azure

Ao tentar montar uma partilha de ficheiros, pode receber o erro seguinte:

- Ocorreu um erro de sistema 5. Acesso negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Fazer com que 1: Canal de comunicação sem encriptação

Por motivos de segurança, as ligações a partilhas de ficheiros do Azure são bloqueadas se o canal de comunicação não é encriptado e se a tentativa de ligação não é feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. Conexões não criptografadas no mesmo datacenter podem também ser bloqueados se o [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição está ativada na conta de armazenamento. Um canal de comunicação encriptado é fornecido apenas se o SO de cliente do usuário oferece suporte a encriptação SMB.

Windows 8, Windows Server 2012 e versões posteriores de cada sistema negociam pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para causa 1

1. Ligar a partir de um cliente que suporte a encriptação SMB (Windows 8, Windows Server 2012 ou posterior) ou ligar a partir de uma máquina virtual no mesmo datacenter que a conta de armazenamento do Azure que é utilizada para a partilha de ficheiros do Azure.
2. Verifique se o [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição estiver desativada na conta de armazenamento se o cliente não suporta a encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: Regras de firewall ou de rede virtual estão ativadas na conta de armazenamento 

Se a rede virtual (VNET) e regras de firewall estão configuradas na conta de armazenamento, o tráfego de rede será negado acesso a menos que o endereço IP do cliente ou a rede virtual tem permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para causa 2

Certifique-se de que as regras de rede e de firewall virtual estiverem configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual está a causar o problema, alterar temporariamente a definição da conta de armazenamento para **permitir o acesso de todas as redes**. Para obter mais informações, consulte [armazenamento do Azure configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, erro 67 ou erro 87 ao montar ou desmontar uma partilha de ficheiros do Azure

Ao tentar montar uma partilha de ficheiros no local ou a partir de um centro de dados diferente, pode receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho de rede não foi encontrado.
- Ocorreu um erro de sistema 67. Não é possível localizar o nome da rede.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Fazer com que 1: A porta 445 está bloqueada

Erro de sistema 53 ou erro 67 do sistema pode ocorrer se a porta 445 comunicação saída para um centro de dados de ficheiros do Azure está bloqueada. Para ver o resumo de ISPs que permitem ou não o acesso de porta 445, aceda à [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se a firewall ou um ISP está a bloquear a porta 445, utilize o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ferramenta ou `Test-NetConnection` cmdlet. 

Para utilizar o `Test-NetConnection` cmdlet, o AzureRM PowerShell módulo tem de estar instalado, consulte [módulo de instalar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) para obter mais informações. Não se esqueça de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes relevantes para a sua conta de armazenamento.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
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

### <a name="solution-for-cause-1"></a>Solução para causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1 - utilização do Azure File Sync
O Azure File Sync pode transforma o seu servidor do Windows no local numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. O Azure File Sync funciona através da porta 443 e, portanto, pode ser utilizado como uma solução alternativa para aceder aos ficheiros do Azure de clientes que tenham a porta 445 bloqueado. [Saiba como configurar o Azure File Sync](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2 – utilização VPN
Ao configurar uma VPN para a sua conta de armazenamento específico, o tráfego passará por meio de um túnel seguro, em vez de através da internet. Siga os [instruções para configurar a VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) para aceder aos ficheiros do Azure do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução de 3 - desbloquear a porta 445, com a ajuda de seu ISP / administrador de TI
Trabalhar com o seu departamento de TI ou um ISP para abrir a porta 445 de saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 - utilizar a API REST com base em ferramentas como o Explorer/Powershell do armazenamento
Os ficheiros do Azure também suporta REST, além de SMB. Acesso REST funciona através da porta 443 (tcp padrão). Existem várias ferramentas que são escritas usando a REST API que permitem a Rica experiência de interface do Usuário. [Explorador de armazenamento](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) é uma delas. [Transferir e instalar o Explorador de armazenamento](https://azure.microsoft.com/en-us/features/storage-explorer/) e ligar à partilha de ficheiros de cópia de ficheiros do Azure. Também pode utilizar [PowerShell](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-powershell) que utilizador também a REST API.


### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está ativada

Erro de sistema 53 ou erro de sistema 87 pode ocorrer se a comunicação de NTLMv1 estiver ativada no cliente. Ficheiros do Azure suportam apenas a autenticação NTLMv2. Ter NTLMv1 ativado cria um cliente menos seguras. Por conseguinte, a comunicação é bloqueada para ficheiros do Azure. 

Para determinar se se trata a causa do erro, certifique-se de que a seguinte subchave do registo está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, consulte a [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tópico no TechNet.

### <a name="solution-for-cause-2"></a>Solução para causa 2

Reverter o **LmCompatibilityLevel** valor para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "quota não suficiente está disponível para processar este comando" quando copiar para uma partilha de ficheiros do Azure

### <a name="cause"></a>Causa

Ocorre um erro 1816 quando atingir o limite superior de identificadores abertos em simultâneo permitidos para um ficheiro no computador onde a partilha de ficheiros está sendo montada.

### <a name="solution"></a>Solução

Reduzir o número de identificadores abertos em simultâneo por fechar alguns identificadores e tente novamente. Para obter mais informações, consulte [lista de verificação de armazenamento do Microsoft Azure, desempenho e escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erro "Acesso negado" ao navegar para uma partilha de ficheiros do Azure no portal

Ao navegar para uma partilha de ficheiros do Azure no portal, poderá receber o erro seguinte:

Acesso negado  
Não tem acesso  
Parece que não tem acesso a este conteúdo. Para obter acesso, contacte o proprietário.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Fazer com que 1: Sua conta de utilizador não tem acesso à conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para causa 1

Navegue para a conta de armazenamento onde está localizada a partilha de ficheiros do Azure, clique em **controlo de acesso (IAM)** e certifique-se a sua conta de utilizador tem acesso à conta de armazenamento. Para obter mais informações, consulte [como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: Regras de firewall ou de rede virtual estão ativadas na conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para causa 2

Certifique-se de que as regras de rede e de firewall virtual estiverem configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual está a causar o problema, alterar temporariamente a definição da conta de armazenamento para **permitir o acesso de todas as redes**. Para obter mais informações, consulte [armazenamento do Azure configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Tornar mais lenta de copiar o arquivo de e para ficheiros do Azure no Windows

Poderá ver um desempenho lento ao tentar transferir ficheiros para o serviço de ficheiros do Azure.

- Se não tiver um requisito de tamanho de e/s mínimo específico, recomendamos que utilize 1 MiB como o tamanho de e/s para otimizar o desempenho.
-   Se conhece o tamanho final de um ficheiro que estiver a expandir com gravações e seu software não tem problemas de compatibilidade quando a cauda implícitas no arquivo contém zeros, em seguida, defina o tamanho do ficheiro com antecedência, em vez de fazer uma gravação de expansão de cada gravação.
-   Utilize o método de cópia certo:
    -   Uso [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas partilhas de ficheiros.
    -   Uso [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre partilhas de ficheiros num computador no local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para o Windows 8.1 ou Windows Server 2012 R2

Para os clientes que estejam a executar o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que o [KB3114025](https://support.microsoft.com/help/3114025) correção está instalada. Esta correção melhora o desempenho de criar e fechar identificadores.

Pode executar o script seguinte para verificar se a correção foi instalada:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se a correção está instalada, é apresentada a seguinte saída:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Imagens do Windows Server 2012 R2 no Azure Marketplace tem correção KB3114025 instalado por predefinição, a partir de Dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nenhuma pasta com uma letra de unidade no **meu computador**

Se mapear uma partilha de ficheiros do Azure como administrador com a utilização de rede, o compartilhamento parece estar em falta.

### <a name="cause"></a>Causa

Por predefinição, o Explorador de ficheiros do Windows não é executado como administrador. Se executar o net use a partir de uma linha de comandos administrativa, mapear a unidade de rede como administrador. Como as unidades mapeadas são centrada no utilizador, a conta de utilizador que tem sessão iniciada não apresenta as unidades se eles estão montados numa conta de utilizador diferente.

### <a name="solution"></a>Solução
Monte a partilha a partir de uma linha de comando não-administrador. Em alternativa, pode seguir [este tópico do TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o **EnableLinkedConnections** valor de registo.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Comando net use falha se a conta de armazenamento contém uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comandos. Se o nome da sua conta de utilizador for iniciado com uma barra, o mapeamento de unidade falha.

### <a name="solution"></a>Solução

Pode utilizar qualquer um dos seguintes passos para resolver o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  De um arquivo de lote, pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Coloque as aspas à volta a chave para solucionar este problema, a menos que a barra é o primeiro caráter. Se for, utilize o modo interativo e introduza a palavra-passe em separado ou regenerar as chaves para obter uma chave que não começa com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplicação ou serviço não é possível aceder uma unidade de ficheiros do Azure montada

### <a name="cause"></a>Causa

Unidades estão montadas por utilizador. Se a sua aplicação ou serviço está em execução sob uma conta de utilizador diferente daquela que montar a unidade, o aplicativo não verão a unidade.

### <a name="solution"></a>Solução

Utilize uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de utilizador, que contém a aplicação. Pode usar uma ferramenta como o PsExec.
- Transmita o nome da conta de armazenamento e a chave o nome de utilizador e os parâmetros de palavra-passe do net use o comando.
- Utilize o comando cmdkey para adicionar as credenciais no Gestor de credenciais. Fazer a partir de uma linha de comandos no contexto de conta de serviço, por meio de um início de sessão interativo ou através de runas.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapear a partilha diretamente sem utilizar uma letra de unidade mapeada. Alguns aplicativos podem não restabelece ligação para a letra de unidade corretamente, para que utilizar o caminho UNC completo pode ser mais fiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, pode receber a seguinte mensagem de erro ao executar o net use para a conta de serviço do sistema/rede: "Erro de sistema 1312 ocorreu. Uma sessão de início de sessão especificado não existe. Ele já foi foi terminado." Se isto ocorrer, certifique-se de que o nome de utilizador que é passada para net utilização inclui informações de domínio (por exemplo: "[nome da conta de armazenamento]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Está a copiar um ficheiro para um destino que não suporta encriptação"

Quando um ficheiro é copiado através da rede, o ficheiro é desencriptado no computador de origem, transmitido em texto não criptografado e encriptado novamente no destino. No entanto, poderá ver o seguinte erro quando está a tentar copiar um arquivo criptografado: "Está a copiar o ficheiro para um destino que não suporta a encriptação."

### <a name="cause"></a>Causa
Este problema pode ocorrer se estiver a utilizar o Encrypting File System (EFS). Ficheiros encriptados por BitLocker podem ser copiados para ficheiros do Azure. No entanto, os ficheiros do Azure não suporta EFS de NTFS.

### <a name="workaround"></a>Solução
Para copiar um ficheiro através da rede, tem de desencriptá-lo primeiro. Utilize um dos seguintes métodos:

- Utilize o **copiar /d** comando. Ele permite que os ficheiros encriptados para serem guardados como ficheiros desencriptados no destino.
- Defina a seguinte chave de registo:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor DWORD de =
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

Lembre-se de que definir a chave do registo afeta todas as operações de cópia que são feitas a compartilhamentos de rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta de ficheiros e pastas

### <a name="cause"></a>Causa

Este problema pode ocorrer se existir sem cache suficiente no computador cliente para grandes diretórios.

### <a name="solution"></a>Solução

Para resolver este problema, ajustar a **DirectoryCacheEntrySizeMax** valor de registo para permitir a colocação em cache de listagens de diretórios de maior no computador cliente:

- Localização: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Mane de valor: DirectoryCacheEntrySizeMax 
- Tipo de valor: DWORD
 
 
Por exemplo, pode defini-lo como 0x100000 e ver se o desempenho se tornar melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>AadDsTenantNotFound de erro na ativação da autenticação do Azure Active Directory para ficheiros do Azure "Não é possível localizar o Active Directory inquilinos com Id aad-tenant-id de inquilino"

### <a name="cause"></a>Causa

Erro AadDsTenantNotFound acontece quando tentar [ativar a autenticação do Azure Active Directory (AAD) para ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) numa conta de armazenamento onde [AAD domínio Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado sobre o AAD inquilino da subscrição associada.  

### <a name="solution"></a>Solução

Ative DS do AAD no inquilino do AAD da subscrição que a sua conta de armazenamento está implementada. São necessários privilégios de administrador de inquilino do AAD para criar um domínio gerido. Se não o administrador de inquilino do Azure AD, contacte o administrador e siga as orientações passo a passo para [ativar o Azure Active Directory Domain Services no portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.
