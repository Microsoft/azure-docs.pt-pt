---
title: Conceitos - Armazenamento
description: Saiba mais sobre as principais capacidades de armazenamento em nuvens privadas Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a4c34f8767b20de3ca0647e09c5dc9edad3d45fb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200547"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Conceitos de armazenamento de solução Azure VMware

As nuvens privadas Azure VMware Solution fornecem armazenamento nativo, em todo o cluster, com VMware vSAN. Todo o armazenamento local de cada hospedeiro num cluster é usado numa loja de dados vSAN, e a encriptação de dados em repouso está disponível e ativada por padrão. Você pode usar recursos de armazenamento Azure para estender as capacidades de armazenamento das suas nuvens privadas.

## <a name="vsan-clusters"></a>vSAN clusters

O armazenamento local em cada hospedeiro de cluster é usado como parte de uma loja de dados vSAN. Todos os grupos de discos utilizam um nível de cache NVMe de 1.6 TB com a capacidade bruta, por hospedeiro, baseada em SSD de 15,4 TB. O tamanho do nível de capacidade bruta de um cluster é a capacidade por hospedeiro vezes o número de hospedeiros. Por exemplo, um cluster de quatro hospedeiros fornecerá capacidade bruta de 61,6-TB no nível de capacidade vSAN.

O armazenamento local em hospedeiros de cluster é usado na loja de dados vSAN em todo o cluster. Todas as datas são criadas como parte de uma implementação de nuvem privada e estão disponíveis para uso imediato. O utilizador cloudadmin e todos os utilizadores do grupo CloudAdmin podem gerir as datas com estes privilégios vSAN:
- Datastore.AlocarSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Encriptação de dados em repouso

vSAN datastores usam encriptação de dados em repouso por padrão. A solução de encriptação é baseada em KMS e suporta operações vCenter para gestão de chaves. As chaves são armazenadas encriptadas, embrulhadas por uma chave principal do Cofre da Chave Azure. Quando um hospedeiro é removido de um cluster por qualquer motivo, os dados sobre SSDs são invalidados imediatamente.

## <a name="scaling"></a>Dimensionamento

A capacidade de armazenamento de clusters nativo é dimensionada adicionando hospedeiros a um cluster. Para os clusters que utilizam hospedeiros AVS36, a capacidade bruta de cluster é aumentada em 15,4 TB com cada hospedeiro adicionado. Os anfitriões demoram cerca de 10 minutos a ser adicionados a um aglomerado. Para obter instruções sobre aglomerados de escala, consulte o [tutorial de nuvem privada de escala.][tutorial-scale-private-cloud]

## <a name="azure-storage-integration"></a>Integração de armazenamento azul

Você pode usar serviços de armazenamento Azure em cargas de trabalho em execução na sua nuvem privada. Os serviços de armazenamento Azure incluem Contas de Armazenamento, Armazenamento de Mesa e Armazenamento de Blob. A ligação de cargas de trabalho aos serviços de armazenamento Azure não atravessa a internet. Esta conectividade proporciona mais segurança e permite-lhe utilizar serviços de armazenamento Azure baseados em SLA nas suas cargas de trabalho em nuvem privada.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu os conceitos de armazenamento da Azure VMware Solution, talvez queira aprender sobre:

- [Conceitos privados de identidade em nuvem.](concepts-identity.md)
- [v Controlo de acesso baseado em funções para Azure VMware Solution](concepts-role-based-access-control.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
