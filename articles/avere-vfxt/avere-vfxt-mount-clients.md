---
title: Monte o Avere vFXT - Azure
description: Como montar clientes com Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76153416"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montar o cluster Avere vFXT

Siga estes passos para ligar as máquinas do cliente ao seu cluster vFXT.

1. Decida como equilibrar o tráfego de clientes entre os seus nós de cluster. Leia [Balance a carga do cliente,](#balance-client-load)abaixo, para mais detalhes.
1. Identifique o endereço IP e o caminho de junção para montar.
1. Emita o comando do [monte,](#mount-command-arguments)com argumentos apropriados.

## <a name="balance-client-load"></a>Equilibrar a carga do cliente

Para ajudar a equilibrar os pedidos dos clientes entre todos os nós do cluster, deverá montar os clientes para toda a gama de endereços IP voltados para o cliente. Existem várias formas simples de automatizar esta tarefa.

> [!TIP]
> Outros métodos de equilíbrio da carga podem ser adequados para sistemas grandes ou complicados; [abrir um bilhete](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) de apoio para ajuda.)
>
> Se preferir utilizar um servidor DNS para equilibrar a carga automática do lado do servidor, tem de configurar e gerir o seu próprio servidor DNS dentro do Azure. Nesse caso, pode configurar DNS de robin redondo para o cluster vFXT de acordo com este documento: [Configuração DNS do cluster Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Exemplo de suporte de cliente equilibrado script

Este exemplo de código utiliza os endereços IP do cliente como um elemento aleatório para distribuir clientes a todos os endereços IP disponíveis do cluster vFXT.

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

A função acima faz parte do exemplo do Lote disponível no site de [exemplos Avere vFXT.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>Criar o comando do suporte

> [!NOTE]
> Se não criou um novo recipiente Blob ao criar o seu cluster Avere vFXT, adicione sistemas de armazenamento conforme descrito no [armazenamento configure](avere-vfxt-add-storage.md) antes de tentar montar clientes.

Do seu cliente, o ``mount`` comando mapeia o servidor virtual (vserver) no cluster vFXT para um caminho no sistema de ficheiros local. O formato é``mount <vFXT path> <local path> {options}``

O comando do suporte tem três elementos:

* vFXT caminho - uma combinação de um endereço IP e caminho de junção espaço de nome no cluster 9descrito abaixo)
* caminho local - o caminho no cliente
* montar opções de comando - listados nos argumentos de [comando do Monte](#mount-command-arguments)

### <a name="junction-and-ip"></a>Junção e IP

O caminho do vserver é uma combinação do seu *endereço IP* mais o caminho para uma *junção espaço-nome*. A junção espaço de nome é um caminho virtual que foi definido quando o sistema de armazenamento foi adicionado.

Se o seu cluster foi criado com armazenamento Blob, o caminho espaço de nome para esse recipiente é`/msazure`

Exemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se adicionou armazenamento após a criação do cluster, o caminho de junção espaço de nome é o valor que definiu no **caminho namespace** ao criar a junção. Por exemplo, se ``/avere/files`` usasse como caminho do espaço de nome, os seus clientes montariam *IP_address*:/avere/ficheiros até ao ponto de montagem local.

![Diálogo "Adicionar nova junção" com /avere/ficheiros no campo de caminho espaço de nome](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

O endereço IP é um dos endereços IP orientados para o cliente definidos para o servidor de vserver. Você pode encontrar a gama de IPs voltados para o cliente em dois lugares no Painel de Controlo de Avere:

* **Tabela VServers** (separador dashboard) -

  ![Separador de painel do Painel de Controlo Avere com o separador VServer selecionado na tabela de dados abaixo do gráfico, e a secção de endereçoIP circulou](media/avere-vfxt-ip-addresses-dashboard.png)

* **Página** de definições de rede virada para o cliente -

  ![Definições > Página de configuração da Rede de Localização do Cliente > VServer com um círculo em torno da secção gama de endereços da tabela para um servidor de servidor específico](media/avere-vfxt-ip-addresses-settings.png)

Além dos caminhos, inclua os argumentos de [comando mount descritos](#mount-command-arguments) abaixo ao montar cada cliente.

### <a name="mount-command-arguments"></a>Monte argumentos de comando

Para garantir uma montagem perfeita do cliente, passe estas configurações e argumentos no seu comando de montagem:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Definições necessárias | |
--- | ---
``hard`` | Os suportes macios para o cluster vFXT estão associados a falhas de aplicação e possível perda de dados.
``proto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede NFS.
``mountproto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede para as operações de montagem.
``retry=n`` | Preparar ``retry=30`` para evitar falhas transitórias de montagem. (Recomenda-se um valor diferente em suportes de primeiro plano.)

## <a name="next-steps"></a>Passos seguintes

Depois de ter clientes montados, pode usá-los para copiar dados para um novo recipiente de armazenamento Blob no seu cluster. Se não precisar de preencher novos armazenamentos, leia os outros links para aprender sobre tarefas adicionais de configuração:

* [Mova os dados para um ficheiro core de cluster](avere-vfxt-data-ingest.md) - Como usar vários clientes e fios para carregar eficientemente os seus dados para um novo ficheiro principal
* [Personalizar a finação do cluster](avere-vfxt-tuning.md) - Adapte as definições do cluster para se adequar à sua carga de trabalho
* [Gerir o cluster](avere-vfxt-manage-cluster.md) - Como iniciar ou parar o cluster e gerir nós
