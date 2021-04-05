---
title: Monte uma Cache Azure HPC
description: Como ligar os clientes a um serviço de Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651433"
---
# <a name="mount-the-azure-hpc-cache"></a>Montar o Azure HPC Cache

Após a criação da cache, os clientes NFS podem aceder-lhe com um `mount` simples comando. O comando liga uma rota específica de armazenamento na Cache Azure HPC a um diretório local na máquina do cliente.

O comando de montagem é composto por estes elementos:

* Um dos endereços de montagem da cache (listado na página geral da cache)
* Um caminho virtual de espaço de nome que definiu para o alvo de armazenamento (listado na página do espaço de identificação do cache)
* O caminho local a utilizar no cliente
* Parâmetros de comando que otimizam o sucesso deste tipo de montagem NFS

A página **de instruções de montagem** do seu cache recolhe as informações e as opções recomendadas para si, e cria um comando de montagem de protótipo que pode copiar. Leia [Utilize o utilitário de instruções de montagem](#use-the-mount-instructions-utility) para obter mais detalhes.

## <a name="prepare-clients"></a>Preparar clientes

Certifique-se de que os seus clientes são capazes de montar a Cache Azure HPC seguindo as diretrizes desta secção.

### <a name="provide-network-access"></a>Fornecer acesso à rede

As máquinas clientes devem ter acesso à rede virtual da cache e à sub-rede privada.

Por exemplo, crie VMs de cliente dentro da mesma rede virtual, ou use um ponto final, gateway ou outra solução na rede virtual para acesso a partir de fora. (Lembre-se que nada mais que a cache em si deve ser hospedado dentro da sub-rede da cache.)

### <a name="install-utilities"></a>Instalar utilitários

Instale o software de utilidade Linux apropriado para suportar o comando de montagem NFS:

* Para Red Hat Enterprise Linux ou SuSE: `sudo yum install -y nfs-utils`
* Para Ubuntu ou Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Criar um caminho local

Crie um percurso de diretório local em cada cliente para ligar à cache. Crie um caminho para cada caminho do espaço de nome que pretende montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

A página [de instruções do Monte](#use-the-mount-instructions-utility) no portal Azure inclui um comando protótipo que pode copiar.

Quando ligar a máquina do cliente à cache, irá associar este caminho a um caminho virtual de espaço de nome que representa uma exportação de alvo de armazenamento. Crie diretórios para cada um dos caminhos virtuais do espaço de nome que o cliente irá utilizar.

## <a name="use-the-mount-instructions-utility"></a>Use o utilitário de instruções de montagem

Pode utilizar a página **de instruções de montagem** no portal Azure para criar um comando de montagem copiável. Abra a página a partir da secção **configuração** da vista cache no portal.

Antes de utilizar o comando de um cliente, certifique-se de que o cliente cumpre os requisitos pré-requisitos e tem o software necessário para utilizar o comando NFS, `mount` conforme descrito acima nos [clientes Preparem.](#prepare-clients)

![screenshot de uma instância cache Azure HPC no portal, com a página de instruções de configuração > monte carregado](media/mount-instructions.png)

Siga este procedimento para criar o comando de montagem.

1. Personalize o campo **de percurso do Cliente.** Este campo dá um comando de exemplo que pode usar para criar um caminho local no cliente. O cliente acede ao conteúdo da Cache Azure HPC localmente neste diretório.

   Clique no campo e edite o comando para conter o nome do diretório que pretende. O nome aparece no final da cadeia depois `sudo mkdir -p`

   ![screenshot do campo de caminho do cliente com cursor posicionado no final](media/mount-edit-client.png)

   Depois de terminar a edição do campo, o comando de montagem na parte inferior da página atualiza com o novo caminho do cliente.

1. Escolha o **endereço de montagem cache** da lista. Este menu lista todos os [pontos](#find-mount-command-components)de montagem do cliente da cache.

   Equilibrar a carga do cliente em todos os endereços de montagem disponíveis para um melhor desempenho da cache.

   ![screenshot do campo de endereço de montagem de cache, com seletor mostrando três endereços IP para escolher](media/mount-select-ip.png)

1. Escolha o caminho virtual do **espaço de identificação** para o cliente. Estes caminhos ligam-se às exportações no sistema de armazenamento back-end.

   ![Screenshot que mostra o campo "Virtual namespace path", com seletor aberto.](media/mount-select-target.png)

   Pode visualizar e alterar os caminhos virtuais do espaço de nome na página do portal **Namespace.** Leia [Configurar o espaço de nome agregado](add-namespace-paths.md) para ver como.

   Para saber mais sobre a funcionalidade agregada de namespace da Azure HPC Cache, leia [Plano do espaço de nome agregado](hpc-cache-namespace.md).

1. O campo **de comando Mount** no passo três povoa automaticamente com um comando de montagem personalizado que utiliza o endereço de montagem, o caminho virtual do espaço de nome e o caminho do cliente que definiu nos campos anteriores.

   Clique no símbolo da cópia para o lado direito do campo para copiá-lo automaticamente na sua área de transferência.

   ![screenshot do campo de comando de montagem do protótipo, mostrando texto hover para o botão "copy to clipboard"](media/mount-command-copy.png)

1. Utilize o comando de montagem copiado na máquina do cliente para o ligar à Cache Azure HPC. Pode emitir o comando diretamente da linha de comando do cliente ou incluir o comando de montagem num script ou modelo de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Compreender a sintaxe do comando de montagem

O comando de montagem tem a seguinte forma:

> sudo mount {*opções*} *cache_mount_address*:/*namespace_path* *local_path*

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Após este comando ter sucesso, o conteúdo da exportação de armazenamento será visível no ``hpccache`` diretório do cliente.

### <a name="mount-command-options"></a>Monte opções de comando

Para uma montagem robusta do cliente, passe estas definições e argumentos no seu comando de montagem:

> montagem -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Definições recomendadas de comando de montagem | Description |
--- | ---
``hard`` | Os suportes macios para a Cache Azure HPC estão associados a falhas de aplicação e possíveis perda de dados.
``proto=tcp`` | Esta opção suporta o manuseamento adequado de erros de rede NFS.
``mountproto=tcp`` | Esta opção suporta o manuseamento adequado de erros de rede para operações de montagem.
``retry=<value>`` | Preparado ``retry=30`` para evitar falhas de montagem transitórias. (Recomenda-se um valor diferente em suportes de primeiro plano.)

### <a name="find-mount-command-components"></a>Encontre componentes de comando de montagem

Se pretender criar um comando de montagem sem utilizar a página **de instruções do Monte,** pode encontrar os endereços de montagem na página **de visão geral** da cache e os caminhos de espaço de nome virtual na página **Namespace.**

![screenshot da página geral da cache de Azure HPC, com uma caixa de destaque em torno da lista de endereços de montagem no direito inferior](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede da cache. Num grupo de recursos, estes NICs estão listados com nomes que terminam `-cluster-nic-` e um número. Não altere ou elimine estas interfaces, ou a cache ficará indisponível.

Os caminhos virtuais do espaço de nome são mostrados na página de definições do **Namespace** da cache.

![screenshot do portal Definições > página Namespace com uma caixa de destaque em torno da primeira coluna da tabela: "Caminho do espaço de nome"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Passos seguintes

* Para mover os dados para os alvos de armazenamento da cache, leia [o novo armazenamento da Azure Blob](hpc-cache-ingest.md).
