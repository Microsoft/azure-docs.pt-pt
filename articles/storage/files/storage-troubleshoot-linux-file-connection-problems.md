---
title: Resolução de problemas de ficheiros do Azure no Linux | Documentos da Microsoft
description: Resolução de problemas de ficheiros do Azure no Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 97f737c8d1228bd03baf59f2ebe830f715241299
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449847"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Resolução de problemas de ficheiros do Azure no Linux

Este artigo lista problemas comuns relacionados com ficheiros do Azure quando se liga a partir de clientes de Linux. Ele também fornece possíveis causas e resoluções para esses problemas. 

Além dos passos de resolução de problemas neste artigo, pode usar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) para garantir que o cliente de Linux tem pré-requisitos corretos. AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionadas neste artigo. Ele ajuda a configurar o ambiente para obter um desempenho ideal. Também pode encontrar estas informações no [solucionador de problemas de partilhas de ficheiros do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). A resolução de problemas fornece passos para ajudá-lo com problemas de ligação, mapeamento e montar partilhas de ficheiros do Azure.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível ligar a ou montar uma partilha de ficheiros do Azure

### <a name="cause"></a>Causa

Causas comuns para esse problema são:

- Está a utilizar um cliente de distribuição de Linux incompatível. Recomendamos que utilize as seguintes distribuições do Linux para ligar a uma partilha de ficheiros do Azure:

|   | SMB 2.1 <br>(Monta em VMs na mesma região do Azure) | SMB 3.0 <br>(Monta no local e em várias regiões) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Utilitários CIFS (cfs utils) não estão instalados no cliente.
- A versão mínima do SMB/CIFS, 2.1, não está instalada no cliente.
- Encriptação SMB 3.0 não é suportada no cliente. A tabela anterior fornece uma lista de distribuições do Linux que montagem de suporte no local e em várias regiões através da encriptação. Outras distribuições requerem kernel 4.11 e versões posteriores.
- Está a tentar ligar a uma conta de armazenamento através da porta TCP 445, que não é suportada.
- Está a tentar ligar a uma partilha de ficheiros do Azure a partir de uma VM do Azure e a VM não está na mesma região que a conta de armazenamento.
- Se o [transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição está ativada na conta de armazenamento, ficheiros do Azure irá permitir que apenas as ligações que utilizam SMB 3.0 com a encriptação.

### <a name="solution"></a>Solução

Para resolver o problema, utilize o [ferramenta de resolução de problemas para erros de montagem no Linux dos ficheiros do Azure](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta ferramenta:

* Ajuda-o para validar o cliente com o ambiente.
* Deteta a configuração de cliente incompatível que podem causar a falha de acesso para ficheiros do Azure.
* Fornece orientação prescritiva Self-resolver este problema.
* Recolhe os rastreios de diagnóstico.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Montar error(13): Permissão negada"Quando monta uma partilha de ficheiros do Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Fazer com que 1: Canal de comunicação sem encriptação

Por motivos de segurança, as ligações a partilhas de ficheiros do Azure são bloqueadas se o canal de comunicação não é encriptado e se a tentativa de ligação não é feita a partir do mesmo datacenter onde residem as partilhas de ficheiros do Azure. Conexões não criptografadas no mesmo datacenter podem também ser bloqueados se o [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição está ativada na conta de armazenamento. Um canal de comunicação encriptado é fornecido apenas se o SO de cliente do usuário oferece suporte a encriptação SMB.

Para obter mais informações, consulte [partilharem de pré-requisitos para montar um arquivo do Azure com Linux e o pacote do cifs utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Solução para causa 1

1. Ligar a partir de um cliente que suporte a encriptação SMB ou ligar a partir de uma máquina virtual no mesmo datacenter que a conta de armazenamento do Azure que é utilizada para a partilha de ficheiros do Azure.
2. Verifique se o [transferência segura necessária](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição estiver desativada na conta de armazenamento se o cliente não suporta a encriptação SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: Regras de firewall ou de rede virtual estão ativadas na conta de armazenamento 

Se a rede virtual (VNET) e regras de firewall estão configuradas na conta de armazenamento, o tráfego de rede será negado acesso a menos que o endereço IP do cliente ou a rede virtual tem permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para causa 2

Certifique-se de que as regras de rede e de firewall virtual estiverem configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual está a causar o problema, alterar temporariamente a definição da conta de armazenamento para **permitir o acesso de todas as redes**. Para obter mais informações, consulte [armazenamento do Azure configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"quota de disco [permissão negada] foi excedido" quando tenta abrir um ficheiro

No Linux, receberá uma mensagem de erro que é semelhante ao seguinte:

**\<nome de ficheiro > quota de disco [permissão negada] foi excedida**

### <a name="cause"></a>Causa

Atingiu o limite superior de identificadores abertos em simultâneo permitidos para um ficheiro.

Existe uma quota de 2.000 identificadores abertos num único arquivo. Quando tiver de 2.000 identificadores abertos, é apresentada uma mensagem de erro que diz que a quota é atingida.

### <a name="solution"></a>Solução

Reduzir o número de identificadores abertos em simultâneo por fechar alguns identificadores e, em seguida, repita a operação.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Tornar mais lenta de copiar o arquivo de e para ficheiros do Azure no Linux

- Se não tiver um requisito de tamanho de e/s mínimo específico, recomendamos que utilize 1 MiB como o tamanho de e/s para otimizar o desempenho.
- Se souber o tamanho final de um ficheiro que está a expandir através da utilização de escritas, e seu software não têm problemas de compatibilidade, quando uma cauda implícitas no arquivo contém zeros, em seguida, defina o tamanho do ficheiro com antecedência, em vez de fazer uma gravação de expansão de cada gravação.
- Utilize o método de cópia certo:
    - Uso [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas partilhas de ficheiros.
    - Usar cp com paralelo pode aumentar a velocidade de cópia, o número de threads depende da sua carga de trabalho e caso de utilização. Este exemplo usa seis: `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - Ferramentas de terceiros de código aberto, tais como:
        - [GNU paralelo](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - ordena os arquivos e pacotes-los em partições.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -utiliza Fpart e uma ferramenta de cópia para gerar várias instâncias para migrar dados de src_dir para dst_url.
        - [Várias](https://github.com/pkolano/mutil) -cp com múltiplos threads e md5sum com base no GNU coreutils.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Montar error(115): Operação de agora em curso"quando montar ficheiros do Azure utilizando o SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições de Linux ainda não suportam recursos de criptografia no SMB 3.0. Os usuários podem receber uma mensagem de erro "115" se tentarem ficheiros do Azure de montagem, utilizando o SMB 3.0 devido a um recurso em falta. SMB 3.0 com a encriptação completa é suportada apenas quando estiver a utilizar o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

O recurso de criptografia para SMB 3.0 para Linux foi introduzido no kernel do 4.11. Esta funcionalidade permite a montagem de uma partilha de ficheiros do Azure no local ou a partir de uma região diferente do Azure. Esta funcionalidade está incluída nas distribuições de Linux listadas na [mínimo recomendado de versões com recursos de montagem correspondentes (vs SMB versão 3.0 de SMB versão 2.1)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Outras distribuições requerem kernel 4.11 e versões posteriores.

Se o cliente Linux SMB não suporta a encriptação, ficheiros do Azure de montagem, utilizando o SMB 2.1 a partir de uma VM do Linux do Azure que está no mesmo datacenter como a partilha de ficheiros. Certifique-se de que o [transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição estiver desativada na conta de armazenamento. 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erro "Falha de autorização" ao navegar para uma partilha de ficheiros do Azure no portal

Ao navegar para uma partilha de ficheiros do Azure no portal, poderá receber o erro seguinte:

Falha na autorização  
Não tem acesso

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Fazer com que 1: Sua conta de utilizador não tem acesso à conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para causa 1

Navegue para a conta de armazenamento onde está localizada a partilha de ficheiros do Azure, clique em **controlo de acesso (IAM)** e certifique-se a sua conta de utilizador tem acesso à conta de armazenamento. Para obter mais informações, consulte [como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: Regras de firewall ou de rede virtual estão ativadas na conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para causa 2

Certifique-se de que as regras de rede e de firewall virtual estiverem configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtual está a causar o problema, alterar temporariamente a definição da conta de armazenamento para **permitir o acesso de todas as redes**. Para obter mais informações, consulte [armazenamento do Azure configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento numa partilha de ficheiros do Azure montada numa VM do Linux

### <a name="cause-1-caching"></a>Fazer com que 1: Colocação em cache

Uma causa possível de um desempenho lento está desativada a colocação em cache. Colocação em cache pode ser útil se estiver a aceder repetidamente um arquivo, caso contrário, pode ser uma sobrecarga. Verifique se estiver a utilizar a cache antes de o desativar.

### <a name="solution-for-cause-1"></a>Solução para causa 1

Para verificar se a colocação em cache está desativada, procure o **cache =** entrada.

**Cache = none** indica que a colocação em cache está desativada. Voltar a montar a partilha com o comando de montagem de predefinição ou adicionando explicitamente a **cache = strict** opção para o comando de montagem para garantir que esse padrão de colocação em cache ou o modo de colocação em cache "estrito" estiver ativada.

Em alguns cenários, o **serverino** pode fazer com que a opção de montagem a **ls** comando para executar stat em relação a cada entrada de diretório. Este comportamento resulta na degradação do desempenho quando está a lista um diretório grandes. Pode verificar as opções de montagem em sua **etc/fstab** entrada:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Também pode verificar se as opções corretas estão a ser utilizadas ao executar o **sudo mount | grep cifs** comando e verificar a sua saída. Exemplo de saída é o seguinte:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Se o **cache = strict** ou **serverino** opção é não apresentar, desmonte e montar ficheiros do Azure novamente ao executar o comando de montagem do [documentação](../storage-how-to-use-files-linux.md). Em seguida, verificar que o **etc/fstab** entrada tem as opções corretas.

### <a name="cause-2-throttling"></a>Causa 2: Limitação

É possível que está experimentando a limitação e os pedidos estão a ser enviados para uma fila. Pode verificar isto ao tirar partido [métricas de armazenamento do Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Solução para causa 2

Certifique-se de que a sua aplicação está dentro do [ficheiros do Azure Dimensionar destinos](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Carimbos de data / hora tenham sido perdidos devido copiar os ficheiros do Windows para Linux

Em plataformas Linux/Unix, o **cp -p** comando falhar se o proprietário de diferentes usuários ficheiro 1 e 2 do ficheiro.

### <a name="cause"></a>Causa

O sinalizador force **f** no COPYFILE resulta na execução **cp -p -f** no Unix. Este comando também falha preservar o carimbo de data / hora do ficheiro que não possui.

### <a name="workaround"></a>Solução

Utilize o utilizador de conta de armazenamento para copiar os ficheiros:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível ligar a ou montar uma partilha de ficheiros do Azure

### <a name="cause"></a>Causa

Causas comuns para esse problema são:

- Está a utilizar um cliente de distribuição de Linux incompatível. Recomendamos que utilize as seguintes distribuições do Linux para ligar a uma partilha de ficheiros do Azure:

    |   | SMB 2.1 <br>(Monta em VMs na mesma região do Azure) | SMB 3.0 <br>(Monta no local e em várias regiões) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Utilitários CIFS (cifs utils) não estão instalados no cliente.
- A versão mínima do SMB/CIFS, 2.1, não está instalada no cliente.
- Encriptação SMB 3.0 não é suportada no cliente. Encriptação SMB 3.0 está disponível no Ubuntu 16.4 e versões posteriores, juntamente com SUSE 12.3 e versões posteriores. Outras distribuições requerem kernel 4.11 e versões posteriores.
- Está a tentar ligar a uma conta de armazenamento através da porta TCP 445, que não é suportada.
- Está a tentar ligar a uma partilha de ficheiros do Azure a partir de uma VM do Azure e a VM não está na mesma região que a conta de armazenamento.
- Se o [transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) definição está ativada na conta de armazenamento, ficheiros do Azure irá permitir que apenas as ligações que utilizam SMB 3.0 com a encriptação.

### <a name="solution"></a>Solução

Para resolver o problema, utilize o [ferramenta de resolução de problemas para erros de montagem no Linux dos ficheiros do Azure](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta ferramenta:

* Ajuda-o para validar o cliente com o ambiente.
* Deteta a configuração de cliente incompatível que podem causar a falha de acesso para ficheiros do Azure.
* Fornece orientação prescritiva Self-resolver este problema.
* Recolhe os rastreios de diagnóstico.


## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não é possível aceder '&lt;caminho&gt;": Erro de entrada/saída

Quando tenta para listar ficheiros numa partilha de ficheiros do Azure com o comando de ls, o comando pára quando lista os ficheiros. Receber o erro seguinte:

**ls: não é possível aceder '&lt;caminho&gt;": Erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que tenham uma correção para este problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que são o maior que ou igual a 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não é possível criar links simbólicos - ln: Falha ao criar a ligação simbólica T' ": Operação não suportada

### <a name="cause"></a>Causa
Por predefinição, a montagem de partilhas de ficheiros do Azure no Linux utilizando CIFS não ativa o suporte para links simbólicos (links simbólicos). Verá um erro como este:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Solução
O cliente de Linux CIFS não suporta a criação de links simbólicos do estilo Windows sobre o SMB 2 ou 3 do protocolo. Atualmente, o cliente de Linux suporta outro estilo de links simbólicos, chamados [Minshall + francês links simbólicos](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) para ambos criar e siga as operações. Os clientes que precisam de links simbólicos podem utilizar a opção de montagem de "mfsymlinks". Recomendamos que "mfsymlinks" uma vez que também é o formato que os Macs usam.

Para utilizar a links simbólicos, adicione o seguinte ao final do comando de montagem de CIFS:

```
,mfsymlinks
```

Portanto, o comando é parecido com:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Em seguida, pode criar links simbólicos como sugerido no [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Montar error(112): Anfitrião não está funcional"devido a um limite de tempo de restabelecimento de ligação

Quando o cliente tem estado inativo durante muito tempo, ocorre um erro de montagem "112" no cliente Linux. Depois de um tempo de inatividade expandido, o cliente se desliga e a ligação exceder o tempo limite.  

### <a name="cause"></a>Causa

A ligação pode estar inativa pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem a voltar a estabelecer uma ligação de TCP para o servidor quando é utilizada a opção de montagem "soft" padrão
-   Correções de restabelecimento de ligação recente que não estão presentes nos kernels anteriores

### <a name="solution"></a>Solução

Esse problema de restabelecimento de ligação no kernel do Linux agora é fixado como parte das seguintes alterações:

- [Correção de voltar a ligar a difere da sessão de smb3 voltar a ligar depois restabelecimento de ligação de socket](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Chamar o serviço de eco imediatamente depois de restabelecimento de ligação de socket](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Corrigir uma corrupção de memória durante o restabelecimento de ligação](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Corrigir um possível duplo bloqueio do mutex durante a restabelecer ligação (para o kernel v4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, essas alterações poderão não ser transportadas ainda para todas as distribuições de Linux. Esta correção e outras correções de restabelecimento de ligação podem ser encontradas na [mínimo recomendado de versões com recursos de montagem correspondentes (vs SMB versão 3.0 de SMB versão 2.1)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) secção a [ficheiros do Azure de utilização com o Linux](storage-how-to-use-files-linux.md)artigo. Pode obter esta correção com a atualização para uma destas versões de kernel recomendada.

### <a name="workaround"></a>Solução

Pode contornar este problema, especificando uma montagem do disco rígida. Uma disco rígida montagem força o cliente de aguardar até que é estabelecida uma ligação ou até que seja interrompido explicitamente. Pode usá-lo para evitar erros devido a tempo limite de rede. No entanto, esta solução pode causar a espera indefinida. Esteja preparado para parar as ligações conforme necessário.

Se é possível atualizar para as versões mais recentes do kernel, pode contornar este problema, mantendo um ficheiro na partilha de ficheiros do Azure que escreve a cada 30 segundos ou menos. Tem de ser uma operação de escrita, como a reescrever a data de criação ou modificação no arquivo. Caso contrário, poderá obter resultados em cache e a operação não pode acionar o restabelecimento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.
