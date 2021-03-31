---
title: Utilize ficheiros Azure HPC Cache e Azure NetApp
description: Como utilizar o Azure HPC Cache para melhorar o acesso aos dados armazenados com ficheiros Azure NetApp
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86497017"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Use cache Azure HPC com ficheiros Azure NetApp

Pode utilizar [os Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) como alvo de armazenamento para o seu Cache Azure HPC. Este artigo explica como os dois serviços podem trabalhar em conjunto, e dá dicas para os configurar.

O Azure NetApp Files combina o seu sistema operativo ONTAP com a escalabilidade e velocidade do Microsoft Azure. Esta combinação permite que os utilizadores transloquem os fluxos de trabalho estabelecidos para a nuvem sem reescrever o código.

A adição de um componente cache Azure HPC pode melhorar o acesso ao ficheiro apresentando vários volumes de Ficheiros Azure NetApp num espaço de nome agregado. Pode fornecer caching de borda para volumes localizados em uma região de serviço diferente. Também pode melhorar o desempenho da procura de volumes criados a níveis de serviço de nível inferior para poupar custos.

## <a name="overview"></a>Descrição Geral

Para utilizar um sistema Azure NetApp Files como armazenamento back-end com cache Azure HPC, siga este processo.

1. Crie o sistema E volumes Azure NetApp De acordo com as orientações no [Plano do seu sistema, abaixo](#plan-your-azure-netapp-files-system).
1. Crie a Cache Azure HPC na região onde necessita de acesso a ficheiros. (Utilize as instruções em [Criar uma Cache Azure HPC](hpc-cache-create.md).)
1. [Defina os alvos de armazenamento](#create-storage-targets-in-the-cache) na cache que apontam para os volumes dos Ficheiros Azure NetApp. Crie um alvo de armazenamento de cache para cada endereço IP único utilizado para aceder aos volumes.
1. Os clientes [montam a Cache Azure HPC](#mount-storage-targets) em vez de montarem os volumes dos Ficheiros Azure NetApp diretamente.

## <a name="plan-your-azure-netapp-files-system"></a>Planeie o seu sistema Azure NetApp Files

Ao planear o seu sistema Azure NetApp Files, preste atenção aos itens desta secção para se certificar de que pode integrá-lo suavemente com a Cache Azure HPC.

Leia também a documentação dos [Ficheiros Azure NetApp](../azure-netapp-files/index.yml) antes de criar volumes para utilização com cache Azure HPC.

### <a name="nfs-client-access-only"></a>Acesso ao cliente NFS apenas

A Azure HPC Cache suporta atualmente apenas o acesso NFS. Não pode ser utilizado com volumes de bits do modo SMB ACL ou POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Sub-rede exclusiva para ficheiros Azure NetApp

O Azure NetApp Files utiliza uma única sub-rede delegada para os seus volumes. Nenhum outro recurso pode usar esta sub-rede. Além disso, apenas uma sub-rede numa rede virtual pode ser utilizada para ficheiros Azure NetApp. Saiba mais sobre [o planeamento da rede Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Tamanho da sub-rede delegada

Utilize o tamanho mínimo para a sub-rede delegada ao criar um sistema Azure NetApp Files para utilização com cache Azure HPC.

O tamanho mínimo, especificado com a massa líquida /28, fornece 16 endereços IP. Na prática, o Azure NetApp Files utiliza apenas três dos endereços IP disponíveis para acesso ao volume. Isto significa que só precisa de criar três alvos de armazenamento na sua Cache Azure HPC para cobrir todos os volumes.

Se a sub-rede delegada for demasiado grande, é possível que os volumes dos Ficheiros Azure NetApp utilizem mais endereços IP do que uma única instância de Cache Azure HPC pode lidar. Uma única cache pode ter no máximo 10 alvos de armazenamento.

O exemplo de arranque rápido na documentação do Azure NetApp Files utiliza 10.7.0.0/16 para a sub-rede delegada, que dá uma sub-rede demasiado grande.

### <a name="capacity-pool-service-level"></a>Nível de serviço de piscina de capacidade

Ao escolher o nível de serviço para o seu pool de capacidade, considere o seu fluxo de trabalho. Se escrever frequentemente dados para o volume de Ficheiros Azure NetApp, o desempenho da cache pode ser restringido se o tempo de reversão for lento. Escolha um alto nível de serviço para volumes que terão escritas frequentes.

Volumes com baixos níveis de serviço também podem mostrar algum atraso no início de uma tarefa enquanto o cache pré-preenche o conteúdo. Depois de a cache estar a funcionar com um bom conjunto de ficheiros, o atraso deve tornar-se irrepreocupável.

É importante planear o nível de serviço de piscina de capacidade antes do tempo, porque não pode ser alterado após a criação. Um novo volume teria de ser criado num conjunto de capacidades diferentes, e os dados copiados.

Note que pode alterar a quota de armazenamento de um volume e o tamanho da piscina de capacidade sem perturbar o acesso.

## <a name="create-storage-targets-in-the-cache"></a>Criar alvos de armazenamento na cache

Depois de o seu sistema Azure NetApp Files ser configurado e a Cache Azure HPC ser criada, defina alvos de armazenamento na cache que apontam para os volumes do sistema de ficheiros.

Crie um alvo de armazenamento para cada endereço IP utilizado pelos volumes dos seus Ficheiros Azure NetApp. O endereço IP está listado na página de instruções de montagem do volume.

Se vários volumes partilharem o mesmo endereço IP, pode utilizar um alvo de armazenamento para todos eles.  

Siga as [instruções de montagem na documentação do Azure NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para encontrar os endereços IP para utilizar.

Também pode encontrar endereços IP com o Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Os nomes de exportação no sistema Azure NetApp Files têm um único componente de caminho. Não tente criar um alvo de armazenamento para a exportação de raiz ``/`` em Ficheiros Azure NetApp, porque essa exportação não fornece acesso a ficheiros.

Não existem restrições especiais nos caminhos virtuais do espaço para estes alvos de armazenamento.

## <a name="mount-storage-targets"></a>Monte alvos de armazenamento

As máquinas de clientes devem montar a cache em vez de montar os volumes dos Ficheiros Azure NetApp diretamente. Siga as instruções no [Monte da Cache Azure HPC](hpc-cache-mount.md).

## <a name="next-steps"></a>Passos seguintes

* Ler mais sobre configuração e utilização de [ficheiros Azure NetApp](../azure-netapp-files/index.yml)
* Para ajudar a planear e configurar o seu sistema Azure HPC Cache para utilizar ficheiros Azure NetApp, [contacte o suporte](hpc-cache-support-ticket.md).
