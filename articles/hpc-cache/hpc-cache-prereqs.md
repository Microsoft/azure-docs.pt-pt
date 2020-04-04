---
title: Pré-requisitos de cache Azure HPC
description: Pré-requisitos para a utilização de Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 6da35cb60dc5f22be01ae25393bd62327db64867
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655648"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Pré-requisitos para Cache Azure HPC

Antes de utilizar o portal Azure para criar um novo Azure HPC Cache, certifique-se de que o seu ambiente cumpre estes requisitos.

## <a name="azure-subscription"></a>Subscrição do Azure

Recomenda-se uma subscrição paga.

> [!NOTE]
> Durante os primeiros meses do lançamento da GA, a equipa azure HPC Cache deve adicionar a sua subscrição à lista de acesso antes de poder ser usada para criar uma instância de cache. Este procedimento ajuda a garantir que cada cliente obtém uma capacidade de resposta de alta qualidade a partir dos seus caches. Preencha [este formulário](https://aka.ms/onboard-hpc-cache) para solicitar acesso.

## <a name="network-infrastructure"></a>Infraestrutura de rede

Devem ser criados dois pré-requisitos relacionados com a rede antes de poder utilizar o seu cache:

* Uma subnet dedicada para a instância Azure HPC Cache
* Suporte dNS para que a cache possa aceder ao armazenamento e outros recursos

### <a name="cache-subnet"></a>Sub-rede cache

O Azure HPC Cache precisa de uma sub-rede dedicada com estas qualidades:

* A sub-rede deve ter pelo menos 64 endereços IP disponíveis.
* A subnet não pode acolher outros VMs, mesmo para serviços relacionados como máquinas de clientes.
* Se utilizar várias instâncias de Cache Azure HPC, cada uma precisa da sua própria sub-rede.

A melhor prática é criar uma nova sub-rede para cada cache. Pode criar uma nova rede virtual e subrede como parte da criação da cache.

### <a name="dns-access"></a>Acesso ao DNS

A cache precisa de DNS para aceder a recursos fora da sua rede virtual. Dependendo dos recursos que está a utilizar, poderá necessitar de configurar um servidor DNS personalizado e configurar o reencaminhamento entre esse servidor e os servidores DNS do Azure:

* Para aceder aos pontos finais de armazenamento do Azure Blob e a outros recursos internos, necessita do servidor DNS baseado no Azure.
* Para aceder ao armazenamento no local, precisa configurar um servidor DNS personalizado que possa resolver os nomes dos seus anfitriões de armazenamento.

Se necessitar apenas de acesso ao armazenamento Blob, pode utilizar o servidor DNS fornecido por Predefinido para o seu cache. No entanto, se necessitar de acesso a outros recursos, deverá criar um servidor DNS personalizado e configurá-lo para encaminhar quaisquer pedidos de resolução específicos do Azure para o servidor DNS do Azure.

Um servidor DNS simples também pode ser usado para carregar as conexões de clientes de equilíbrio entre todos os pontos de montagem de cache disponíveis.

Saiba mais sobre redes virtuais Azure e configurações de servidorD na resolução de [nome para recursos em redes virtuais Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Permissões

Verifique estes pré-requisitos relacionados com a permissão antes de começar a criar a sua cache.

* A instância de cache precisa de ser capaz de criar interfaces de rede virtuais (NICs). O utilizador que cria a cache deve ter privilégios suficientes na subscrição para criar NICs.

* Se utilizar o armazenamento Blob, o Azure HPC Cache necessita de autorização para aceder à sua conta de armazenamento. Utilize o controlo de acesso baseado em funções (RBAC) para dar acesso ao cache ao seu armazenamento Blob. São necessárias duas funções: Contribuinte de Conta de Armazenamento e Contribuinte de Dados blob de armazenamento.

  Siga as instruções em [Adicionar alvos](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) de armazenamento para adicionar as funções.

## <a name="storage-infrastructure"></a>Infraestrutura de armazenamento

A cache suporta contentores Azure Blob ou exportações de armazenamento de hardware NFS. Adicione os alvos de armazenamento depois de criar a cache.

Cada tipo de armazenamento tem pré-requisitos específicos.

### <a name="blob-storage-requirements"></a>Requisitos de armazenamento de blob

Se pretender utilizar o armazenamento Azure Blob com a sua cache, necessita de uma conta de armazenamento compatível e de um recipiente Blob vazio ou de um recipiente que esteja povoado com dados formados Azure HPC Cache, conforme descrito nos [dados do Move para o armazenamento de Blob Azure.](hpc-cache-ingest.md)

Crie a conta antes de tentar adicionar um alvo de armazenamento. Pode criar um novo recipiente quando adicionar o alvo.

Para criar uma conta de armazenamento compatível, utilize estas definições:

* Desempenho: **Standard**
* Tipo de conta: **StorageV2 (finalidade geral v2)**
* Replicação: **Armazenamento localmente redundante (LRS)**
* Nível de acesso (padrão): **Quente**

É uma boa prática usar uma conta de armazenamento no mesmo local que a sua cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

Também deve dar à aplicação cache acesso à sua conta de armazenamento Azure, conforme mencionado em [Permissões,](#permissions)acima. Siga o procedimento em [Adicionar alvos](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) de armazenamento para dar à cache as funções de acesso necessárias. Se não é o proprietário da conta de armazenamento, mande o proprietário fazer este passo.

### <a name="nfs-storage-requirements"></a>Requisitos de armazenamento nFS

Se utilizar um sistema de armazenamento NFS (por exemplo, um sistema NAS de hardware no local), certifique-se de que satisfaz estes requisitos. Poderá ter de trabalhar com os administradores de rede ou gestores de firewall para o seu sistema de armazenamento (ou data center) para verificar estas definições.

> [!NOTE]
> A criação do alvo de armazenamento falhará se a cache não tiver acesso suficiente ao sistema de armazenamento NFS.

Mais informações estão incluídas na [configuração da Troubleshoot NAS e nos problemas de alvo de armazenamento NFS.](troubleshoot-nas.md)

* **Conectividade da rede:** O Cache Azure HPC necessita de acesso de rede de alta largura de banda entre a sub-rede cache e o centro de dados do sistema NFS. [Recomenda-se](https://docs.microsoft.com/azure/expressroute/) o ExpressRoute ou acesso semelhante. Se utilizar uma VPN, poderá ser necessário configurá-lo para fixar o TCP MSS em 1350 para se certificar de que os pacotes grandes não estão bloqueados. Leia as restrições de tamanho do [pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) para obter uma ajuda adicional para resolver as definições vpn.

* **Acesso portuário:** A cache precisa de acesso a portas específicas de TCP/UDP no seu sistema de armazenamento. Diferentes tipos de armazenamento têm diferentes requisitos de porta.

  Para verificar as definições do seu sistema de armazenamento, siga este procedimento.

  * Emita um `rpcinfo` comando ao seu sistema de armazenamento para verificar as portas necessárias. O comando abaixo lista as portas e formatos os resultados relevantes numa tabela. (Utilize o endereço IP do seu sistema no lugar do *termo><storage_IP.)*

    Pode emitir este comando de qualquer cliente Linux que tenha a infraestrutura NFS instalada. Se utilizar um cliente dentro da sub-rede do cluster, também pode ajudar a verificar a conectividade entre a subnet e o sistema de armazenamento.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Certifique-se de que todas as ``rpcinfo`` portas devolvidas pela consulta permitem tráfego ilimitado a partir da subnet azure HPC Cache.

  * Se não puder utilizar `rpcinfo` o comando, certifique-se de que estas portas comumente utilizadas permitem o tráfego de entrada e saída:

    | Protocolo | Porta  | Serviço  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montado   |
    | TCP/UDP  | 4047  | status   |

    Alguns sistemas utilizam diferentes números de porta para estes serviços - consulte a documentação do seu sistema de armazenamento para ter certeza.

  * Verifique as definições de firewall para se certificar de que permitem o tráfego em todas estas portas necessárias. Certifique-se de verificar as firewalls utilizadas no Azure, bem como as firewalls no local no seu centro de dados.

* **Acesso ao diretório:** Ativar `showmount` o comando no sistema de armazenamento. O Azure HPC Cache utiliza este comando para verificar se a configuração do seu alvo de armazenamento aponta para uma exportação válida, e também para garantir que vários suportes não acedem aos mesmos subdiretórios (risco de colisão de ficheiros).

  > [!NOTE]
  > Se o seu sistema de armazenamento NFS utilizar o sistema operativo ONTAP 9.2 da NetApp, **não ativa `showmount` **. [Contacte](hpc-cache-support-ticket.md) o Serviço microsoft e o Suporte para obter ajuda.

  Saiba mais sobre o acesso à listagem de listas de diretórios no artigo de resolução de [problemas](troubleshoot-nas.md#enable-export-listing)do alvo de armazenamento nFS .

* **Acesso à raiz** (ler/escrever): A cache liga-se ao sistema de back-end como ID 0 do utilizador. Verifique estas definições no seu sistema de armazenamento:
  
  * Ativar `no_root_squash`. Esta opção garante que o utilizador da raiz remota pode aceder a ficheiros propriedade da raiz.

  * Verifique as políticas de exportação para se certificar de que não incluem restrições ao acesso à raiz a partir da subnet do cache.

  * Se o seu armazenamento tiver alguma exportação que seja subdirectide de outra exportação, certifique-se de que a cache tem acesso raiz ao segmento mais baixo do caminho. Leia [o acesso raiz aos caminhos](troubleshoot-nas.md#allow-root-access-on-directory-paths) do diretório no artigo de resolução de problemas do nfs de armazenamento para mais detalhes.

* O armazenamento traseiro da NFS deve ser uma plataforma de hardware/software compatível. Contacte a equipa azure HPC Cache para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Crie uma instância de Cache Azure HPC](hpc-cache-create.md) a partir do portal Azure
