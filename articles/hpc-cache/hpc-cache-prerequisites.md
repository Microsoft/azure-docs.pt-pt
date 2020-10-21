---
title: Pré-requisitos da Cache Azure HPC
description: Pré-requisitos para a utilização da Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/03/2020
ms.author: v-erkel
ms.openlocfilehash: 39c890e9e6634413947a96b1ddac608ee44bd9a0
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327404"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Pré-requisitos para cache Azure HPC

Antes de utilizar o portal Azure para criar uma nova Cache Azure HPC, certifique-se de que o seu ambiente cumpre estes requisitos.

## <a name="video-overviews"></a>Visão geral do vídeo

Veja estes vídeos para uma visão geral rápida dos componentes do sistema e do que precisam para trabalhar em conjunto.

(Clique na imagem de vídeo ou no link para assistir.)

* [Como funciona](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - Explica como a Azure HPC Cache interage com o armazenamento e os clientes

  [![imagem miniatura de vídeo: Azure HPC Cache: Como funciona (clique para visitar a página de vídeo)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Pré-requisitos](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - Descreve os requisitos para armazenamento NAS, armazenamento Azure Blob, acesso à rede e acesso ao cliente

  [![imagem miniatura de vídeo: Azure HPC Cache: Pré-requisitos (clique para visitar a página de vídeo)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Leia o resto deste artigo para recomendações específicas.

## <a name="azure-subscription"></a>Subscrição do Azure

Recomenda-se uma subscrição paga.

## <a name="network-infrastructure"></a>Infraestrutura de rede

Devem ser configurado dois pré-requisitos relacionados com a rede antes de poder utilizar o seu cache:

* Uma sub-rede dedicada para a instância cache Azure HPC
* Suporte DNS para que a cache possa aceder ao armazenamento e outros recursos

### <a name="cache-subnet"></a>Sub-rede cache

A Cache Azure HPC necessita de uma sub-rede dedicada com estas qualidades:

* A sub-rede deve ter pelo menos 64 endereços IP disponíveis.
* A sub-rede não pode hospedar quaisquer outros VMs, mesmo para serviços relacionados como máquinas de clientes.
* Se utilizar várias instâncias de Cache Azure HPC, cada uma precisa da sua própria sub-rede.

A melhor prática é criar uma nova sub-rede para cada cache. Pode criar uma nova rede virtual e uma sub-rede como parte da criação da cache.

### <a name="dns-access"></a>Acesso ao DNS

A cache precisa de DNS para aceder a recursos fora da sua rede virtual. Dependendo dos recursos que está a utilizar, poderá necessitar de configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure:

* Para aceder aos pontos finais de armazenamento Azure Blob e outros recursos internos, precisa do servidor DNS baseado em Azure.
* Para aceder ao armazenamento no local, é necessário configurar um servidor DNS personalizado que possa resolver os seus nomes de anfitriões de armazenamento.

Se necessitar apenas de acesso ao armazenamento Blob, pode utilizar o servidor DNS fornecido pelo Azure para o seu cache. No entanto, se precisar de acesso a outros recursos, deverá criar um servidor DNS personalizado e configurá-lo para encaminhar quaisquer pedidos de resolução específicos do Azure para o servidor Azure DNS.

Um servidor DNS simples também pode ser usado para carregar as ligações do cliente de equilíbrio entre todos os pontos de montagem de cache disponíveis.

Saiba mais sobre as redes virtuais Azure e as configurações do servidor DNS na [resolução name para recursos em redes virtuais Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Permissões

Verifique estes pré-requisitos relacionados com a permissão antes de começar a criar o seu cache.

* A instância de cache precisa de ser capaz de criar interfaces de rede virtuais (NICs). O utilizador que criar a cache deve ter privilégios suficientes na subscrição para criar NICs.

* Se utilizar o armazenamento Blob, a Cache Azure HPC necessita de autorização para aceder à sua conta de armazenamento. Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para dar acesso à cache ao seu armazenamento Blob. São necessárias duas funções: Contribuidor de Conta de Armazenamento e Contribuinte de Dados blob de armazenamento.

  Siga as instruções em [Adicionar os alvos de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para adicionar as funções.

## <a name="storage-infrastructure"></a>Infraestrutura de armazenamento

A cache suporta contentores Azure Blob ou exportações de armazenamento de hardware NFS. Adicione os alvos de armazenamento depois de criar a cache.

Cada tipo de armazenamento tem pré-requisitos específicos.

### <a name="blob-storage-requirements"></a>Requisitos de armazenamento de bolhas

Se pretender utilizar o armazenamento Azure Blob com o seu cache, necessita de uma conta de armazenamento compatível e de um recipiente Blob vazio ou de um recipiente que esteja povoado com dados formatados Azure HPC Cache, conforme descrito nos [dados do Move para o armazenamento do Azure Blob](hpc-cache-ingest.md).

Crie a conta antes de tentar adicionar um alvo de armazenamento. Pode criar um novo recipiente quando adicionar o alvo.

Para criar uma conta de armazenamento compatível, utilize estas definições:

* Desempenho: **Standard**
* Tipo de conta: **StorageV2 (finalidade geral v2)**
* Replicação: **Armazenamento localmente redundante (LRS)**
* Nível de acesso (predefinido): **Quente**

É uma boa prática usar uma conta de armazenamento no mesmo local que o seu cache.

Também deve dar acesso à aplicação cache à sua conta de armazenamento Azure, como mencionado nas [Permissões,](#permissions)acima. Siga o procedimento em [Adicionar os alvos de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para dar à cache as funções de acesso necessárias. Se você não é o proprietário da conta de armazenamento, faça o proprietário fazer este passo.

### <a name="nfs-storage-requirements"></a>Requisitos de armazenamento NFS
<!-- linked from configuration.md -->

Se utilizar um sistema de armazenamento NFS (por exemplo, um sistema NAS de hardware no local), certifique-se de que cumpre estes requisitos. Poderá ter de trabalhar com os administradores de rede ou gestores de firewall para o seu sistema de armazenamento (ou centro de dados) para verificar estas definições.

> [!NOTE]
> A criação do alvo de armazenamento falhará se a cache tiver acesso insuficiente ao sistema de armazenamento NFS.

Mais informações estão incluídas na [configuração do Troubleshoot NAS e nos problemas de alvo de armazenamento NFS](troubleshoot-nas.md).

* **Conectividade de rede:** O Cache Azure HPC necessita de acesso de rede de alta largura de banda entre a sub-rede de cache e o centro de dados do sistema NFS. [Recomenda-se o ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou acesso semelhante. Se utilizar uma VPN, poderá ser necessário configurá-lo para fixar o SSS TCP a 1350 para se certificar de que os grandes pacotes não estão bloqueados. Leia [as restrições de tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) para ajudar a resolver as definições de VPN.

* **Acesso portuário:** A cache precisa de acesso a portas TCP/UDP específicas no seu sistema de armazenamento. Diferentes tipos de armazenamento têm diferentes requisitos portuários.

  Para verificar as definições do seu sistema de armazenamento, siga este procedimento.

  * Emita um `rpcinfo` comando ao seu sistema de armazenamento para verificar as portas necessárias. O comando abaixo lista as portas e formatos os resultados relevantes numa tabela. (Utilize o endereço IP do seu sistema no lugar do *termo<storage_IP>.)*

    Pode emitir este comando a qualquer cliente Linux que tenha infraestrutura NFS instalada. Se utilizar um cliente dentro da sub-rede de cluster, também pode ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Certifique-se de que todas as portas devolvidas pela ``rpcinfo`` consulta permitem o tráfego ilimitado da sub-rede da Cache Azure HPC.

  * Se não puder utilizar o `rpcinfo` comando, certifique-se de que estas portas comumente utilizadas permitem o tráfego de entrada e saída:

    | Protocolo | Porta  | Serviço  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montado   |
    | TCP/UDP  | 4047  | status   |

    Alguns sistemas utilizam diferentes números de porta para estes serviços - consulte a documentação do seu sistema de armazenamento para ter a certeza.

  * Verifique as definições da firewall para se certificar de que permitem o tráfego em todas as portas necessárias. Certifique-se de verificar as firewalls utilizadas no Azure, bem como as firewalls no local do seu centro de dados.

* **Acesso ao diretório:** Ativar o `showmount` comando no sistema de armazenamento. A Azure HPC Cache utiliza este comando para verificar se a configuração do alvo de armazenamento aponta para uma exportação válida, e também para garantir que vários suportes não acedam às mesmas subdiretórios (risco de colisão de ficheiros).

  > [!NOTE]
  > Se o seu sistema de armazenamento NFS utilizar o sistema operativo ONTAP 9.2 da NetApp, **não ative `showmount` **. [Contacte o Microsoft Service e o Support](hpc-cache-support-ticket.md) para obter ajuda.

  Saiba mais sobre o acesso à listagem de listas no artigo de [resolução de problemas](troubleshoot-nas.md#enable-export-listing)do alvo de armazenamento NFS .

* **Acesso à raiz** (ler/escrever): A cache liga-se ao sistema back-end como ID 0 do utilizador. Verifique estas definições no seu sistema de armazenamento:
  
  * Ativar `no_root_squash` . Esta opção garante que o utilizador de raiz remota pode aceder a ficheiros propriedade da raiz.

  * Verifique as políticas de exportação para se certificar de que não incluem restrições ao acesso à raiz a partir da sub-rede da cache.

  * Se o seu armazenamento tiver alguma exportação que sejam subdiretivas de outra exportação, certifique-se de que a cache tem acesso à raiz ao segmento mais baixo do caminho. Leia o acesso à raiz nos caminhos do diretório no artigo de resolução de [problemas](troubleshoot-nas.md#allow-root-access-on-directory-paths) do alvo de armazenamento NFS para obter mais detalhes.

* O armazenamento de back-end NFS deve ser uma plataforma de hardware/software compatível. Contacte a equipa da Azure HPC Cache para mais detalhes.

## <a name="set-up-azure-cli-access-optional"></a>Configurar o acesso do Azure CLI (opcional)

Se pretender criar ou gerir a Cache Azure HPC a partir da interface de linha de comando Azure (Azure CLI), tem de instalar o software CLI e a extensão hpc-cache. Siga as instruções na [Configuração Azure CLI para cache Azure HPC](az-cli-prerequisites.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar uma instância cache Azure HPC](hpc-cache-create.md) a partir do portal Azure
