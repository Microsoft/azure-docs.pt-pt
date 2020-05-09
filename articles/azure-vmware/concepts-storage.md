---
title: Conceitos - Armazenamento
description: Conheça as principais capacidades de armazenamento na Azure VMware Solution (AVS) Preview nuvens privadas.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7e58a829a40d590b7936a58ccdc866211a4f5cb4
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740359"
---
# <a name="azure-vmware-solution-avs-preview-storage-concepts"></a>Conceitos de armazenamento de solução Azure VMware (AVS)

As nuvens privadas AVS fornecem armazenamento nativo, em todo o cluster com VMware vSAN. Todo o armazenamento local de cada anfitrião num cluster é usado numa loja de dados vSAN, e a encriptação de dados em repouso está disponível e ativada por padrão. Pode utilizar os recursos de Armazenamento Azure para alargar as capacidades de armazenamento das suas nuvens privadas.

## <a name="vsan-clusters"></a>aglomerados vSAN

O armazenamento local em cada anfitrião do cluster é usado como parte de uma loja de dados vSAN. Todos os grupos de discos utilizam um nível de cache NVMe de 1.6 TB com a capacidade bruta, por hospedeiro, baseada em SSD de 15,4 TB. O tamanho do nível de capacidade bruta de um cluster é a capacidade por hospedeiro vezes o número de hospedeiros. Por exemplo, um cluster de quatro hospedeiros fornecerá capacidade bruta de 61,6 TB no nível de capacidade vSAN.

O armazenamento local em hospedeiros de cluster é utilizado na loja de dados vSAN em todo o cluster. Todas as lojas de dados são criadas como parte de uma implantação privada na nuvem e estão disponíveis para uso imediatamente. O utilizador da cloudadmin e todos os utilizadores do grupo CloudAdmin podem gerir as lojas de dados com estes privilégios vSAN:
- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Encriptação de dados em repouso

as lojas de dados vSAN utilizam encriptação de dados em repouso por padrão. A solução de encriptação é baseada em KMS e suporta operações vCenter para gestão chave. As chaves são armazenadas encriptadas, embrulhadas por uma chave master Azure Key Vault baseada em HSM. Quando um hospedeiro é removido de um cluster por qualquer motivo, os dados sobre SSDs são imediatamente invalidados.

## <a name="scaling"></a>Dimensionamento

A capacidade de armazenamento de clusters nativos é dimensionada adicionando hospedeiros a um cluster. Para os clusters que utilizam os hospedeiros HE, a capacidade bruta de aglomerado é aumentada em 15,4 TB a cada hospedeiro adicional. Os aglomerados que são construídos com anfitriões GP têm a sua capacidade bruta aumentada em 7,7 TB com cada hospedeiro adicional. Em ambos os tipos de agrupamentos, os anfitriões demoram cerca de 10 minutos a ser adicionados a um aglomerado. Consulte o [tutorial de nuvem privada de escala][tutorial-escala-nuvem privada] para obter instruções sobre clusters de escala.

## <a name="azure-storage-integration"></a>Integração de armazenamento azure

Pode utilizar serviços de armazenamento Azure em cargas de trabalho em funcionamento na sua nuvem privada. Os serviços de armazenamento Azure incluem Contas de Armazenamento, Armazenamento de Mesa e Armazenamento blob. A ligação das cargas de trabalho aos serviços de armazenamento azure não atravessa a internet. Esta conectividade proporciona segurança adicional e permite-lhe utilizar serviços de armazenamento Azure baseados em SLA nas suas cargas de trabalho privadas em nuvem.

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender sobre [conceitos privados][concepts-identity]de identidade na nuvem.

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorials-scale-private-cloud]: ./tutorials-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md