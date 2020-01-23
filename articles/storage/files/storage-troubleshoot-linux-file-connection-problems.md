---
title: Solucionar problemas de arquivos do Azure no Linux | Microsoft Docs
description: Solucionando problemas de arquivos do Azure no Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9849e8ab918562267e93506771a4c32cf96533a4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544944"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Solucionar problemas de arquivos do Azure no Linux

Este artigo lista os problemas comuns relacionados aos arquivos do Azure quando você se conecta de clientes Linux. Ele também fornece possíveis causas e resoluções para esses problemas. 

Além das etapas de solução de problemas neste artigo, você pode usar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) para garantir que o cliente Linux tenha os pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo. Ele ajuda a configurar seu ambiente para obter um desempenho ideal. Você também pode encontrar essas informações na [solução de problemas de compartilhamentos do Azure files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). O solucionador de problemas fornece etapas para ajudá-lo com problemas de conexão, mapeamento e montagem de compartilhamentos de arquivos do Azure.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível conectar ou montar um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

As causas comuns para esse problema são:

- Você está usando um cliente de distribuição do Linux incompatível. Recomendamos que você use as seguintes distribuições do Linux para se conectar a um compartilhamento de arquivos do Azure:

|   | SMB 2.1 <br>(Monta em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens do local e entre regiões) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + |   |
| openSUSE | 13.2 + | 42.3 + |
| Servidor Linux Empresarial SUSE | 12 | 12 SP3 + |

- Os utilitários CIFS (CIFS-utils) não estão instalados no cliente.
- A versão mínima do SMB/CIFS, 2,1, não está instalada no cliente.
- Não há suporte para a criptografia SMB 3,0 no cliente. A tabela anterior fornece uma lista de distribuições do Linux que dão suporte à montagem do local e entre regiões usando criptografia. Outras distribuições requerem o kernel 4.11 e versões posteriores.
- Você está tentando se conectar a uma conta de armazenamento pela porta TCP 445, que não tem suporte.
- Você está tentando se conectar a um compartilhamento de arquivos do Azure de uma VM do Azure e a VM não está na mesma região que a conta de armazenamento.
- Se a configuração [transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver habilitada na conta de armazenamento, os arquivos do Azure permitirão apenas conexões que usam SMB 3,0 com criptografia.

### <a name="solution"></a>Solução

Para resolver o problema, use a [ferramenta de solução de problemas para erros de montagem de arquivos do Azure no Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta ferramenta:

* Ajuda a validar o ambiente de execução do cliente.
* Detecta a configuração de cliente incompatível que causaria falha de acesso para arquivos do Azure.
* Fornece orientação prescritiva sobre correção automática.
* Coleta os rastreamentos de diagnóstico.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Erro de montagem (13): permissão negada" ao montar um compartilhamento de arquivos do Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canal de comunicação não criptografado

Por motivos de segurança, as ligações para as partilhas de ficheiros do Azure serão bloqueadas se o canal de comunicação não estiver encriptado e se a tentativa de ligação não for feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. As ligações não encriptadas dentro do mesmo datacenter poderão também ser bloqueadas se a definição [Transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento. Será fornecido um canal de comunicação encriptado apenas se o SO do cliente do utilizador suportar a encriptação SMB.

Para obter mais informações, veja [Pré-requisitos para montar uma partilha de ficheiros do Azure com o Linux e o pacote cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Conecte-se de um cliente que dá suporte à criptografia SMB ou conecte-se de uma máquina virtual no mesmo datacenter que a conta de armazenamento do Azure que é usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Caso estejam configuradas regras de firewall ou de rede virtual (VNET) na conta de armazenamento, será negado o acesso ao tráfego de rede, a menos que o endereço IP do cliente ou a rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se as regras de firewall ou de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual estão a causar o problema, altere temporariamente a definição da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[permissão negada] cota de disco excedida" quando você tenta abrir um arquivo

No Linux, você recebe uma mensagem de erro semelhante à seguinte:

**\<nome de arquivo > [permissão negada] cota de disco excedida**

### <a name="cause"></a>Causa

Você atingiu o limite máximo de identificadores abertos simultâneos que são permitidos para um arquivo.

Há uma cota de 2.000 identificadores abertos em um único arquivo. Quando você tem 2.000 identificadores abertos, uma mensagem de erro é exibida dizendo que a cota é atingida.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e, em seguida, repita a operação.

Para exibir identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) do PowerShell.  

Para fechar identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivo lenta de e para arquivos do Azure no Linux

- Se você não tiver um requisito de tamanho mínimo de e/s específico, recomendamos o uso de 1 MiB como o tamanho de e/s para um desempenho ideal.
- Use o método de cópia correto:
    - Use [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre dois compartilhamentos de arquivos.
    - Usar CP ou DD com Parallel pode melhorar a velocidade de cópia, o número de threads depende do seu caso de uso e da carga de trabalho. Os exemplos a seguir usam seis: 
    - exemplo de CP (CP usará o tamanho de bloco padrão do sistema de arquivos como o tamanho da parte): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - exemplo de DD (este comando define explicitamente o tamanho da parte como 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Ferramentas de terceiros de código aberto, como:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) -classifica os arquivos e os compacta em partições.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -usa fpart e uma ferramenta de cópia para gerar várias instâncias para migrar dados de src_dir para dst_url.
        - [Vários](https://github.com/pkolano/mutil) multithreaded CP e md5sum com base no GNU coreutils.
- Definir o tamanho do arquivo com antecedência, em vez de fazer cada gravação de uma gravação de extensão, ajuda a melhorar a velocidade de cópia em cenários em que o tamanho do arquivo é conhecido. Se for necessário evitar gravações estendidas, você poderá definir um tamanho de arquivo de destino com `truncate - size <size><file>` comando. Depois disso, `dd if=<source> of=<target> bs=1M conv=notrunc`comando copiará um arquivo de origem sem precisar atualizar repetidamente o tamanho do arquivo de destino. Por exemplo, você pode definir o tamanho do arquivo de destino para cada arquivo que deseja copiar (Suponha que um compartilhamento seja montado em/mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - e, em seguida, copiar arquivos sem estender gravações em paralelo: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Erro de montagem (115): operação agora em andamento" ao montar arquivos do Azure usando SMB 3,0

### <a name="cause"></a>Causa

Algumas distribuições do Linux ainda não suportam as funcionalidades de encriptação no SMB 3.0. Os utilizadores poderão receber uma mensagem de erro “115” se tentarem montar os Ficheiros do Azure com o SMB 3.0 devido à falta de uma funcionalidade. O SMB 3.0 com a encriptação completa é suportado apenas quando estiver a utilizar o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

A funcionalidade de encriptação do SMB 3.0 para Linux foi introduzida no kernel 4.11. Esta funcionalidade permite a montagem de uma partilha de ficheiros do Azure a partir do local ou a partir de uma região diferente do Azure. Algumas distribuições do Linux podem ter alterações reportadas do kernel 4,11 para versões mais antigas do kernel do Linux que eles mantêm. Para ajudar a determinar se sua versão do Linux dá suporte a SMB 3,0 com criptografia, consulte [usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md). 

Se o cliente SMB do Linux não suportar a encriptação, monte os Ficheiros do Azure com o SMB 2.1 numa VM Linux do Azure que está no mesmo datacenter que a partilha de ficheiros. Verifique se a definição [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está ativada na conta de armazenamento. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "sem acesso" ao tentar acessar ou excluir um compartilhamento de arquivos do Azure  
Ao tentar acessar ou excluir um compartilhamento de arquivos do Azure no portal, você pode receber o seguinte erro:

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

### <a name="cause"></a>Causa
Esse problema normalmente ocorre se o arquivo ou diretório tiver um identificador aberto. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todos os identificadores abertos e o problema continuar ocorrendo, execute o seguinte:

- Use o cmdlet do PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) para exibir identificadores abertos.

- Use o cmdlet do PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) para fechar identificadores abertos. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento em um compartilhamento de arquivos do Azure montado em uma VM Linux

### <a name="cause-1-caching"></a>Causa 1: Caching

Uma possível causa do desempenho lento é o cache desabilitado. O Caching pode ser útil se você estiver acessando um arquivo repetidamente, caso contrário, pode ser uma sobrecarga. Verifique se você está usando o cache antes de desabilitá-lo.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

Para verificar se o Caching está desabilitado, procure a entrada **cache =** .

**Cache = None** indica que o Caching está desabilitado. Remonte o compartilhamento usando o comando de montagem padrão ou adicionando explicitamente a opção **cache = Strict** ao comando mount para garantir que o cache padrão ou o modo de cache "estrito" esteja habilitado.

Em alguns cenários, a opção de montagem **serverino** pode fazer com que o comando **ls** execute stat em todas as entradas de diretório. Esse comportamento resulta em degradação de desempenho quando você está listando um diretório grande. Você pode verificar as opções de montagem em sua entrada **/etc/fstab** :

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Você também pode verificar se as opções corretas estão sendo usadas executando o comando **sudo mount | grep CIFS** e verificando sua saída. Veja a seguir um exemplo de saída:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Se a opção **cache = Strict** ou **serverino** não estiver presente, desmonte e monte os arquivos do Azure novamente executando o comando Mount da [documentação](../storage-how-to-use-files-linux.md). Em seguida, verifique novamente se a entrada **/etc/fstab** tem as opções corretas.

### <a name="cause-2-throttling"></a>Causa 2: limitação

É possível que você esteja enfrentando a limitação e que suas solicitações estejam sendo enviadas para uma fila. Você pode verificar isso aproveitando as [métricas de armazenamento do Azure no Azure monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Verifique se seu aplicativo está dentro dos [destinos de escala de arquivos do Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Os carimbos de data/hora foram perdidos na cópia de arquivos do Windows para o Linux

Em plataformas Linux/Unix, o comando **CP-p** falhará se usuários diferentes tiverem o arquivo 1 e o arquivo 2.

### <a name="cause"></a>Causa

O sinalizador de força **f** em CopyFile resulta na execução de **CP-p-f** no UNIX. Esse comando também falha ao preservar o carimbo de data/hora do arquivo que você não possui.

### <a name="workaround"></a>Solução

Use o usuário da conta de armazenamento para copiar os arquivos:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não é possível acessar '&lt;Path&gt;': erro de entrada/saída

Quando você tenta listar arquivos em um compartilhamento de arquivos do Azure usando o comando ls, o comando trava ao listar arquivos. Você Obtém o seguinte erro:

**ls: não é possível acessar '&lt;Path&gt;': erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que têm uma correção para esse problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que são maiores ou iguais a 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não é possível criar links simbólicos-ln: falha ao criar o link simbólico ' T': operação sem suporte

### <a name="cause"></a>Causa
Por padrão, a montagem de compartilhamentos de arquivos do Azure no Linux usando CIFS não habilita o suporte para links simbólicos (symlinks). Você verá um erro como este:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Solução
O cliente Linux CIFS não dá suporte à criação de links simbólicos no estilo do Windows por meio do protocolo SMB 2 ou 3. Atualmente, o cliente Linux dá suporte a outro estilo de links simbólicos chamado [Minshall + French symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) para operações de criação e de acompanhamento. Os clientes que precisam de links simbólicos podem usar a opção de montagem "mfsymlinks". Recomendamos "mfsymlinks" porque ele também é o formato que os Macs usam.

Para usar o symlinks, adicione o seguinte ao final do comando de montagem CIFS:

```
,mfsymlinks
```

Portanto, o comando é semelhante a:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Em seguida, você pode criar symlinks como sugerido no [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Erro de montagem (112): o host está inoperante" devido a um tempo limite de reconexão

Quando o cliente está inativo durante muito tempo, ocorre um erro de montagem “112” no cliente Linux. Após um tempo de inatividade prolongado, o cliente desliga-se e a ligação excede o tempo limite.  

### <a name="cause"></a>Causa

A ligação pode estar inativa pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem o restabelecimento de uma ligação TCP ao servidor quando é utilizada a opção de montagem “flexível” padrão
-   Correções de restabelecimento de ligação recentes que não estão presentes nos kernels mais antigos

### <a name="solution"></a>Solução

Este problema de restabelecimento de ligação no kernel do Linux já foi corrigido como parte das alterações mencionadas nos seguintes artigos:

- [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Corrigir o restabelecimento de ligação para não diferir do restabelecimento da sessão do smb3 muito depois do restabelecimento de ligação do socket)
- [Call echo service immediately after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Chamar o serviço de eco imediatamente após o restabelecimento de ligação do socket)
- [CIFS: corrigir uma possível corrupção de memória durante a reconexão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: corrigir um possível bloqueio duplo de mutex durante a reconexão (para kernel v 4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, estas alterações poderão não ser ainda transportadas para todas as distribuições do Linux. Se você estiver usando uma distribuição do Linux popular, poderá verificar em [usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md) para ver qual versão da sua distribuição tem as alterações de kernel necessárias.

### <a name="workaround"></a>Solução

Pode contornar este problema ao especificar uma montagem forçada. Uma montagem forçada obriga o cliente a aguardar até que seja estabelecida uma ligação ou até que seja interrompido explicitamente. Pode utilizá-la para evitar erros devido a tempos limite de rede. No entanto, esta solução pode causar esperas indefinidas. Esteja preparado para parar as ligações, se necessário.

Se não for possível atualizar para as versões kernel mais recentes, poderá contornar este problema ao manter um ficheiro na partilha de ficheiros do Azure para o qual escreve de 30 em 30 segundos ou menos. Tem de ser uma operação de escrita, como reescrever a data de criação ou de modificação no ficheiro. Caso contrário, poderá obter resultados em cache e a operação poderá não acionar o restabelecimento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.
