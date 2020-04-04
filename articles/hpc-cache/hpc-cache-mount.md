---
title: Monte uma cache Azure HPC
description: Como ligar clientes a um serviço Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657368"
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

Crie um caminho de diretório local em cada cliente para se conectar à cache. Crie um caminho para cada alvo de armazenamento que pretende montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Utilize o utilitário de instruções de montagem

Abra a página de **instruções** do Monte a partir da secção **De configuração** da vista cache no portal Azure.

![screenshot de uma instância de Cache Azure HPC no portal, com a página de instruções de Configuração > montagem carregada](media/mount-instructions.png)

A página de comando de montagem inclui informações sobre o processo de montagem do cliente e pré-requisitos, além de campos que pode usar para criar um comando de montagem copiável.

Para utilizar esta página, siga este procedimento:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Reveja os pré-requisitos do cliente e instale `mount` os utilitários necessários para utilizar o comando NFS como descrito acima nos [clientes Preparem](#prepare-clients).

1. Passo um de Montagem do seu sistema de **ficheiros**<!-- label will change --> dá um exemplo de comando para criar o caminho local sobre o cliente. Este é o caminho que o cliente utilizará para aceder aos conteúdos a partir do Cache Azure HPC.

   Note o nome do caminho para que possa modificá-lo no comando, se necessário.

1. Na segunda etapa, selecione um dos endereços IP disponíveis. Todos os [pontos](#find-mount-command-components) de montagem do cliente da cache estão listados aqui. Certifique-se de que tem um sistema para equilibrar a carga entre todos os endereços IP.

1. O campo no passo três povoa automaticamente com um comando de montagem protótipo. Clique no símbolo da cópia no lado direito do campo para copiá-lo automaticamente para a sua área de redação.

   > [!NOTE]
   > Verifique o comando da cópia antes de o utilizar. Você pode precisar personalizar o caminho de montagem do cliente e o caminho de espaço de nome virtual alvo de armazenamento, que ainda não são selecionáveis nesta interface. Também deve atualizar as opções de comando de montagem para refletir as [opções recomendadas](#mount-command-options) abaixo. Leia Compreender a [sintaxe](#understand-mount-command-syntax) de comando para obter ajuda.

1. Utilize o comando de montagem copiado (com editas, se necessário) na máquina cliente para ligá-lo ao alvo de armazenamento no Cache Azure HPC. Pode emitir o comando diretamente a partir da linha de comando do cliente, ou incluir o comando de montagem num script ou modelo de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Compreender a sintaxe do comando do monte

O comando do suporte tem a seguinte forma:

> sudo montagem *cache_mount_address*:/*namespace_path* *local_path* {*opções*}

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

Se pretender criar um comando de montagem sem utilizar a página de **instruções** do Monte, pode encontrar os endereços de montagem na **página** de resumo da cache e os caminhos de espaço de nome virtual na página de alvos de **Armazenamento.**

![screenshot da página de visão geral da instância de Cache Azure HPC, com uma caixa de destaque em torno da lista de endereços de montagem na parte inferior direita](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede da cache. Num grupo de recursos, estes NICs `-cluster-nic-` estão listados com nomes que terminam e um número. Não altere ou elimine estas interfaces, ou a cache ficará indisponível.

Os caminhos de espaço de nome virtual são mostrados na página de alvos de **Armazenamento.** Clique num nome de alvo de armazenamento individual para ver os seus detalhes, incluindo os caminhos agregados do espaço de nome associados.

![screenshot do painel alvo de armazenamento da cache, com uma caixa de destaque em torno de uma entrada na coluna Caminho da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Passos seguintes

* Para mover dados para os alvos de armazenamento da cache, leia [O novo armazenamento da Blob Azure](hpc-cache-ingest.md).
