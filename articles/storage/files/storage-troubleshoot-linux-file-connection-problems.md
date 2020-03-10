---
title: Problemas de saca dos ficheiros Azure em Linux Microsoft Docs
description: Problemas de resolução de problemas de Ficheiros Azure em Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9849e8ab918562267e93506771a4c32cf96533a4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362403"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Problemas de suturação de ficheiros Azure em Linux

Este artigo lista problemas comuns relacionados com ficheiros Azure quando se conecta com clientes Linux. Fornece igualmente possíveis causas e resoluções para estes problemas. 

Além dos passos de resolução de problemas neste artigo, pode utilizar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) para garantir que o cliente Linux tem pré-requisitos corretos. AZFileDiagnostics automatiza a deteção da maioria dos sintomas mencionados neste artigo. Ajuda a configurar o seu ambiente para obter um desempenho ideal. Também pode encontrar esta informação nos [Ficheiros Azure partilha problemas](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). O resoluto de problemas fornece passos para ajudá-lo com problemas de ligação, mapeamento e montagem de ações do Azure Files.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível ligar ou montar uma partilha de ficheiros Azure

### <a name="cause"></a>Causa

As causas comuns para este problema são:

- Estás a usar um cliente de distribuição linux incompatível. Recomendamos que utilize as seguintes distribuições linux para se ligar a uma partilha de ficheiros Azure:

|   | SMB 2.1 <br>(Montes em VMs dentro da mesma região de Azure) | SMB 3.0 <br>(Montes a partir de instalações e regiões transversais) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Os utilitários CIFS (cifs-utils) não estão instalados no cliente.
- A versão smb/CIFS mínima, 2.1, não está instalada no cliente.
- A encriptação SMB 3.0 não é suportada no cliente. A tabela anterior fornece uma lista de distribuições linux que suportam a montagem a partir de instalações e regiões transversais usando encriptação. Outras distribuições requerem o kernel 4.11 e versões posteriores.
- Está a tentar ligar-se a uma conta de armazenamento sobre a porta 445 da TCP, que não é suportada.
- Está a tentar ligar-se a uma parte de ficheiro sinuosa de um VM Azure, e o VM não está na mesma região que a conta de armazenamento.
- Se a definição necessária para a [transferência Segura]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento, o Azure Files permitirá apenas ligações que utilizem SMB 3.0 com encriptação.

### <a name="solution"></a>Solução

Para resolver o problema, utilize a ferramenta de resolução de problemas para os erros de montagem de [Ficheiros Azure no Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta ferramenta:

* Ajuda-o a validar o ambiente de gestão do cliente.
* Deteta a configuração incompatível do cliente que causaria falha de acesso aos Ficheiros Azure.
* Dá orientação prescritiva sobre auto-fixação.
* Recolhe os vestígios de diagnóstico.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Erro de montagem(13): Permissão negada" quando monta uma partilha de ficheiros Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não encriptado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

Para obter mais informações, veja [Pré-requisitos para montar uma partilha de ficheiros do Azure com o Linux e o pacote cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se a partir de um cliente que suporta encriptação SMB ou se conectem a partir de uma máquina virtual no mesmo datacenter que a conta de armazenamento Azure que é usada para a partilha de ficheiros Azure.
2. Verifique se a definição necessária à [transferência segura](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) é desativada na conta de armazenamento se o cliente não suportar a encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de rede virtual ou firewall estão ativadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[Permissão negada] Quota de disco ultrapassada" quando tenta abrir um ficheiro

Em Linux, recebeuma mensagem de erro que se assemelha ao seguinte:

**\<nome de ficheiro> [permissão negada] Quota de disco ultrapassada**

### <a name="cause"></a>Causa

Atingiu o limite superior das pegas abertas simultâneas que são permitidas para um ficheiro.

Há uma quota de 2.000 pegas abertas num único ficheiro. Quando se tem 2.000 pegas abertas, é apresentada uma mensagem de erro que diz que a quota é atingida.

### <a name="solution"></a>Solução

Reduza o número de pegas abertas simultâneas fechando algumas pegas e, em seguida, tente novamente a operação.

Para visualizar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Para fechar as pegas abertas para uma partilha de ficheiros, diretório ou ficheiro, utilize o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Os cmdlets de cmdlets de comando Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia lenta de ficheiros de e para Ficheiros Azure em Linux

- Se não tiver um requisito de tamanho mínimo de I/O específico, recomendamos que utilize 1 MiB como tamanho de I/S para um desempenho ótimo.
- Utilize o método de cópia certo:
    - Utilize o [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas ações de ficheiro.
    - A utilização de cp ou dd com paralelo pode melhorar a velocidade da cópia, o número de fios depende da sua caixa de utilização e da carga de trabalho. Os seguintes exemplos utilizam seis: 
    - exemplo cp (cp utilizará o tamanho do bloco predefinido do sistema de ficheiros como o tamanho do pedaço): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - exemplo dd (este comando define explicitamente o tamanho do pedaço para 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Ferramentas de terceiros de código aberto, tais como:
        - [Gnu Paralelo](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Separe os ficheiros e embale-os em divisórias.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Utiliza fpart e uma ferramenta de cópia para gerar vários casos para migrar dados de src_dir para dst_url.
        - [Multi](https://github.com/pkolano/mutil) - Cp multi-roscado e md5sum com base em coreutils GNU.
- Definir o tamanho do ficheiro com antecedência, em vez de fazer cada escrever uma escrita extensão, ajuda a melhorar a velocidade da cópia em cenários onde o tamanho do ficheiro é conhecido. Se a extensão de escrita supor ser evitada, pode definir um tamanho de ficheiro de destino com `truncate - size <size><file>` comando. Depois disso, `dd if=<source> of=<target> bs=1M conv=notrunc`comando copiará um ficheiro fonte sem ter de atualizar repetidamente o tamanho do ficheiro alvo. Por exemplo, pode definir o tamanho do ficheiro de destino para cada ficheiro que pretende copiar (assumir que uma parte é montada sob /mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - e, em seguida, - copiar ficheiros sem estender escreve em paralelo: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Erro de montagem(115): Operação agora em curso" quando montar Ficheiros Azure utilizando SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições do Linux ainda não suportam as funcionalidades de encriptação no SMB 3.0. Os utilizadores poderão receber uma mensagem de erro “115” se tentarem montar os Ficheiros do Azure com o SMB 3.0 devido à falta de uma funcionalidade. O SMB 3.0 com a encriptação completa é suportado apenas quando estiver a utilizar o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

A funcionalidade de encriptação do SMB 3.0 para Linux foi introduzida no kernel 4.11. Esta funcionalidade permite a montagem de uma partilha de ficheiros do Azure a partir do local ou a partir de uma região diferente do Azure. Algumas distribuições linux podem ter recuado nas alterações do kernel 4.11 para versões mais antigas do kernel Linux que mantêm. Para ajudar a determinar se a sua versão do Linux suporta SMB 3.0 com encriptação, consulte os [Ficheiros Use Azure com o Linux](storage-how-to-use-files-linux.md). 

Se o cliente SMB do Linux não suportar a encriptação, monte os Ficheiros do Azure com o SMB 2.1 numa VM Linux do Azure que está no mesmo datacenter que a partilha de ficheiros. Verifique se a definição [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está ativada na conta de armazenamento. 

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
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Incapaz de apagar um ficheiro ou diretório numa partilha de ficheiros Azure

### <a name="cause"></a>Causa
Este problema ocorre normalmente se o ficheiro ou diretório tiver uma pega aberta. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todas as pegas abertas e o problema continuar a ocorrer, execute o seguinte:

- Utilize o cmdlet [PowerShell Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell para visualizar as pegas abertas.

- Utilize o cmdlet [PowerShell Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell para fechar as pegas abertas. 

> [!Note]  
> Os cmdlets de cmdlets de comando Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos na versão 2.4 ou posterior do módulo Az PowerShell. Para instalar o mais recente módulo Az PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento em uma partilha de ficheiros Azure montado em um VM Linux

### <a name="cause-1-caching"></a>Causa 1: Caching

Uma das causas possíveis de um desempenho lento é o cache desativado. O caching pode ser útil se estiver a aceder repetidamente a um ficheiro, caso contrário, pode ser um sobrecarga. Verifique se está a usar a cache antes de a desativar.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Para verificar se o cache está desativado, procure a **entrada cache=**

**Cache=nenhum** indica que o cacheching está desativado. Remonte a parte utilizando o comando de montagem predefinido ou adicionando explicitamente a opção **cache=estrita** ao comando de montagem para garantir que o cacheching padrão ou o modo de cache "rigoroso" esteja ativado.

Em alguns cenários, a opção de montagem de **servidor** pode fazer com que o comando **ls** seja executado de stat contra cada entrada de diretório. Este comportamento resulta numa degradação do desempenho quando se está a listar um grande diretório. Pode verificar as opções de montagem na sua entrada **/etc/fstab:**

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Também pode verificar se as opções corretas estão a ser utilizadas através da montagem **sudo / grep cifs** comando e verificação da sua saída. Segue-se a saída de exemplo:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Se a opção **cache=estrita** ou **serverino** não estiver presente, desmonte e monte novamente os Ficheiros Azure, executando o comando de montagem a partir da [documentação](../storage-how-to-use-files-linux.md). Em seguida, volte a verificar se a entrada **/etc/fstab** tem as opções corretas.

### <a name="cause-2-throttling"></a>Causa 2: Estrangulamento

É possível que esteja a sofrer de estrangulamento e que os seus pedidos estejam a ser enviados para uma fila. Pode verificar isso aproveitando as métricas de [armazenamento azure no Monitor Azure.](../common/storage-metrics-in-azure-monitor.md)

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Certifique-se de que a sua aplicação está dentro dos alvos da [escala De Ficheiros Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Selos temporais foram perdidos na cópia de ficheiros do Windows para o Linux

Nas plataformas Linux/Unix, o comando **cp-p** falha se os diferentes utilizadores possuem ficheiros 1 e ficheiro 2.

### <a name="cause"></a>Causa

A bandeira de força **f** no COPYFILE resulta na execução **cp-p-f** no Unix. Este comando também não consegue preservar o carimbo de tempo do ficheiro que não possui.

### <a name="workaround"></a>Solução

Utilize o utilizador da conta de armazenamento para copiar os ficheiros:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não pode aceder&lt;caminho&gt;': Erro de entrada/saída

Quando tenta listar ficheiros numa partilha de ficheiros Azure utilizando o comando ls, o comando fica pendurado ao listar ficheiros. Obtém-se o seguinte erro:

**ls: não pode aceder"&lt;caminho&gt;": Erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel Linux para as seguintes versões que tenham uma correção para este problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que são maiores ou iguais a 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não pode criar ligações simbólicas - inn: falhou na criação de ligação simbólica 't': Operação não suportada

### <a name="cause"></a>Causa
Por padrão, a montagem de ações de ficheiro sinuoso do Azure no Linux utilizando o CIFS não permite suporte para ligações simbólicas (simlinks). Vê-se um erro como este:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Solução
O cliente Linux CIFS não suporta a criação de links simbólicos ao estilo windows sobre o protocolo SMB 2 ou 3. Atualmente, o cliente Linux suporta outro estilo de ligações simbólicas chamadas [simligações Minshall+French](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) para criar e acompanhar operações. Os clientes que necessitem de links simbólicos podem usar a opção de montagem "mfsymlinks". Recomendamos "mfsymlinks" porque é também o formato que os Macs usam.

Para utilizar simligações, adicione o seguinte à extremidade do comando de montagem CIFS:

```
,mfsymlinks
```

Então o comando parece:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Em seguida, pode criar simligações, como sugerido no [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Erro de montagem(112): Hospedeiro está em baixo" devido a um tempo de reconexão

Quando o cliente está inativo durante muito tempo, ocorre um erro de montagem “112” no cliente Linux. Após um tempo de inatividade prolongado, o cliente desliga-se e a ligação excede o tempo limite.  

### <a name="cause"></a>Causa

A ligação pode estar inativa pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem o restabelecimento de uma ligação TCP ao servidor quando é utilizada a opção de montagem “flexível” padrão
-   Correções de restabelecimento de ligação recentes que não estão presentes nos kernels mais antigos

### <a name="solution"></a>Solução

Este problema de restabelecimento de ligação no kernel do Linux já foi corrigido como parte das alterações mencionadas nos seguintes artigos:

- [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Corrigir o restabelecimento de ligação para não diferir do restabelecimento da sessão do smb3 muito depois do restabelecimento de ligação do socket)
- [Call echo service immediately after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Chamar o serviço de eco imediatamente após o restabelecimento de ligação do socket)
- [CIFS: Corrigir uma possível corrupção na memória durante a religação](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Fixar um possível bloqueio duplo de mutex durante a religação (para o kernel v4.9 e posteriormente)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, estas alterações poderão não ser ainda transportadas para todas as distribuições do Linux. Se estiver a utilizar uma distribuição popular do Linux, pode verificar os [Ficheiros De Utilização Azure com o Linux](storage-how-to-use-files-linux.md) para ver qual a versão da sua distribuição tem as alterações necessárias ao kernel.

### <a name="workaround"></a>Solução

Pode contornar este problema ao especificar uma montagem forçada. Uma montagem forçada obriga o cliente a aguardar até que seja estabelecida uma ligação ou até que seja interrompido explicitamente. Pode utilizá-la para evitar erros devido a tempos limite de rede. No entanto, esta solução pode causar esperas indefinidas. Esteja preparado para parar as ligações, se necessário.

Se não for possível atualizar para as versões kernel mais recentes, poderá contornar este problema ao manter um ficheiro na partilha de ficheiros do Azure para o qual escreve de 30 em 30 segundos ou menos. Tem de ser uma operação de escrita, como reescrever a data de criação ou de modificação no ficheiro. Caso contrário, poderá obter resultados em cache e a operação poderá não acionar o restabelecimento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema rapidamente.
