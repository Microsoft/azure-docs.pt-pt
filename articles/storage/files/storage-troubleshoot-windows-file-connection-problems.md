---
title: Resolução de problemas Azure Files problemas no Windows Microsoft Docs
description: Problemas de resolução de problemas no Azure Files no Windows
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e855ed169a0c4eca7dda696c03deedb9e519e9bf
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259979"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Resolver problemas de Ficheiros do Azure no Windows

Este artigo lista problemas comuns relacionados com os Ficheiros Azure do Microsoft quando se conecta a partir de clientes Windows. Fornece igualmente possíveis causas e resoluções para estes problemas. Além das etapas de resolução de problemas neste artigo, também pode utilizar [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)   para garantir que o ambiente do cliente windows tem requisitos corretos. A AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionados neste artigo e ajuda a configurar o seu ambiente para obter o melhor desempenho. Também pode encontrar esta informação no [Azure Files partilha o Troubleshooter](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece passos para o ajudar com problemas de ligação/mapeamento/montagem de ações do Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 quando monta uma partilha de ficheiros Azure

Quando tentar montar uma partilha de ficheiros, poderá receber o seguinte erro:

- Ocorreu o erro de sistema 5. Acesso negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não encriptado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada sistema negociam pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se a partir de um cliente que suporte encriptação SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte-se a partir de uma máquina virtual no mesmo centro de dados que a conta de armazenamento Azure que é usada para a partilha de ficheiros Azure.
2. Verifique se a [definição de transferência necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) para a transferência segura está desativada na conta de armazenamento se o cliente não suportar encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: As permissões de nível de partilha estão incorretas quando se utiliza a autenticação baseada na identidade

Se os utilizadores estiverem a aceder à partilha de ficheiros Azure utilizando a autenticação Ative Directory (AD) ou a Azure Ative Directory Domain Services (Azure AD DS), o acesso à partilha de ficheiros falhará com o erro de "Acesso é negado" se as permissões de nível de partilha estiverem incorretas. 

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Para atualizar as permissões de nível de partilha, consulte [atribuir permissões](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)de acesso a uma identidade .

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 quando monta ou desmonta uma partilha de ficheiros Azure

Quando tentar montar uma partilha de ficheiros a partir do local ou de um centro de dados diferente, poderá receber os seguintes erros:

- Ocorreu o erro de sistema 53. Caminho de rede não encontrado.
- Ocorreu o erro de sistema 67. Nome da rede não encontrado.
- Ocorreu o erro de sistema 87. Parâmetro incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: Porto 445 está bloqueado

Pode ocorrer um erro do sistema 53 ou um erro do sistema 67 se a comunicação de saída da porta 445 a um centro de dados do Azure Files estiver bloqueada. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se a sua firewall ou ISP está a bloquear a porta 445, utilize a ferramenta [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) ou `Test-NetConnection` cmdlet. 

Para utilizar o `Test-NetConnection` cmdlet, o módulo Azure PowerShell tem de ser instalado, consulte [o módulo Install Azure PowerShell](/powershell/azure/install-Az-ps) para obter mais informações. Não se esqueça de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes relevantes para a sua conta de armazenamento.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Se a ligação for bem-sucedida, deverá ver o resultado seguinte:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> O comando acima devolve o endereço IP atual da conta de armazenamento. Não é garantido que este endereço IP permaneça igual e poderá sofrer alterações em qualquer altura. Não codifique este endereço IP em scripts nem numa configuração de firewall.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1 – Utilize o Azure File Sync
O Azure File Sync pode transformar o seu Windows Server no local numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. O Azure File Sync funciona na porta 443 e pode assim servir como uma solução alternativa para aceder aos Ficheiros do Azure de clientes que tenham a porta 445 bloqueada. [Saiba como configurar o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2 – Utilizar a VPN
Ao configurar uma VPN na sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à internet. Siga as [instruções para configurar a VPN](storage-files-configure-p2s-vpn-windows.md) para aceder aos Ficheiros do Azure a partir do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3 – Desbloqueie a porta 445 com a ajuda do ISP/Administrador de TI
Trabalhe com o seu departamento de TI ou ISP para abrir as [gamas Azure IP](https://www.microsoft.com/download/details.aspx?id=41653)da porta 445.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – Utilize ferramentas com base na API REST como o Explorador de Armazenamento/Powershell
O Azure Files também suporta o REST para além do SMB. O acesso à REST funciona na porta 443 (TCP padrão). Existem várias ferramentas que são escritas através da API REST que permitem uma experiência da IU avançada. [O Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) é um deles. [Transfira e Instale o Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e ligue-se à partilha de ficheiros suportada pelos Ficheiros do Azure. Também pode utilizar [o PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) que também utiliza a API REST do utilizador.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está ativado

O erro do sistema 53 ou o erro do sistema 87 podem ocorrer se a comunicação NTLMv1 estiver ativada no cliente. Os Ficheiros do Azure suportam apenas a autenticação NTLMv2. Ter o NTLMv1 ativado cria um cliente menos seguro. Como tal, a comunicação é bloqueada para os Ficheiros do Azure. 

Para determinar se se trata da causa do erro, verifique se a seguinte subchave do registo está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, veja o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tópico no TechNet.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Reverta o valor **LmCompatibilityLevel** para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Erro 1816 - Não há quota suficiente para processar este comando

### <a name="cause"></a>Causa

O erro 1816 acontece quando se atinge o limite superior de pegas abertas simultâneas que são permitidas para um ficheiro ou diretório na partilha de ficheiros Azure. Para obter mais informações, veja [Alvos de escala dos Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets).

### <a name="solution"></a>Solução

Reduza o número de pegas abertas simultâneas fechando algumas pegas e, em seguida, redaça. Para obter mais informações, consulte [o desempenho do Microsoft Azure Storage e a lista de verificação de escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para visualizar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Para fechar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "Não acesso" quando tenta aceder ou apagar uma Partilha de Ficheiros Azure  
Quando tentar aceder ou eliminar uma partilha de ficheiros Azure no portal, poderá receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: A sua conta de utilizador não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Navegue na conta de armazenamento onde está localizada a partilha de ficheiros Azure, clique no **controlo de acesso (IAM)** e verifique se a sua conta de utilizador tem acesso à conta de armazenamento. Para saber mais, consulte [como garantir a sua conta de armazenamento com o Controle de Acesso Baseado em Fun (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não consigo eliminar um ficheiro ou um diretório numa partilha de ficheiros do Azure
Quando tentar apagar um ficheiro, poderá receber o seguinte erro:

O recurso especificado está marcado para eliminação por um cliente SMB.

### <a name="cause"></a>Causa
Este problema ocorre normalmente se o ficheiro ou o diretório tão abertos mentem. 

### <a name="solution"></a>Solução

Se os clientes SMB encerrarem todas as pegas abertas e o problema continuar a ocorrer, execute o seguinte:

- Utilize o [cmdlet Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell para visualizar as pegas abertas.

- Utilize o [cmdlet Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell para fechar as pegas abertas. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia lenta de ficheiros para e de Ficheiros do Azure no Windows

Poderá ver um desempenho lento quando tentar transferir ficheiros para o serviço Azure File.

- Se não tiver um requisito específico de tamanho mínimo de E/S, recomendamos que utilize 1 MiB como o tamanho de E/S para um desempenho ótimo.
-   Se souber o tamanho final de um ficheiro que está a estender com as escritas, e o seu software não tiver problemas de compatibilidade quando a cauda não escrita no ficheiro contiver zeros, então descreva o tamanho do ficheiro com antecedência em vez de fazer cada escrita uma escrita alargada.
-   Utilize o método de cópia certo:
    -   Utilize [a AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas ações de ficheiro.
    -   Utilize [robocopia](/azure/storage/files/storage-files-deployment-guide#robocopy) entre ações de ficheiros num computador no local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para o Windows 8.1 ou Windows Server 2012 R2

Para os clientes que estão a executar o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Este hotfix melhora o desempenho de criar e fechar as pegas.

Pode executar o seguinte script para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix estiver instalado, é apresentada a seguinte saída:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens R2 do Windows Server 2012 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão, a partir de dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "My Computer" ou "This PC"

Se mapear uma partilha de ficheiros Azure como administrador utilizando a utilização líquida, a parte parece estar em falta.

### <a name="cause"></a>Causa

Por predefinição, o Windows File Explorer não funciona como administrador. Se executar a utilização da rede a partir de um pedido de comando administrativo, mapeia a unidade de rede como administrador. Como as unidades mapeadas são centradas no utilizador, a conta de utilizador que está iniciada não exibe as unidades se forem montadas numa conta de utilizador diferente.

### <a name="solution"></a>Solução
Monte a parte de uma linha de comando não administrador. Em alternativa, pode seguir [este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registo **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando de utilização líquida falha se a conta de armazenamento contiver uma barra para a frente

### <a name="cause"></a>Causa

O comando de utilização da rede interpreta um corte dianteiro (/) como uma opção de linha de comando. Se o nome da sua conta de utilizador começar com um corte dianteiro, o mapeamento da unidade falha.

### <a name="solution"></a>Solução

Pode utilizar qualquer um dos seguintes passos para contornar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A partir de um ficheiro de lote, pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Coloque as aspas duplas à volta da chave para contornar este problema, a menos que o corte dianteiro seja o primeiro caracter. Se for, utilize o modo interativo e introduza a sua palavra-passe separadamente ou regenerar as suas chaves para obter uma chave que não comece com um corte dianteiro.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>A aplicação ou serviço não pode aceder a uma unidade de Ficheiros Azure montados

### <a name="cause"></a>Causa

As unidades são montadas por utilizador. Se a sua aplicação ou serviço estiver a funcionar numa conta de utilizador diferente da que montou a unidade, a aplicação não verá a unidade.

### <a name="solution"></a>Solução

Utilize uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de utilizador que contém a aplicação. Pode utilizar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome de utilizador e palavra-passe do comando de utilização da rede.
- Utilize o comando cmdkey para adicionar as credenciais ao Gestor credencial. Execute-o a partir de uma linha de comando no contexto da conta de serviço, seja através de um login interativo ou através da utilização `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapear a partilha diretamente sem usar uma letra de unidade mapeada. Algumas aplicações podem não voltar a ligar-se adequadamente à letra de unidade, pelo que a utilização de todo o caminho unc pode ser mais fiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, poderá receber a seguinte mensagem de erro quando executar a utilização líquida para a conta de serviço sistema/rede: "Ocorreu o erro do sistema 1312. Não existe uma sessão de início de sessão especificada. Pode já ter sido encerrado. Se isso ocorrer, certifique-se de que o nome de utilizador que é transmitido para uso líquido inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Está a copiar um ficheiro para um destino que não suporta encriptação"

Quando um ficheiro é copiado através da rede, o ficheiro é desencriptado no computador de origem, transmitido em texto simples, e reencrimado no destino. No entanto, pode ver o seguinte erro quando está a tentar copiar um ficheiro encriptado: "Está a copiar o ficheiro para um destino que não suporta encriptação."

### <a name="cause"></a>Causa
Este problema pode ocorrer se estiver a utilizar o Sistema de Ficheiros encriptadores (EFS). Os ficheiros encriptados bitLocker podem ser copiados para ficheiros Azure. No entanto, a Azure Files não suporta o EFES NTFS.

### <a name="workaround"></a>Solução
Para copiar um ficheiro pela rede, tem primeiro de o desencriptar. Utilizar um dos seguintes métodos:

- Utilize o comando **copy /d.** Permite que os ficheiros encriptados sejam guardados como ficheiros desencriptados no destino.
- Defina a chave de registo seguinte:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valor = 1

Esteja ciente de que a definição da chave de registo afeta todas as operações de cópia que são feitas para ações de rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta de ficheiros e pastas

### <a name="cause"></a>Causa

Este problema pode ocorrer se não houver cache suficiente na máquina do cliente para grandes diretórios.

### <a name="solution"></a>Solução

Para resolver este problema, ajustando o valor do registo **DirectoryCacheEntrySizeMax** para permitir a caching de listas de diretórios maiores na máquina do cliente:

- Localização: HKLM\System\CCS\Services\Lanmanworkstation\Parâmetros
- Mane de valor: DirectoryCacheEntrySizeMax 
- Tipo de valor:DWORD
 
 
Por exemplo, pode defini-lo para 0x100000 e ver se o desempenho melhora.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Error AadDsTenantNotFound em permitir a autenticação do Azure Ative Directory Domain Service (AAD DS) para ficheiros Azure "Incapaz de localizar inquilinos ativos com inquilinos Id aad-tenant-id"

### <a name="cause"></a>Causa

Error AadDsTenantNotFound acontece quando tenta ativar a [autenticação dos Serviços de Domínio do Diretório Ativo (Azure AD DS) em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) numa conta de armazenamento onde o [Serviço de Domínio AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado no inquilino AAD da subscrição associada.  

### <a name="solution"></a>Solução

Ativar a AAD DS no inquilino AAD da subscrição para a qual a sua conta de armazenamento está implantada. Precisa de privilégios de administrador do inquilino da AAD para criar um domínio gerido. Se não for o administrador do inquilino Azure AD, contacte o administrador e siga a orientação passo a passo para Ativar os [Serviços de Domínio do Diretório Ativo Azure utilizando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Ocorreu o erro do sistema 1359. Um erro interno' recebido sobre o acesso do SMB a ações de ficheiros com a autenticação do Azure Ative Directory Domain Service (AAD DS)

### <a name="cause"></a>Causa

Ocorreu o erro do sistema 1359. Um erro interno ocorre quando tenta ligar à sua partilha de ficheiros com a autenticação AAD DS ativada contra um DS AAD com o nome DNS de domínio a começar por um carácter numérico. Por exemplo, se o nome DE DNS do domínio AAD DS for "1domain", terá este erro ao tentar montar a partilha de ficheiros usando credenciais AAD. 

### <a name="solution"></a>Solução

Atualmente, pode considerar a recolocação do seu AAD DS utilizando um novo nome DNS de domínio que se aplica às regras abaixo:
- Os nomes não podem começar com um personagem numérico.
- Os nomes devem ter entre 3 e 63 caracteres.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Incapaz de montar ficheiros Azure com credenciais de AD 

### <a name="self-diagnostics-steps"></a>Passos de autodiagnósto
Em primeiro lugar, certifique-se de que seguiu os quatro passos para ativar a [autenticação AD dos Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Em segundo lugar, tente [montar a partilha de ficheiros Azure com a chave da conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Se não tiver montado, faça o download [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para o ajudar a validar o ambiente de funcionamento do cliente, detetar a configuração incompatível do cliente que causaria falhas de acesso aos Ficheiros Azure, dá orientações prescritivas sobre a auto-correcção e, recolhendo os vestígios de diagnóstico.

Em terceiro lugar, pode executar o cmdlet Debug-AzStorageAccountAuth para realizar um conjunto de verificações básicas na sua configuração de AD com o registado no utilizador AD. Este cmdlet é suportado na [versão v0.1.2 e superior do AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Deve executar este cmdlet com um utilizador do AD que possua permissão de proprietário na conta de armazenamento de destino.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
O cmdlet executa estas verificações abaixo em sequência e fornece orientação para falhas:
1. CheckPort445Connectivity: Verifique se a Porta 445 está aberta para ligação SMB
2. CheckDomainJoined: Valide que a máquina do cliente é domínio associado à AD
3. CheckADObject: Confirme que existe um objeto no Diretório Ativo que representa a conta de armazenamento e tem o SPN correto (nome principal do serviço).
4. CheckGetKerberosTicket: Tente obter um bilhete Kerberos para ligar à conta de armazenamento 
5. CheckADObjectPasswordIsCorrect: Certifique-se de que a palavra-passe configurada na identidade AD que representa a conta de armazenamento corresponde à da conta de armazenamento kerb1 ou kerb2
6. CheckSidHasAadUser: Verifique se o utilizador com sessão registada no utilizador AD está sincronizado com a Azure AD. Se quiser analisar se um utilizador específico de AD está sincronizado com AZure AD, pode especificar o -UserName e -Domain nos parâmetros de entrada.
7. CheckAadUserHasSid: Verifique se um utilizador AD Azure tem um SID em AD, esta verificação requer que o utilizador introduza o Object Id do utilizador Azure AD com o parâmetro -ObjectId. 
8. CheckStorageAccountDomainJoined: Verifique as propriedades da conta de armazenamento para ver se a autenticação AD foi ativada e as propriedades de AD da conta são povoadas.

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Não é possível configurar permissões de diretório/nível de ficheiro (ACLs windows) com o Windows File Explorer

### <a name="symptom"></a>Sintoma

Pode experimentar os sintomas descritos abaixo ao tentar configurar ACLs do Windows com o File Explorer numa partilha de ficheiros montada:
- Depois de clicar na permissão de Edição no separador Segurança, o assistente de Permissão não carrega. 
- Quando tenta selecionar um novo utilizador ou grupo, a localização do domínio não apresenta o domínio DS AD certo. 

### <a name="solution"></a>Solução

Recomendamos que utilize a [ferramenta ICACLs](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) para configurar as permissões de diretório/arquivo como uma solução alternativa. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
