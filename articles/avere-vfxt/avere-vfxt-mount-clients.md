---
title: Montar o avere vFXT-Azure
description: Como montar clientes com o avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c461b379629927e8f367fad9bfc70b87413f47b7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255380"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montar o cluster Avere vFXT  

Siga estas etapas para conectar computadores cliente ao cluster vFXT.

1. Decida como balancear a carga do tráfego do cliente entre os nós do cluster. Leia [balancear a carga do cliente](#balance-client-load), abaixo, para obter detalhes. 
1. Identifique o endereço IP e o caminho de junção a serem montados.
1. Emita o [comando Mount](#mount-command-arguments), com os argumentos apropriados.

## <a name="balance-client-load"></a>Balancear a carga do cliente

Para ajudar a balancear as solicitações do cliente entre todos os nós no cluster, você deve montar clientes para a faixa completa de endereços IP voltados para o cliente. Há várias maneiras simples de automatizar essa tarefa.

> [!TIP] 
> Outros métodos de balanceamento de carga podem ser apropriados para sistemas grandes ou complicados; [abra um tíquete de suporte](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para obter ajuda.)
> 
> Se preferir usar um servidor DNS para balanceamento de carga automático do lado do servidor, você deverá configurar e gerenciar seu próprio servidor DNS no Azure. Nesse caso, você pode configurar o DNS Round Robin para o cluster vFXT de acordo com este documento: [configuração de DNS do cluster do avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Script de montagem de cliente com balanceamento de amostra

Este exemplo de código usa endereços IP de cliente como um elemento de randomização para distribuir clientes para todos os endereços IP disponíveis do cluster vFXT.

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

A função acima faz parte do exemplo de lote disponível no site de [exemplos do avere vFXT](https://github.com/Azure/Avere#tutorials) .

## <a name="create-the-mount-command"></a>Criar o comando Mount 

> [!NOTE]
> Se você não criou um novo contêiner de blob ao criar o cluster avere vFXT, siga as etapas em [Configurar o armazenamento](avere-vfxt-add-storage.md) antes de tentar montar clientes.

Em seu cliente, o comando ``mount`` mapeia o servidor virtual (vserver) no cluster vFXT para um caminho no sistema de arquivos local. O formato é ``mount <vFXT path> <local path> {options}``

Há três elementos para o comando mount: 

* caminho vFXT – (uma combinação de endereço IP e caminho de junção de namespace descritos abaixo)
* caminho local-o caminho no cliente 
* opções de comando de montagem – (listadas em [argumentos de comando de montagem](#mount-command-arguments))

### <a name="junction-and-ip"></a>Junção e IP

O caminho vserver é uma combinação de seu *endereço IP* mais o caminho para uma *junção de namespace*. A junção do namespace é um caminho virtual que foi definido quando o sistema de armazenamento foi adicionado.

Se o cluster tiver sido criado com o armazenamento de BLOB, o caminho do namespace será `/msazure`

Exemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se você adicionou o armazenamento depois de criar o cluster, o caminho de junção do namespace corresponderá ao valor definido no **caminho do namespace** ao criar a junção. Por exemplo, se você usou ``/avere/files`` como seu caminho de namespace, os clientes montarão *Endereço_IP*:/avere/files em seu ponto de montagem local.

![Caixa de diálogo "Adicionar nova junção" com/avere/files no campo caminho do namespace](media/avere-vfxt-create-junction-example.png)


O endereço IP é um dos endereços IP voltados para o cliente definido para o vserver. Você pode encontrar o intervalo de IPs voltados para o cliente em dois locais no painel de controle do avere:

* Tabela **VServers** (guia painel)- 

  ![Guia painel do painel de controle avere com a guia VServer selecionada na tabela de dados abaixo do grafo e a seção de endereço IP em círculo](media/avere-vfxt-ip-addresses-dashboard.png)

* Página de configurações de **rede voltadas** para o cliente- 

  ![Configurações > VServer > página de configuração de rede voltada para o cliente com um círculo em volta da seção intervalo de endereços da tabela para um VServer específico](media/avere-vfxt-ip-addresses-settings.png)

Além dos caminhos, inclua os argumentos do [comando Mount](#mount-command-arguments) descritos abaixo ao montar cada cliente.

### <a name="mount-command-arguments"></a>Argumentos de comando de montagem

Para garantir uma montagem de cliente sem interrupção, passe essas configurações e argumentos no comando mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Configurações necessárias | |
--- | --- 
``hard`` | As montagens suaves no cluster vFXT são associadas a falhas de aplicativos e à possível perda de dados. 
``proto=netid`` | Essa opção dá suporte ao tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção dá suporte ao tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Defina ``retry=30`` para evitar falhas de montagem transitórias. (Um valor diferente é recomendado em montagens de primeiro plano.)

| Configurações preferenciais  | |
--- | --- 
``nointr``            | A opção "nointr" é preferida para clientes com kernels herdados (antes de abril de 2008) que dão suporte a essa opção. Observe que a opção "INTR" é o padrão.


## <a name="next-steps"></a>Passos seguintes

Depois de montar clientes, você pode usá-los para preencher o armazenamento de dados de back-end (Filer principal). Consulte estes documentos para saber mais sobre tarefas de configuração adicionais:

* [Mover dados para o File Core do cluster](avere-vfxt-data-ingest.md) -como usar vários clientes e threads para carregar seus dados com eficiência
* [Personalizar o ajuste de cluster](avere-vfxt-tuning.md) – Personalize as configurações de cluster para se adequar à sua carga de trabalho
* [Gerenciar o cluster](avere-vfxt-manage-cluster.md) -como iniciar ou parar o cluster e gerenciar nós
