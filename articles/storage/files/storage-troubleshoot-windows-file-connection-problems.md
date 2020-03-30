---
title: Problemas de sessão de problemas do Azure Files no Windows / Microsoft Docs
description: Problemas de resolução de problemas de Ficheiros Azure no Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 17ecc80fee3b024c334b8d36533663f1f3cebe4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136910"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Resolver problemas de Ficheiros do Azure no Windows

Este artigo lista problemas comuns relacionados com o Microsoft Azure Files quando se conecta com clientes do Windows. Fornece igualmente possíveis causas e resoluções para estes problemas. Além dos passos de resolução de problemas neste artigo, também pode utilizar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente do cliente windows tem pré-requisitos corretos. AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionados neste artigo e ajuda a configurar o seu ambiente para obter um desempenho ideal. Também pode encontrar esta informação nos [Ficheiros Azure partilha o Troubleshooter](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece passos para o ajudar com problemas de ligação/mapeamento/montagem de ações do Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 quando monta uma partilha de ficheiros Azure

Quando tentar montar uma partilha de ficheiros, poderá receber o seguinte erro:

- Ocorreu o erro de sistema 5. Acesso negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não encriptado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada sistema negociam pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se a partir de um cliente que suporta a encriptação SMB (Windows 8, Windows Server 2012 ou mais tarde) ou ligue-se a partir de uma máquina virtual no mesmo datacenter que a conta de armazenamento Azure que é usada para a partilha de ficheiros Azure.
2. Verifique se a definição necessária à [transferência segura](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) é desativada na conta de armazenamento se o cliente não suportar a encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: As permissões de nível de partilha são incorretas ao utilizar a autenticação baseada na identidade

Se os utilizadores estiverem a aceder à partilha de ficheiros Azure utilizando a autenticação ative directory (AD) ou Azure Ative Directory Domain Services (Azure AD DS), o acesso à partilha de ficheiros falhará com o erro de "Access is denied" se as permissões de nível de partilha estiverem incorretas. 

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Para atualizar as permissões de nível de partilha, consulte [Aspermissões](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#assign-access-permissions-to-an-identity)de acesso a uma identidade .

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 quando monta ou desmonta uma partilha de ficheiros Azure

Quando tentar montar uma partilha de ficheiros a partir de instalações ou de um centro de dados diferente, poderá receber os seguintes erros:

- Ocorreu o erro de sistema 53. Caminho de rede não encontrado.
- Ocorreu o erro de sistema 67. Nome da rede não encontrado.
- Ocorreu o erro de sistema 87. Parâmetro incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: O porto 445 está bloqueado

Erro do sistema 53 ou erro do sistema 67 pode ocorrer se a comunicação de saída da porta 445 para um centro de dados Do Ficheiro Sino Do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se a sua firewall ou ISP está a bloquear a `Test-NetConnection` porta 445, utilize a ferramenta [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ou cmdlet. 

Para utilizar `Test-NetConnection` o cmdlet, o módulo PowerShell Azure tem de ser instalado, consulte instalar o [módulo PowerShell Azure](/powershell/azure/install-Az-ps) para obter mais informações. Não se esqueça de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes relevantes para a sua conta de armazenamento.

   
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

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1 – Utilize o Azure File Sync
O Azure File Sync pode transformar o seu Windows Server no local numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. O Azure File Sync funciona sobre a porta 443 e pode, assim, ser usado como salção de acesso a Ficheiros Azure de clientes que tenham a porta 445 bloqueada. [Saiba como configurar o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2 – Utilizar a VPN
Ao configurar uma VPN na sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à internet. Siga as [instruções para configurar vpN](storage-files-configure-p2s-vpn-windows.md) para aceder a Ficheiros Azure a partir do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3 – Desbloqueie a porta 445 com a ajuda do ISP/Administrador de TI
Trabalhe com o seu departamento de TI ou ISP para abrir as gamas 445 de saída para [o Azure IP.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – Utilize ferramentas com base na API REST como o Explorador de Armazenamento/Powershell
O Azure Files também suporta o REST para além do SMB. O acesso ao REST funciona sobre a porta 443 (tcp padrão). Existem várias ferramentas que são escritas usando a API REST que permitem a rica experiência de UI. [O Explorador](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) de Armazenamento é um deles. [Descarregue e instale](https://azure.microsoft.com/features/storage-explorer/) o Storage Explorer e ligue-se à sua partilha de ficheiros apoiada por Ficheiros Azure. Também pode utilizar [o PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) que também utiliza a API REST.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está ativado

Erro do sistema 53 ou erro do sistema 87 pode ocorrer se a comunicação NTLMv1 estiver ativada no cliente. Os Ficheiros do Azure suportam apenas a autenticação NTLMv2. Ter o NTLMv1 ativado cria um cliente menos seguro. Como tal, a comunicação é bloqueada para os Ficheiros do Azure. 

Para determinar se se trata da causa do erro, verifique se a seguinte subchave do registo está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, veja o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tópico no TechNet.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Reverta o valor **LmCompatibilityLevel** para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "Não há quota suficiente disponível para processar este comando" quando copia para uma partilha de ficheiros Azure

### <a name="cause"></a>Causa

O erro 1816 acontece quando se atinge o limite superior das pegas abertas simultâneas que são permitidas para um ficheiro no computador onde a parte do ficheiro está a ser montada.

### <a name="solution"></a>Solução

Reduza o número de pegas abertas simultâneas fechando algumas pegas e, em seguida, retente novamente. Para mais informações, consulte o desempenho do [Microsoft Azure Storage e](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)a lista de verificação de escalabilidade .

Para visualizar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Para fechar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Os cmdlets de cmdlets de comando Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "Sem acesso" quando tenta aceder ou eliminar uma Partilha de Ficheiros Azure  
Quando tentar aceder ou eliminar uma parte de ficheiro Azure no portal, poderá receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: A sua conta de utilizador não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Navegue na conta de armazenamento onde está localizada a partilha de ficheiros Azure, clique no controlo de **acesso (IAM)** e verifique se a sua conta de utilizador tem acesso à conta de armazenamento. Para saber mais, consulte como proteger a sua conta de [armazenamento com o Controlo de Acesso baseado em Funções (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não consigo eliminar um ficheiro ou um diretório numa partilha de ficheiros do Azure
Quando tentar apagar um ficheiro, poderá receber o seguinte erro:

O recurso especificado é marcado para eliminação por um cliente SMB.

### <a name="cause"></a>Causa
Este problema ocorre normalmente se o ficheiro ou diretório tiver uma pega aberta. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todas as pegas abertas e o problema continuar a ocorrer, execute o seguinte:

- Utilize o cmdlet [PowerShell Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell para visualizar as pegas abertas.

- Utilize o cmdlet [PowerShell Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell para fechar as pegas abertas. 

> [!Note]  
> Os cmdlets de cmdlets de comando Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia lenta de ficheiros para e de Ficheiros do Azure no Windows

Pode ver um desempenho lento quando tentar transferir ficheiros para o serviço De Ficheiros Azure.

- Se não tiver um requisito de tamanho mínimo de I/O específico, recomendamos que utilize 1 MiB como tamanho de I/S para um desempenho ótimo.
-   Se souber o tamanho final de um ficheiro que está a estender com as escritas, e o seu software não tiver problemas de compatibilidade quando a cauda não escrita no ficheiro contém zeros, então detete o tamanho do ficheiro com antecedência em vez de fazer com que cada escreva uma escrita extensiva.
-   Utilize o método de cópia certo:
    -   Utilize o [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas ações de ficheiro.
    -   Use [robocópia](/azure/storage/files/storage-files-deployment-guide#robocopy) entre as ações de ficheiros num computador no local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para os clientes que estão a executar o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que o [hotfix KB3114025](https://support.microsoft.com/help/3114025) está instalado. Este hotfix melhora o desempenho de criar e fechar as pegas.

Pode executar o seguinte script para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se for instalada a correção de calor, é visualizada a seguinte saída:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens R2 do Windows Server 2012 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão, a partir de dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "My Computer" ou "This PC"

Se mapear uma partilha de ficheiros Azure como administrador utilizando o uso líquido, a parte parece estar em falta.

### <a name="cause"></a>Causa

Por predefinição, o Windows File Explorer não funciona como administrador. Se executar o uso líquido a partir de um pedido de comando administrativo, mapeie a unidade de rede como administrador. Uma vez que as unidades mapeadas são centradas no utilizador, a conta de utilizador que está acesada não exibe as unidades se forem montadas numa conta de utilizador diferente.

### <a name="solution"></a>Solução
Monte a parte de uma linha de comando não administrador. Em alternativa, pode seguir [este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor de registo **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando de utilização líquida falha se a conta de armazenamento contiver um corte para a frente

### <a name="cause"></a>Causa

O comando de utilização da rede interpreta um corte dianteiro (/) como uma opção de linha de comando. Se o nome da conta de utilizador começar com um corte dianteiro, o mapeamento de unidade falha.

### <a name="solution"></a>Solução

Pode utilizar qualquer um dos seguintes passos para contornar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A partir de um ficheiro de lote, pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Coloque duas marcas de citação em torno da chave para contornar este problema -- a menos que o corte dianteiro seja o primeiro personagem. Se for, utilize o modo interativo e introduza a sua palavra-passe separadamente ou regeneraas as teclas para obter uma chave que não comece com um corte dianteiro.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplicação ou serviço não pode aceder a uma unidade montada de Ficheiros Azure

### <a name="cause"></a>Causa

As unidades são montadas por utilizador. Se a sua aplicação ou serviço estiver a funcionar sob uma conta de utilizador diferente da que montou a unidade, a aplicação não verá a unidade.

### <a name="solution"></a>Solução

Utilize uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de utilizador que contém a aplicação. Pode utilizar uma ferramenta como a PsExec.
- Passe o nome da conta de armazenamento e a chave no nome do utilizador e parâmetros de senha do comando de utilização líquida.
- Utilize o comando cmdkey para adicionar as credenciais ao Credential Manager. Execute isto a partir de uma linha de comando sob o `runas`contexto da conta de serviço, seja através de um login interativo ou utilizando .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapeie a parte diretamente sem usar uma letra de unidade mapeada. Algumas aplicações podem não voltar a ligar-se corretamente à letra de unidade, pelo que a utilização do caminho completo do CNU pode ser mais fiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, poderá receber a seguinte mensagem de erro quando executar a utilização líquida para a conta de serviço de sistema/rede: "Ocorreu um erro do sistema 1312. Não existe uma sessão de logon especificada. Pode já ter sido encerrado. Se isto ocorrer, certifique-se de que o nome de utilizador que é passado para a utilização líquida inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Está a copiar um ficheiro para um destino que não suporta encriptação"

Quando um ficheiro é copiado através da rede, o ficheiro é desencriptado no computador de origem, transmitido em texto simples e reencriptado no destino. No entanto, pode ver o seguinte erro quando está a tentar copiar um ficheiro encriptado: "Está a copiar o ficheiro para um destino que não suporta encriptação."

### <a name="cause"></a>Causa
Este problema pode ocorrer se estiver a utilizar o Sistema de Ficheiros encriptadores (EFS). Os ficheiros encriptados bitLocker podem ser copiados para ficheiros Azure. No entanto, o Azure Files não suporta o NTFS EFS.

### <a name="workaround"></a>Solução
Para copiar um ficheiro sobre a rede, tem primeiro de o desencriptar. Utilize um dos seguintes métodos:

- Utilize o comando **de cópia/d.** Permite que os ficheiros encriptados sejam guardados como ficheiros desencriptados no destino.
- Desprete a seguinte chave de registo:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valor = 1

Esteja ciente de que a definição da chave de registo afeta todas as operações de cópia que são feitas às ações da rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta de ficheiros e pastas

### <a name="cause"></a>Causa

Este problema pode ocorrer se não houver cache suficiente na máquina de clientes para grandes diretórios.

### <a name="solution"></a>Solução

Para resolver este problema, ajustando o valor do registo **Do DirectoryCacheEntrySizeMax** para permitir o cacheching de listas de diretórios maiores na máquina cliente:

- Localização: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Value mane: DirectoryCacheEntrySizeMax 
- Tipo de valor:DWORD
 
 
Por exemplo, pode defini-lo em 0x100000 e ver se o desempenho se torna melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erro AadDsTenantNotFound ao permitir a autenticação do Serviço de Domínio ativo do Azure (AAD DS) para ficheiros Azure "Incapaz de localizar inquilinos ativos com inquilino-inquilino-id"

### <a name="cause"></a>Causa

Erro AadDsTenantNotFound acontece quando tenta ativar a autenticação dos Serviços de Domínio de [Diretório Ativo azure (Azure AD DS) em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) numa conta de armazenamento onde o [AAD Domain Service (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado no inquilino AAD da subscrição associada.  

### <a name="solution"></a>Solução

Ative a AAD DS no inquilino AAD da subscrição para a a que a sua conta de armazenamento está implantada. Você precisa de privilégios de administrador do inquilino da AAD para criar um domínio gerido. Se não for o administrador do inquilino da AD Azure, contacte o administrador e siga a orientação passo a passo para [enable Azure Ative Directory Domain Services utilizando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Erro 'Erro do sistema 1359 ocorreu. Um erro interno' recebido sobre o acesso da SMB a ações de ficheirocom a autenticação do Serviço de Domínio ativo do Azure (AAD DS) habilitado

### <a name="cause"></a>Causa

Erro 'Erro do sistema 1359 ocorreu. Um erro interno ocorre quando tenta ligar-se à sua partilha de ficheiros com a autenticação AAD DS ativada contra um DS AAD com o nome DNS de domínio a partir de um carácter numérico. Por exemplo, se o seu nome DNS de domínio AAD DS for "1domínio", terá este erro ao tentar montar a partilha de ficheiros utilizando credenciais AAD. 

### <a name="solution"></a>Solução

Atualmente, pode considerar recolocar o seu AAD DS usando um novo nome DNS de domínio que se aplica com as regras abaixo:
- Os nomes não podem começar com um carácter numérico.
- Os nomes devem ter 3 a 63 caracteres de comprimento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema rapidamente.
