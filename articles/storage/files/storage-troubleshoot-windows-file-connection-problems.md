---
title: Resolver problemas de Ficheiros do Azure no Windows
description: Problemas de resolução de problemas no Azure Files no Windows. Consulte questões comuns relacionadas com ficheiros Azure quando se conecta a partir de clientes windows e veja possíveis resoluções. Apenas para ações SMB
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878515"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Problemas de resolução de ficheiros Azure no Windows (SMB)

Este artigo lista problemas comuns relacionados com os Ficheiros Azure do Microsoft quando se conecta a partir de clientes Windows. Fornece igualmente possíveis causas e resoluções para estes problemas. Além das etapas de resolução de problemas neste artigo, também pode utilizar [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para garantir que o ambiente do cliente windows tem requisitos corretos. A AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionados neste artigo e ajuda a configurar o seu ambiente para obter o melhor desempenho.

> [!IMPORTANT]
> O conteúdo deste artigo aplica-se apenas às ações da SMB. Para mais informações sobre as ações da NFS, consulte [as ações de ficheiro Troubleshoot Azure NFS](storage-troubleshooting-files-nfs.md).

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 quando monta uma partilha de ficheiros Azure

Quando tentar montar uma partilha de ficheiros, poderá receber o seguinte erro:

- Ocorreu o erro de sistema 5. O acesso é negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não encriptado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](../common/storage-require-secure-transfer.md) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada sistema negociam pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se a partir de um cliente que suporte encriptação SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte-se a partir de uma máquina virtual no mesmo centro de dados que a conta de armazenamento Azure que é usada para a partilha de ficheiros Azure.
2. Verifique se a [definição de transferência necessária](../common/storage-require-secure-transfer.md) para a transferência segura está desativada na conta de armazenamento se o cliente não suportar encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: As permissões de nível de partilha estão incorretas quando se utiliza a autenticação baseada na identidade

Se os utilizadores estiverem a aceder à partilha de ficheiros Azure utilizando a autenticação Ative Directory (AD) ou a Azure Ative Directory Domain Services (Azure AD DS), o acesso à partilha de ficheiros falhará com o erro de "Acesso é negado" se as permissões de nível de partilha estiverem incorretas. 

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Validar que as permissões estão configuradas corretamente:

- **Ative Directory (AD)** ver [Atribuir permissões de nível de partilha a uma identidade](./storage-files-identity-ad-ds-assign-permissions.md).

    As atribuições de permissão ao nível de partilha são suportadas por grupos e utilizadores que tenham sido sincronizados desde o Ative Directory (AD) ao Azure Ative Directory (Azure AD) utilizando o Azure AD Connect.  Confirme que os grupos e utilizadores que estão a ser atribuídos permissões de nível de partilha não são grupos "apenas na nuvem".
- **Azure Ative Directory Domain Services (Azure AD DS)** ver [Atribuir permissões de acesso a uma identidade](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

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
O Azure File Sync pode transformar o seu Windows Server no local numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. O Azure File Sync funciona na porta 443 e pode assim servir como uma solução alternativa para aceder aos Ficheiros do Azure de clientes que tenham a porta 445 bloqueada. [Saiba como configurar o Azure File Sync](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Solução 2 – Utilizar a VPN
Ao configurar uma VPN na sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à internet. Siga as [instruções para configurar a VPN](storage-files-configure-p2s-vpn-windows.md) para aceder aos Ficheiros do Azure a partir do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3 – Desbloqueie a porta 445 com a ajuda do ISP/Administrador de TI
Trabalhe com o seu departamento de TI ou ISP para abrir as [gamas Azure IP](https://www.microsoft.com/download/details.aspx?id=41653)da porta 445.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – Utilize ferramentas com base na API REST como o Explorador de Armazenamento/Powershell
O Azure Files também suporta o REST para além do SMB. O acesso à REST funciona na porta 443 (TCP padrão). Existem várias ferramentas que são escritas através da API REST que permitem uma experiência da IU avançada. [O Explorador de Armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) é um deles. [Transfira e Instale o Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e ligue-se à partilha de ficheiros suportada pelos Ficheiros do Azure. Também pode utilizar [o PowerShell](./storage-how-to-use-files-powershell.md) que também utiliza a API REST do utilizador.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está ativado

O erro do sistema 53 ou o erro do sistema 87 podem ocorrer se a comunicação NTLMv1 estiver ativada no cliente. Os Ficheiros do Azure suportam apenas a autenticação NTLMv2. Ter o NTLMv1 ativado cria um cliente menos seguro. Como tal, a comunicação é bloqueada para os Ficheiros do Azure. 

Para determinar se esta é a causa do erro, verifique se a seguinte sub-chave de registo não está definida para um valor inferior a 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, veja o tópico [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) tópico no TechNet.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Reverta o valor **LmCompatibilityLevel** para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Erro 1816 - Não há quota suficiente para processar este comando

### <a name="cause"></a>Causa

O erro 1816 acontece quando se atinge o limite superior de pegas abertas simultâneas que são permitidas para um ficheiro ou diretório na partilha de ficheiros Azure. Para obter mais informações, veja [Alvos de escala dos Ficheiros do Azure](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Solução

Reduza o número de pegas abertas simultâneas fechando algumas pegas e, em seguida, redaça. Para obter mais informações, consulte [o desempenho do Microsoft Azure Storage e a lista de verificação de escalabilidade](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para visualizar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Para fechar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Os Get-AzStorageFileHandle e Close-AzStorageFileHandle cmdlets estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "Não acesso" quando tenta aceder ou apagar uma Partilha de Ficheiros Azure  
Quando tentar aceder ou eliminar uma partilha de ficheiros Azure no portal, poderá receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: A sua conta de utilizador não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Navegue na conta de armazenamento onde está localizada a partilha de ficheiros Azure, clique no **controlo de acesso (IAM)** e verifique se a sua conta de utilizador tem acesso à conta de armazenamento. Para saber mais, consulte [Como garantir a sua conta de armazenamento com o controlo de acesso baseado em funções Azure (Azure RBAC)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Incapaz de modificar, mover/mudar de nome, ou apagar um ficheiro ou diretório
Um dos principais propósitos de uma partilha de ficheiros é que vários utilizadores e aplicações podem simultaneamente interagir com ficheiros e diretórios na partilha. Para ajudar nesta interação, as ações de ficheiros fornecem várias formas de mediar o acesso a ficheiros e diretórios.

Quando abre um ficheiro a partir de uma partilha de ficheiros Azure montada sobre a SMB, o seu sistema de aplicação/funcionamento solicita uma pega de ficheiro, que é uma referência ao ficheiro. Entre outras coisas, a sua aplicação especifica um modo de partilha de ficheiros quando solicita uma pega de ficheiro, que especifica o nível de exclusividade do seu acesso ao ficheiro aplicado pelos Ficheiros Azure: 

- `None`: tem acesso exclusivo. 
- `Read`: outros podem ler o ficheiro enquanto o tem aberto.
- `Write`: outros podem escrever para o ficheiro enquanto o tem aberto. 
- `ReadWrite`: uma combinação dos `Read` modos e `Write` partilha.
- `Delete`: outros podem apagar o ficheiro enquanto o tem aberto. 

Embora como um protocolo apátrida, o protocolo FileREST não tenha um conceito de alças de ficheiros, fornece um mecanismo semelhante para mediar o acesso a ficheiros e pastas que o seu script, aplicação ou serviço podem usar: locações de ficheiros. Quando um ficheiro é alugado, é tratado como equivalente a uma pega de ficheiro com um modo de partilha de ficheiros de `None` . 

Embora as alças de ficheiros e os contratos de arrendamento sirvam um propósito importante, por vezes as alças de ficheiros e os locados podem ser órfãos. Quando isto acontece, isto pode causar problemas na modificação ou eliminação de ficheiros. Pode ver mensagens de erro como:

- O processo não pode aceder ao ficheiro, porque está a ser utilizado por outro processo.
- A ação não pode ser concluída porque o ficheiro está aberto noutro programa.
- O documento está bloqueado para edição por outro utilizador.
- O recurso especificado está marcado para eliminação por um cliente SMB.

A resolução para esta questão depende se esta questão está a ser causada por um cabo de ficheiro órfão ou pelo arrendamento. 

### <a name="cause-1"></a>Motivo 1
Uma pega de ficheiro está a impedir que um ficheiro/diretório seja modificado ou eliminado. Pode utilizar o [cmdlet Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell para visualizar as pegas abertas. 

Se todos os clientes SMB tiverem fechado as suas pegas abertas num ficheiro/diretório e o problema continuar a ocorrer, pode forçar a fechar uma pega de ficheiro.

### <a name="solution-1"></a>Solução 1
Para forçar o fecho de uma pega de ficheiro, utilize o [cmdlet Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell. 

> [!Note]  
> Os Get-AzStorageFileHandle e Close-AzStorageFileHandle cmdlets estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Motivo 2
Uma concessão de ficheiro serve para impedir que um ficheiro seja modificado ou eliminado. Pode verificar se um ficheiro tem uma locação de ficheiros com o seguinte PowerShell, `<resource-group>` `<storage-account>` substituindo, e pelos `<file-share>` `<path-to-file>` valores adequados para o seu ambiente:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Se um ficheiro tiver uma concessão, o objeto devolvido deve conter as seguintes propriedades:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Solução 2
Para remover a concessão de um ficheiro, pode libertar ou interromper a concessão. Para libertar a concessão, precisa do LeaseId da concessão, que define quando cria a concessão. Não precisa do LeaseId para interromper a concessão.

O exemplo a seguir mostra como quebrar o contrato de arrendamento para o ficheiro indicado na causa 2 (este exemplo continua com as variáveis PowerShell da causa 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia lenta de ficheiros para e de Ficheiros do Azure no Windows

Poderá ver um desempenho lento quando tentar transferir ficheiros para o serviço Azure File.

- Se não tiver um requisito específico de tamanho mínimo de E/S, recomendamos que utilize 1 MiB como o tamanho de E/S para um desempenho ótimo.
-   Se souber o tamanho final de um ficheiro que está a estender com as escritas, e o seu software não tiver problemas de compatibilidade quando a cauda não escrita no ficheiro contiver zeros, então descreva o tamanho do ficheiro com antecedência em vez de fazer cada escrita uma escrita alargada.
-   Utilize o método de cópia certo:
    -   Utilize [a AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas ações de ficheiro.
    -   Utilize [robocopia](./storage-how-to-create-file-share.md) entre ações de ficheiros num computador no local.

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
Monte a parte de uma linha de comando não administrador. Em alternativa, pode seguir [este tópico TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) para configurar o valor do registo **EnableLinkedConnections.**

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
 
 
Por exemplo, pode defini-lo para 0x100000 e ver se o desempenho se torna melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Error AadDsTenantNotFound em permitir a autenticação do Azure Ative Directory Domain Service (Azure AD DS) para ficheiros Azure "Incapaz de localizar inquilinos ativos com iD-inquilino-id"

### <a name="cause"></a>Causa

Error AadDsTenantNotFound acontece quando tenta ativar a [autenticação dos Serviços de Domínio do Diretório Ativo (Azure AD DS) em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) numa conta de armazenamento onde o [Azure AD Domain Service (Azure AD DS)](../../active-directory-domain-services/overview.md) não é criado no inquilino AD AD da subscrição associada.  

### <a name="solution"></a>Solução

Ativar o Azure AD DS no inquilino AZure AD da subscrição para a qual a sua conta de armazenamento está implantada. Você precisa de privilégios de administrador do inquilino AD Azure para criar um domínio gerido. Se não for o administrador do inquilino Azure AD, contacte o administrador e siga a orientação passo a passo para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure.](../../active-directory-domain-services/tutorial-create-instance.md)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Incapaz de montar ficheiros Azure com credenciais de AD 

### <a name="self-diagnostics-steps"></a>Passos de autodiagnósto
Em primeiro lugar, certifique-se de que seguiu os quatro passos para ativar a [autenticação AD dos Ficheiros Azure](./storage-files-identity-auth-active-directory-enable.md).

Em segundo lugar, tente [montar a partilha de ficheiros Azure com a chave da conta de armazenamento](./storage-how-to-use-files-windows.md). Se não tiver montado, faça o download [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para o ajudar a validar o ambiente de funcionamento do cliente, detetar a configuração incompatível do cliente que causaria falhas de acesso aos Ficheiros Azure, dá orientações prescritivas sobre a auto-correcção e, recolhendo os vestígios de diagnóstico.

Em terceiro lugar, pode executar o Debug-AzStorageAccountAuth cmdlet para realizar um conjunto de verificações básicas na sua configuração de AD com o registado no utilizador AD. Este cmdlet é suportado na [versão v0.1.2 e superior do AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Deve executar este cmdlet com um utilizador do AD que possua permissão de proprietário na conta de armazenamento de destino.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
O cmdlet executa estas verificações abaixo em sequência e fornece orientação para falhas:
1. CheckADObjectPasswordIsCorrect: Certifique-se de que a palavra-passe configurada na identidade AD que representa a conta de armazenamento corresponde à da conta de armazenamento kerb1 ou kerb2. Se a palavra-passe estiver incorreta, pode executar [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) para redefinir a palavra-passe. 
2. CheckADObject: Confirme que existe um objeto no Diretório Ativo que representa a conta de armazenamento e tem o SPN correto (nome principal do serviço). Se o SPN não estiver corretamente configurado, por favor, verifique o cmdlet Set-AD devolvido no cmdlet de depurador para configurar o SPN.
3. CheckDomainJoined: Valide que a máquina do cliente é domínio associado à AD. Se a sua máquina não estiver de domínio associado à AD, consulte este [artigo](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) para obter instruções de união de domínios.
4. CheckPort445Connectivity: Verifique se a Porta 445 está aberta para a ligação SMB. Se a porta necessária não estiver aberta, consulte a ferramenta de resolução de problemas [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para problemas de conectividade com ficheiros Azure.
5. CheckSidHasAadUser: Verifique se o utilizador com sessão registada no utilizador AD está sincronizado com a Azure AD. Se quiser analisar se um utilizador específico de AD está sincronizado com AZure AD, pode especificar o -UserName e -Domain nos parâmetros de entrada. 
6. CheckGetKerberosTicket: Tente obter um bilhete Kerberos para ligar à conta de armazenamento. Se não houver um token Kerberos válido, execute o klist obter cifs/armazenamento-conta-name.file.core.windows.net cmdlet e examine o código de erro para causar a falha de recuperação do bilhete.
7. CheckStorageAccountDomainJoined: Verifique se a autenticação AD foi ativada e se as propriedades de AD da conta estão povoadas. Caso contrário, consulte [as](./storage-files-identity-ad-ds-enable.md) instruções aqui para ativar a autenticação AD DS nos Ficheiros Azure. 
8. CheckUserRbacAssignment: Verifique se o utilizador AD tem a atribuição adequada da função RBAC para fornecer permissão de nível de partilha para aceder a Ficheiros Azure. Caso contrário, consulte [a](./storage-files-identity-ad-ds-assign-permissions.md) instrução aqui para configurar a permissão do nível de partilha. (Suportado na versão AzFilesHybrid v0.2.3+
9. CheckUserFileAccess: Verifique se o utilizador de AD tem a permissão de diretório/ficheiro adequado (ACLs windows) para aceder a Ficheiros Azure. Caso contrário, consulte [a](./storage-files-identity-ad-ds-configure-permissions.md) instrução aqui para configurar a permissão de nível de diretório/arquivo. (Suportado na versão AzFilesHybrid v0.2.3+

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Não é possível configurar permissões de diretório/nível de ficheiro (ACLs windows) com o Windows File Explorer

### <a name="symptom"></a>Sintoma

Pode experimentar os sintomas descritos abaixo ao tentar configurar ACLs do Windows com o File Explorer numa partilha de ficheiros montada:
- Depois de clicar na permissão de Edição no separador Segurança, o assistente de Permissão não carrega. 
- Quando tenta selecionar um novo utilizador ou grupo, a localização do domínio não apresenta o domínio DS AD certo. 

### <a name="solution"></a>Solução

Recomendamos que utilize a [ferramenta ICACLs](/windows-server/administration/windows-commands/icacls) para configurar as permissões de diretório/arquivo como uma solução alternativa. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Erros ao executar Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Erro: "O serviço de diretório não pôde alocar um identificador relativo"

Este erro pode ocorrer se um controlador de domínio que detém a função RID Master FSMO estiver indisponível ou tiver sido removido do domínio e restaurado a partir de cópia de segurança.  Confirme que todos os Controladores de Domínio estão a funcionar e disponíveis.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Erro: “Não é possível vincular os parâmetros posicionais, porque não foram indicados nomes”

Este erro é provavelmente acionado por um erro de sintaxe no comando Join-AzStorageAccountforAuth.  Verifique se o comando tem erros ortográficos ou erros de sintaxe e verifique se a versão mais recente do módulo AzFilesHybrid https://github.com/Azure-Samples/azure-files-samples/releases) está instalada.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files no local Suporte de autenticação AD DS para encriptação AES 256 Kerberos

Introduzimos suporte de encriptação AES 256 Kerberos para a autenticação AD DS dos Ficheiros Azure com [módulo AzFilesHybrid v0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Se tiver ativado a autenticação AD DS com uma versão do módulo inferior a v0.2.2, terá de descarregar o mais recente módulo AzFilesHybrid (v0.2.2+) e executar o PowerShell abaixo. Se ainda não tiver ativado a autenticação AD DS na sua conta de armazenamento, pode seguir esta [orientação](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) para ativação. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
