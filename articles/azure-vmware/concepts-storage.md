---
title: Conceitos - Armazenamento
description: Saiba mais sobre as principais capacidades de armazenamento em nuvens privadas Azure VMware Solution.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 02378040061080d3c9abbfafb26180c9d22e9073
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316822"
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

vSAN datastores usam encriptação de dados em repouso por padrão. A solução de encriptação é baseada em KMS e suporta operações vCenter para gestão de chaves. As chaves são armazenadas encriptadas, embrulhadas por uma chave master Azure Key Vault baseada em HSM. Quando um hospedeiro é removido de um cluster por qualquer motivo, os dados sobre SSDs são invalidados imediatamente.

## <a name="scaling"></a>Dimensionamento

A capacidade de armazenamento de clusters nativo é dimensionada adicionando hospedeiros a um cluster. Para os agrupamentos que utilizam hospedeiros HE, a capacidade bruta de cluster é aumentada em 15,4 TB com cada hospedeiro adicional. Os clusters que são construídos com anfitriões GP têm a sua capacidade bruta aumentada em 7,7 TB com cada hospedeiro adicional. Em ambos os tipos de aglomerados, os anfitriões demoram cerca de 10 minutos a serem adicionados a um cluster. Consulte o [tutorial de nuvem privada][tutorial-scale-private-cloud] de escala para obter instruções sobre aglomerados de escala.

## <a name="azure-storage-integration"></a>Integração de armazenamento azul

Você pode usar serviços de armazenamento Azure em cargas de trabalho em execução na sua nuvem privada. Os serviços de armazenamento Azure incluem Contas de Armazenamento, Armazenamento de Mesa e Armazenamento de Blob. A ligação de cargas de trabalho aos serviços de armazenamento Azure não atravessa a internet. Esta conectividade proporciona segurança adicional e permite-lhe utilizar serviços de armazenamento Azure baseados em SLA nas suas cargas de trabalho em nuvem privada.

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender sobre [conceitos privados de identidade em nuvem.][concepts-identity]

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
