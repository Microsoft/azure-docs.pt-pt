---
title: Utilize ficheiros Azure HPC Cache e Azure NetApp
description: Como utilizar o Cache Azure HPC para melhorar o acesso aos dados armazenados com ficheiros Azure NetApp
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238685"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Utilize cache Azure HPC com ficheiros Azure NetApp

Pode utilizar [o Azure NetApp Files](https://azure.microsoft.com/services/netapp/) como alvo de armazenamento para o seu Cache Azure HPC. Este artigo explica como os dois serviços podem trabalhar em conjunto, e dá dicas para montá-los.

O Azure NetApp Files combina o seu sistema operativo ONTAP com a escalabilidade e velocidade do Microsoft Azure. Esta combinação permite que os utilizadores transtem fluxos de trabalho estabelecidos para a nuvem sem reescrever código.

A adição de um componente De Cache Azure HPC pode melhorar o acesso aos ficheiros apresentando vários volumes de Ficheiros Azure NetApp num espaço de nome agregado. Pode fornecer cerca de borda para volumes localizados em uma região de serviço diferente. Também pode melhorar o desempenho da procura de volumes criados a níveis de serviço de nível inferior para economizar custos.

## <a name="overview"></a>Descrição geral

Para utilizar um sistema de Ficheiros Azure NetApp como armazenamento de back-end com cache Azure HPC, siga este processo.

1. Crie o sistema e volumes azure NetApp de acordo com a orientação no [Plan o seu sistema, abaixo](#plan-your-azure-netapp-files-system).
1. Crie o Cache Azure HPC na região onde necessita de acesso a ficheiros. (Utilize as instruções em [Criar uma cache Azure HPC](hpc-cache-create.md).)
1. [Defina os alvos](#create-storage-targets-in-the-cache) de armazenamento na cache que apontam para os volumes de Ficheiros Azure NetApp. Crie um alvo de armazenamento de cache para cada endereço IP único utilizado para aceder aos volumes.
1. Os clientes [montam o Cache Azure HPC](#mount-storage-targets) em vez de montarem os volumes de Ficheiros Azure NetApp diretamente.

## <a name="plan-your-azure-netapp-files-system"></a>Planeie o seu sistema de Ficheiros Azure NetApp

Ao planear o seu sistema Deficheiros Azure NetApp, preste atenção aos itens desta secção para se certificar de que pode integrá-lo sem problemas com o Azure HPC Cache.

Leia também a documentação do [Azure NetApp Files](../azure-netapp-files/index.yml) antes de criar volumes para utilização com a Cache Azure HPC.

### <a name="nfs-client-access-only"></a>Acesso apenas ao cliente NFS

A Tualmente, a Azure HPC Cache suporta apenas o acesso ao NFS. Não pode ser utilizado com volumes de bits de bit SMB ACL ou POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Subnet exclusivo para Ficheiros Azure NetApp

O Azure NetApp Files utiliza uma única subnet delegada para os seus volumes. Nenhum outro recurso pode usar esta sub-rede. Além disso, apenas uma sub-rede numa rede virtual pode ser usada para ficheiros Azure NetApp. Saiba mais em Diretrizes para o planeamento da [rede De ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Tamanho da subnet delegada

Utilize o tamanho mínimo para a subnet delegada ao criar um sistema de Ficheiros Azure NetApp para utilização com cache Azure HPC.

O tamanho mínimo, especificado com a máscara de rede /28, fornece 16 endereços IP. Na prática, o Azure NetApp Files utiliza apenas três dos endereços IP disponíveis para acesso em volume. Isto significa que só precisa de criar três alvos de armazenamento no seu Cache Azure HPC para cobrir todos os volumes.

Se a subnet delegada for demasiado grande, é possível que os volumes de Ficheiros Azure NetApp utilizem mais endereços IP do que uma única instância de Cache Azure HPC pode manusear. Uma única cache pode ter no máximo dez alvos de armazenamento.

O exemplo de quickstart na documentação do Azure NetApp Files utiliza 10.7.0.0.0/16 para a subnet delegada, que dá uma sub-rede demasiado grande.

### <a name="capacity-pool-service-level"></a>Nível de serviço de piscina de capacidade

Ao escolher o nível de serviço para a sua capacidade de piscina, considere o seu fluxo de trabalho. Se escrever frequentemente dados para o volume de Ficheiros Azure NetApp, o desempenho da cache pode ser restringido se o tempo de redação for lento. Escolha um alto nível de serviço para volumes que terão escritos frequentes.

Volumes com baixos níveis de serviço também podem mostrar algum atraso no início de uma tarefa enquanto o cache pré-preenche o conteúdo. Depois de a cache estar a funcionar com um bom conjunto de ficheiros de trabalho, o atraso deve tornar-se impercetível.

É importante planear o nível de serviço de piscina de capacidade antes do tempo, porque não pode ser alterado após a criação. Um novo volume teria de ser criado num conjunto de capacidades diferente, e os dados copiados.

Note que pode alterar a quota de armazenamento de um volume e o tamanho da piscina de capacidade sem perturbar o acesso.

## <a name="create-storage-targets-in-the-cache"></a>Criar alvos de armazenamento na cache

Depois de configurar o sistema Deficheiros Azure NetApp e de criar o Cache Azure HPC, defina os alvos de armazenamento na cache que apontam para os volumes do sistema de ficheiros.

Crie um alvo de armazenamento para cada endereço IP utilizado pelos volumes dos ficheiros Azure NetApp. O endereço IP está listado na página de instruções de montagem do volume.

Se vários volumes partilharem o mesmo endereço IP, pode utilizar um alvo de armazenamento para todos eles.  

Siga as instruções de [montagem na documentação do Azure NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para encontrar os endereços IP a utilizar.

Também pode encontrar endereços IP com o Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Os nomes de exportação no sistema Deficheiros Azure NetApp têm um componente único. Não tente criar um alvo de ``/`` armazenamento para a exportação de raiz em Ficheiros Azure NetApp, porque essa exportação não fornece acesso a ficheiros.

Não existem restrições especiais em caminhos espaciais de nome virtual para estes alvos de armazenamento.

## <a name="mount-storage-targets"></a>Monte alvos de armazenamento

As máquinas de clientes devem montar a cache em vez de montar em volumes de Ficheiros Azure NetApp diretamente. Siga as instruções no [Monte da Cache Azure HPC](hpc-cache-mount.md).

## <a name="next-steps"></a>Passos seguintes

* Ler mais sobre a criação e utilização de [ficheiros Azure NetApp](../azure-netapp-files/index.yml)
* Para ajudar a planear e configurar o seu sistema de cache Azure HPC para utilizar ficheiros Azure NetApp, suporte de [contato](hpc-cache-support-ticket.md).
