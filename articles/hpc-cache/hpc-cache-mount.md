---
title: Monte uma cache Azure HPC
description: Como ligar clientes a um serviço Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458399"
---
# <a name="mount-the-azure-hpc-cache"></a>Monte a Cache Azure HPC

Após a criação da cache, os clientes nFS podem acessá-lo com um `mount` simples comando. O comando liga um caminho-alvo de armazenamento específico no Cache Azure HPC a um diretório local na máquina cliente.

O comando do suporte é composto por estes elementos:

* Um dos endereços de montagem do cache (listado na página de visão geral da cache)
* O caminho virtual do espaço de nome que definiu quando criou o alvo de armazenamento
* O caminho local a usar no cliente
* Parâmetros de comando que otimizam o sucesso deste tipo de montagem nFS

A página de **instruções** do Monte para a sua cache recolhe as informações e as opções recomendadas para si, e cria um comando de montagem protótipo que pode copiar. Leia [Utilize o utilitário de instruções de montagem](#use-the-mount-instructions-utility) para obter mais detalhes.

## <a name="prepare-clients"></a>Preparar clientes

Certifique-se de que os seus clientes são capazes de montar o Cache Azure HPC seguindo as orientações nesta secção.

### <a name="provide-network-access"></a>Fornecer acesso à rede

As máquinas de clientes devem ter acesso à rede à rede virtual do cache e à subnet privada.

Por exemplo, criar VMs de clientes dentro da mesma rede virtual, ou usar um ponto final, gateway ou outra solução na rede virtual para acesso a partir do exterior. (Lembre-se que nada além da cache em si deve ser alojada dentro da subnet do cache.)

### <a name="install-utilities"></a>Instalar utilitários

Instale o software de utilidade linux apropriado para suportar o comando de montagem NFS:

* Para Red Hat Enterprise Linux ou SuSE:`sudo yum install -y nfs-utils`
* Para Ubuntu ou Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Criar um caminho local

Crie um caminho de diretório local em cada cliente para se conectar à cache. Crie um caminho para cada caminho espaço-nome que queira montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

A página de [instruções](#use-the-mount-instructions-utility) do Monte no portal Azure inclui um comando protótipo que pode copiar.

Quando ligar a máquina cliente à cache, irá associar este caminho a um caminho de espaço de nome virtual que representa uma exportação de alvo de armazenamento. Crie diretórios para cada um dos caminhos de espaço de nome virtual que o cliente irá utilizar.

## <a name="use-the-mount-instructions-utility"></a>Utilize o utilitário de instruções de montagem

Pode utilizar a página de **instruções** do Monte no portal Azure para criar um comando de montagem copiável. Abra a página a partir da secção **Configure** da vista cache no portal.

Antes de utilizar o comando de um cliente, certifique-se de que o cliente `mount` cumpre os pré-requisitos e dispõe do software necessário para utilizar o comando NFS, tal como descrito acima nos [clientes Preparem](#prepare-clients).

![screenshot de uma instância de Cache Azure HPC no portal, com a página de instruções de Configuração > montagem carregada](media/mount-instructions.png)

Siga este procedimento para criar o comando do suporte.

1. Personalize o campo de **percurso do Cliente.** Este campo dá um comando de exemplo que pode usar para criar um caminho local no cliente. O cliente acede ao conteúdo do Azure HPC Cache localmente neste diretório.

   Clique no campo e edite o comando para conter o nome de diretório que deseja. O nome aparece no final da corda depois`sudo mkdir -p`

   ![screenshot do campo de caminho do cliente com cursor posicionado no final](media/mount-edit-client.png)

   Depois de terminar de editar o campo, o comando de montagem na parte inferior da página atualiza com o novo caminho do cliente.

1. Escolha o **endereço cache mount** da lista. Este menu lista todos os [pontos](#find-mount-command-components)de montagem do cliente da cache.

   Equilibre a carga do cliente em todos os endereços de montagem disponíveis para um melhor desempenho em cache.

   ![screenshot do campo de endereço de montagem cache, com seletor mostrando três endereços IP para escolher](media/mount-select-ip.png)

1. Escolha o **caminho do espaço** de nome Virtual para usar para o cliente. Estes caminhos estão ligados às exportações do sistema de armazenamento de back-end.

   ![screenshot do campo de caminhos espaço nome, com seletor aberto](media/mount-select-target.png)

   Pode ver e alterar caminhos de espaço de nome virtual na página do portal dos alvos de Armazenamento. Leia [Adicionar alvos](hpc-cache-add-storage.md) de armazenamento para ver como.

   Para saber mais sobre a funcionalidade de espaço de nome agregado do Azure HPC Cache, leia [Plano o espaço](hpc-cache-namespace.md)de nome agregado .

1. O campo de **comando mount** no passo três povoa automaticamente com um comando de montagem personalizado que usa o endereço de montagem, caminho de espaço de nome virtual e caminho do cliente que você estabeleceu nos campos anteriores.

   Clique no símbolo da cópia no lado direito do campo para copiá-lo automaticamente para a sua área de redação.

   ![screenshot do campo de caminhos espaço nome, com seletor aberto](media/mount-command-copy.png)

1. Utilize o comando de montagem copiado na máquina cliente para ligá-lo à cache Azure HPC. Pode emitir o comando diretamente a partir da linha de comando do cliente, ou incluir o comando de montagem num script ou modelo de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Compreender a sintaxe do comando do monte

O comando do suporte tem a seguinte forma:

> sudo montagem {*opções*} *cache_mount_address*:/*namespace_path* *local_path*

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Após este comando ter sucesso, o conteúdo da ``hpccache`` exportação de armazenamento será visível no diretório sobre o cliente.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem robusta do cliente, passe estas configurações e argumentos no seu comando de montagem:

> montagem -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/{NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Definições recomendadas de comando de montagem | |
--- | ---
``hard`` | Os suportes macios para a Cache Azure HPC estão associados a falhas de aplicação e possível perda de dados.
``proto=tcp`` | Esta opção suporta o tratamento adequado dos erros de rede NFS.
``mountproto=tcp`` | Esta opção suporta o tratamento adequado dos erros de rede para as operações de montagem.
``retry=<value>`` | Preparar ``retry=30`` para evitar falhas transitórias de montagem. (Recomenda-se um valor diferente em suportes de primeiro plano.)

### <a name="find-mount-command-components"></a>Localizar componentes de comando de montagem

Se pretender criar um comando de montagem sem utilizar a página de **instruções** do Monte, pode encontrar os endereços de montagem na **página** de resumo da cache e os caminhos de espaço de nome virtual na **página-alvo** do Armazenamento.

![screenshot da página de visão geral da instância de Cache Azure HPC, com uma caixa de destaque em torno da lista de endereços de montagem na parte inferior direita](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede da cache. Num grupo de recursos, estes NICs `-cluster-nic-` estão listados com nomes que terminam e um número. Não altere ou elimine estas interfaces, ou a cache ficará indisponível.

Os caminhos do espaço de nome virtual são mostrados na página de detalhes de cada alvo de armazenamento. Clique num nome de alvo de armazenamento individual para ver os seus detalhes, incluindo os caminhos agregados do espaço de nome associados.

![screenshot da página de detalhes de um alvo de armazenamento (cabeçalho "Alvo de armazenamento de atualização"). Há uma caixa de destaque em torno de uma entrada na coluna de caminho espaço de nome virtual da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Passos seguintes

* Para mover dados para os alvos de armazenamento da cache, leia [O novo armazenamento da Blob Azure](hpc-cache-ingest.md).
