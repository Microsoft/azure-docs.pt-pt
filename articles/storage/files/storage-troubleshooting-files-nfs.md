---
title: Problemas de partilha de ficheiros Azure NFS - Ficheiros Azure
description: Problemas de resolução de problemas Azure NFS ficheiro problemas de partilha.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: f684aff58f441fb0642779e54de39dff941e818c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430667"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Resolução de problemas Azure NFS ações de ficheiros

Este artigo enumera alguns problemas comuns relacionados com as ações de ficheiros Azure NFS. Fornece potenciais causas e soluções quando estes problemas são encontrados.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" falhou: Argumento inválido (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Causa 1: o idmapping não é desativado
Os Ficheiros Azure não permite uid/GID alfanumérico. Portanto, o idmapping deve ser desativado. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Causa 2: o idmapping foi desativado, mas foi reativado depois de encontrar o nome de ficheiro/dir mau
Mesmo que o idmapping tenha sido corretamente desativado, as definições para desativar o idmapping são ultrapassadas em alguns casos. Por exemplo, quando os Ficheiros Azure encontram um mau nome de ficheiro, envia de volta um erro. Ao ver este código de erro em particular, o cliente NFS v 4.1 Linux decide voltar a permitir o idmapping e os pedidos futuros são enviados novamente com UID/GID alfanumérico. Para obter uma lista de caracteres não suportados em Ficheiros Azure, consulte este [artigo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). O cólon é um dos personagens não apoiados. 

### <a name="workaround"></a>Solução
Verifique se o idmapping está desativado e nada está a reativar e, em seguida, executar o seguinte:

- Desmonte a parte
- Desativar o id-mapping com # echo Y > /sys/module/nfs/parâmetros/nfs4_disable_idmapping
- Monte a parte de volta
- Se correr rsync, executar rsync com argumento "-- numérico-ids" do diretório que não têm nenhum mau nome dir/arquivo.

## <a name="unable-to-create-an-nfs-share"></a>Incapaz de criar uma quota NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Causa 1: A subscrição não está ativada

A sua subscrição pode não ter sido registada para a pré-visualização do Azure Files NFS. Terá de executar alguns comandos adicionais a partir de Cloud Shell ou de um terminal local para ativar a funcionalidade.

> [!NOTE]
> Pode ter de esperar até 30 minutos para que a inscrição esteja concluída.


#### <a name="solution"></a>Solução

Utilize o seguinte script para registar a funcionalidade e o fornecedor de recursos, `<yourSubscriptionIDHere>` substitua-o antes de executar o script:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Causa 2: Definições de conta de armazenamento não suportadas

O NFS só está disponível nas contas de armazenamento com a seguinte configuração:

- Nível - Premium
- Tipo de Conta - Arquivamento de Ficheiros
- Regiões - [Lista de regiões apoiadas](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Solução

Siga as instruções do nosso artigo: [Como criar uma partilha NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Causa 3: A conta de armazenamento foi criada antes do registo concluído

Para que uma conta de armazenamento utilize a funcionalidade, esta deve ser criada uma vez que a subscrição tenha concluído o registo para NFS. Pode levar até 30 minutos para a inscrição ser concluída.

#### <a name="solution"></a>Solução

Assim que o registo estiver concluído, siga as instruções do nosso artigo: [Como criar uma partilha NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Não é possível ligar ou montar uma partilha de ficheiros Azure NFS

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Causa 1: O pedido tem origem num cliente numa rede não fidedquiríssia/IP não fidedvo

Ao contrário do SMB, o NFS não tem autenticação baseada no utilizador. A autenticação para uma ação baseia-se na configuração da regra de segurança da rede. Devido a isso, para garantir que apenas as ligações seguras são estabelecidas na sua quota NFS, deve utilizar o ponto final de serviço ou os pontos finais privados. Para aceder a ações a partir de pontos finais privados, tem de configurar a VPN ou a ExpressRoute. Os IPs adicionados à lista de admissões da conta de armazenamento para a firewall são ignorados. Deve utilizar um dos seguintes métodos para configurar o acesso a uma partilha NFS:


- [Ponto final de serviço](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Acessado pelo ponto final público
    - Só disponível na mesma região.
    - O espremiá-lo da VNet não dará acesso à sua parte.
    - Cada rede virtual ou sub-rede deve ser adicionada individualmente à lista de admissões.
    - Para acesso no local, os pontos finais de serviço podem ser usados com ExpressRoute, ponto-a-local e VPNs site-to-site, mas, recomendamos a utilização de ponto final privado, uma vez que é mais seguro.

O diagrama que se segue mostra conectividade utilizando pontos finais públicos.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagrama de conectividade de ponto final público." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - O acesso é mais seguro do que o ponto final de serviço.
    - O acesso à partilha de NFS através de um link privado está disponível dentro e fora da região Azure da conta de armazenamento (cross-region, no local)
    - A rede virtual que espreita com redes virtuais hospedadas no ponto final privado dá acesso à NFS de partilha aos clientes em redes virtuais.
    - Os pontos finais privados podem ser usados com ExpressRoute, ponto a local e VPNs site-to-site.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagrama de conectividade privada de ponto final." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Causa 2: A transferência segura necessária está ativada

A dupla encriptação ainda não é suportada para ações NFS. O Azure fornece uma camada de encriptação para todos os dados em trânsito entre centros de dados Azure usando MACSec. As ações NFS só podem ser acedidas a partir de redes virtuais fidedignas e sobre túneis VPN. Não existe encriptação adicional da camada de transporte nas ações NFS.

#### <a name="solution"></a>Solução

Desative a transferência segura necessária na lâmina de configuração da sua conta de armazenamento.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Screenshot da lâmina de configuração da conta de armazenamento, desativando a transferência segura necessária.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Causa 3: o pacote comum nfs não está instalado
Antes de executar o comando de montagem, instale a embalagem executando o comando específico distro a partir de baixo.

Para verificar se a embalagem NFS está instalada, corra: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Solução

Se a embalagem não for instalada, instale a embalagem na sua distribuição.

##### <a name="ubuntu-or-debian"></a>Ubuntu ou Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+, CentOS 8+

Utilize o gestor de pacotes dnf: `sudo dnf install nfs-common` .

Versões mais antigas da Red Hat Enterprise Linux e CentOS utilizam o gestor de pacotes yum: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Utilize o gestor de pacotes zypper: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Causa 4: Porta de bloqueio de firewall 2049

O protocolo NFS comunica ao seu servidor através da porta 2049, certifique-se de que esta porta está aberta à conta de armazenamento (o servidor NFS).

#### <a name="solution"></a>Solução

Verifique se a porta 2049 está aberta ao seu cliente executando o seguinte comando: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Se o porto não estiver aberto, abra-o.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (ficheiros de lista) mostra resultados incorretos/inconsistentes

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Causa: Inconsistência entre valores em cache e metadados de ficheiros do servidor quando a pega do ficheiro está aberta
Por vezes, o comando "ficheiros de lista" apresenta um tamanho não zero como esperado e no comando de ficheiros da lista seguinte mostra o tamanho 0 ou uma carimbo de tempo muito antigo. Esta é uma questão conhecida devido à inconsistente caching dos valores de metadados de ficheiros enquanto o ficheiro está aberto. Pode utilizar uma das seguintes soluções alternativas para resolver isto:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Solução alternativa 1: Para obter o tamanho do ficheiro, utilize wc -c em vez de ls -l
A utilização do wc-c irá sempre obter o valor mais recente do servidor e não terá qualquer inconsistência.

#### <a name="workaround-2-use-noac-mount-flag"></a>Solução 2: Use a bandeira de montagem "noac"
Remonte o sistema de ficheiros utilizando a bandeira "noac" com o seu comando de montagem. Isto irá sempre obter todos os valores de metadados do servidor. Pode haver alguma sobrecarga de perf menor para todas as operações de metadados se esta solução for utilizada.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
