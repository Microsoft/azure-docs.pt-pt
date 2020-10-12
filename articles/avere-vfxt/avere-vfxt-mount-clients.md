---
title: Monte o Avere vFXT - Azure
description: Saiba como conectar os clientes ao seu cluster vFXT em Avere vFXT para a Azure e como equilibrar o tráfego de clientes entre os seus nós de cluster.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272576"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montar o cluster Avere vFXT

Siga estes passos para ligar as máquinas clientes ao seu cluster vFXT.

1. Decida como equilibrar o tráfego de clientes entre os seus nós de cluster. Leia [a carga do cliente balance,](#balance-client-load)abaixo, para mais detalhes.
1. Identifique o endereço IP e o caminho de junção para montar.
1. Emite o [comando de montagem,](#mount-command-arguments)com argumentos apropriados.

## <a name="balance-client-load"></a>Equilibrar carga do cliente

Para ajudar a equilibrar os pedidos dos clientes entre todos os nós do cluster, deverá montar os clientes na gama completa de endereços IP voltados para o cliente. Existem várias formas simples de automatizar esta tarefa.

> [!TIP]
> Outros métodos de equilíbrio de carga podem ser adequados para sistemas grandes ou complicados; [abrir um bilhete de apoio](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para ajuda.)
>
> Se preferir utilizar um servidor DNS para equilibrar a carga automática do servidor, tem de configurar e gerir o seu próprio servidor DNS dentro do Azure. Nesse caso, pode configurar DNS round-robin para o cluster vFXT de acordo com este documento: [Configuração DNS cluster Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Script de montagem de cliente equilibrado amostra

Este exemplo de código utiliza endereços IP do cliente como elemento aleatório para distribuir clientes a todos os endereços IP disponíveis do cluster vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

A função acima faz parte do exemplo do Lote disponível no site [de exemplos Avere vFXT.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>Criar o comando de montagem

> [!NOTE]
> Se não criou um novo recipiente Blob ao criar o seu cluster Avere vFXT, adicione os sistemas de armazenamento descritos no [armazenamento Configure](avere-vfxt-add-storage.md) antes de tentar montar clientes.

A partir do seu cliente, o ``mount`` comando mapeia o servidor virtual (vserver) no cluster vFXT para um caminho no sistema de ficheiros local. O formato é ``mount <vFXT path> <local path> {options}``

O comando de montagem tem três elementos:

* vFXT path - uma combinação de um endereço IP e caminho de junção namespace no cluster 9 descrito abaixo)
* caminho local - o caminho no cliente
* opções de comando de montagem - listados em [argumentos de comando de monte](#mount-command-arguments)

### <a name="junction-and-ip"></a>Junção e IP

O caminho vserver é uma combinação do seu *endereço IP* mais o caminho para uma *junção de espaço de nome*. A junção do espaço de nome é um caminho virtual que foi definido quando o sistema de armazenamento foi adicionado.

Se o seu cluster foi criado com armazenamento Blob, o caminho do espaço de nome para aquele recipiente é `/msazure`

Exemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se adicionar armazenamento após a criação do cluster, o caminho de junção namespace é o valor que definiu no **caminho namespace** ao criar a junção. Por exemplo, se usasse ``/avere/files`` como caminho do espaço de identificação, os seus clientes montariam *IP_address*:/avere/ficheiros para o seu ponto de montagem local.

![Diálogo "Adicionar nova junção" com /avere/ficheiros no campo do caminho do espaço de nome](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

O endereço IP é um dos endereços IP virados para o cliente definidos para o vserver. Pode encontrar a gama de IPs voltados para o cliente em dois locais do Painel de Controlo de Avere:

* **Tabela VServers** (separador dashboard) -

  ![Separador do painel de controlo avere com o separador VServer selecionado na tabela de dados abaixo do gráfico e a secção de endereço IP circulou](media/avere-vfxt-ip-addresses-dashboard.png)

* **Página de** definições de rede virada para o cliente -

  ![As definições > VServer > página de configuração de rede virada para o cliente com um círculo em torno da secção de alcance de endereço da tabela para um vserver particular](media/avere-vfxt-ip-addresses-settings.png)

Além dos caminhos, inclua os argumentos de comando do [Monte descritos](#mount-command-arguments) abaixo ao montar cada cliente.

### <a name="mount-command-arguments"></a>Monte argumentos de comando

Para garantir uma montagem de cliente sem emenda, passe estas definições e argumentos no seu comando de montagem:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações necessárias | Descrição |
--- | ---
``hard`` | Os suportes macios ao cluster vFXT estão associados a falhas de aplicação e possíveis perda de dados.
``proto=netid`` | Esta opção suporta o manuseamento adequado de erros de rede NFS.
``mountproto=netid`` | Esta opção suporta o manuseamento adequado de erros de rede para operações de montagem.
``retry=n`` | Preparado ``retry=30`` para evitar falhas de montagem transitórias. (Recomenda-se um valor diferente em suportes de primeiro plano.)

## <a name="next-steps"></a>Passos seguintes

Depois de ter clientes montados, pode usá-los para copiar dados para um novo recipiente de armazenamento Blob no seu cluster. Se não precisar de preencher novos armazenamentos, leia os outros links para saber mais sobre tarefas adicionais de configuração:

* [Mover dados para um ficheiro core cluster](avere-vfxt-data-ingest.md) - Como usar vários clientes e linhas para carregar eficientemente os seus dados para um novo ficheiro principal
* [Personalizar a sintonização do cluster](avere-vfxt-tuning.md) - Adapte as configurações do cluster para se adequar à sua carga de trabalho
* [Gerir o cluster](avere-vfxt-manage-cluster.md) - Como iniciar ou parar o cluster e gerir os nosdes
