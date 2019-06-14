---
title: Montar o vFXT Avere - Azure
description: Como montar os clientes com Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409389"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montar o cluster Avere vFXT  

Siga estes passos para ligar computadores cliente ao seu cluster vFXT.

1. Decidir como tráfego de cliente de balanceamento de carga entre os nós do cluster. Leia [carga de cliente do saldo](#balance-client-load), abaixo, para obter detalhes. 
1. Identifique o caminho de endereço e a junção IP para montar.
1. Problema do [comando de montagem](#mount-command-arguments), com argumentos adequados.

## <a name="balance-client-load"></a>Carga de cliente de saldo

Para ajudar a pedidos de cliente de equilíbrio entre todos os nós do cluster, deve montar os clientes a gama completa de endereços IP com clientes. Existem várias formas simples de automatizar esta tarefa.

> [!TIP] 
> Outros métodos de balanceamento de carga podem ser apropriados para sistemas de grandes porte ou complicados; [abra um pedido de suporte](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para obter ajuda.)
> 
> Se preferir usar um servidor DNS para o balanceamento de carga automático do lado do servidor, tem de configurar e gerir o seu próprio servidor DNS no Azure. Nesse caso, pode configurar round robin DNS para o cluster de vFXT, de acordo com este documento: [Configuração de DNS do cluster Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Exemplo de cliente equilibrada montar o script

Este exemplo de código utiliza endereços IP de cliente como um elemento aleatório para distribuir os clientes a todos os endereços IP do cluster vFXT disponíveis.

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
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

A função acima é parte do Batch. por exemplo disponível no [Avere vFXT exemplos](https://github.com/Azure/Avere#tutorials) site.

## <a name="create-the-mount-command"></a>Criar o comando de montagem 

> [!NOTE]
> Se não tiver criado um novo contentor de BLOBs ao criar o cluster de vFXT Avere, siga os passos em [configurar o armazenamento](avere-vfxt-add-storage.md) antes de tentar montar os clientes.

A partir do cliente, o ``mount`` mapas do comando do virtual server (vserver) no cluster vFXT para um caminho no sistema de ficheiros local. O formato é ``mount <vFXT path> <local path> {options}``

Existem três elementos para o comando de montagem: 

* caminho de vFXT - (uma combinação de IP endereço e espaço de nomes junção do caminho descrito abaixo)
* caminho local - o caminho no cliente 
* Opções de comando - de montagem (listados na [argumentos de comando de montagem](#mount-command-arguments))

### <a name="junction-and-ip"></a>Junção e IP

O caminho de vserver é uma combinação de seus *endereço IP* além do caminho para um *junção de espaço de nomes*. A junção de espaço de nomes é um caminho virtual que foi definido quando o sistema de armazenamento foi adicionado.

Se o cluster foi criado com o armazenamento de BLOBs, o caminho de espaço de nomes é `/msazure`

Exemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se tiver adicionado armazenamento depois de criar o cluster, o caminho de junção do espaço de nomes corresponde ao valor definido nas **caminho de espaço de nomes** ao criar a junção. Por exemplo, se utilizou ``/avere/files`` como o caminho de espaço de nomes, os clientes se montaria *IP_address*: / avere/ficheiros para o ponto de montagem local.

![Caixa de diálogo "Adicionar novo junção" com os ficheiros/avere/no campo do caminho de espaço de nomes](media/avere-vfxt-create-junction-example.png)


O endereço IP é um dos endereços IP com clientes definidos para o vserver. Pode encontrar o intervalo de IPs em dois locais no painel de controlo de Avere de com de clientes:

* **VServers** tabela (separador Dashboard) - 

  ![Separador dashboard do painel de controle Avere com o separador de VServer selecionado na tabela de dados abaixo o gráfico e a secção de endereço IP com círculos](media/avere-vfxt-ip-addresses-dashboard.png)

* **Rede com acesso do cliente** página de definições - 

  ![Definições > VServer > página de configuração de rede com acesso do cliente com um círculo à volta a secção de intervalo de endereços da tabela para um determinado vserver](media/avere-vfxt-ip-addresses-settings.png)

Os caminhos, além de incluir o [argumentos de comando de montagem](#mount-command-arguments) descrito a seguir ao montar a cada cliente.

### <a name="mount-command-arguments"></a>Argumentos de comando de montagem

Para garantir uma montagem de cliente totalmente integrado, passa estas definições e os argumentos do comando de montagem: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Definições necessárias | |
--- | --- 
``hard`` | Monta de forma recuperável para o cluster vFXT está associadas com falhas da aplicação e possível perda de dados. 
``proto=netid`` | Esta opção suporta manipulação adequada de erros de rede NFS.
``mountproto=netid`` | Esta opção suporta manipulação adequada de erros de rede para operações de montagem.
``retry=n`` | Definir ``retry=30`` para evitar falhas de montagem transitório. (Um valor diferente é recomendado em primeiro plano monta.)

| Definições preferenciais  | |
--- | --- 
``nointr``            | A opção "nointr" é preferencial para os clientes com kernels herdados (antes de Abril de 2008) que suportam esta opção. Observe que a opção "intr" é a predefinição.


## <a name="next-steps"></a>Passos Seguintes

Após ter montado clientes, pode utilizá-los para preencher o armazenamento de dados de back-end (filtro de core). Consulte estes documentos para saber mais sobre as tarefas de configuração adicionais:

* [Mover dados para o filtro de núcleo de cluster](avere-vfxt-data-ingest.md) -como utilizar vários clientes e os threads de forma eficiente carregar os dados
* [Personalizar o ajuste de cluster](avere-vfxt-tuning.md) -personalizar as definições do cluster de acordo com a sua carga de trabalho
* [Gerir o cluster](avere-vfxt-manage-cluster.md) -como iniciar ou parar o cluster e gerir os nós
