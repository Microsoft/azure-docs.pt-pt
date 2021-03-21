---
title: Endereços IP do Azure Integration Runtime
description: Saiba quais os endereços IP a partir dos quais deve permitir o tráfego de entrada, de modo a configurar adequadamente firewalls para garantir o acesso à rede nas lojas de dados.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371401"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços IP do Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O IP endereça que o Tempo de Execução da Integração Azure depende da região onde o seu tempo de integração Azure está localizado. *Todos os* Os tempos de integração do Azure que estão na mesma região utilizam os mesmos intervalos de endereços IP.

> [!IMPORTANT]  
> Fluxos de dados e Tempo de execução de integração Azure que permitem rede virtual gerida não suportam a utilização de gamas IP fixas.
>
> Pode utilizar estas gamas IP para execuções de movimento de dados, pipeline e atividades externas. Estas gamas IP podem ser utilizadas para filtragem em lojas de dados/ Grupo de Segurança de Rede (NSG)/ Firewalls para acesso à entrada a partir do tempo de funcionamento da Integração Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Endereços IP de integração do Azure: Regiões específicas

Permitir o tráfego dos endereços IP listados para o tempo de funcionamento da Integração Azure na região específica de Azure onde os seus recursos estão localizados. Pode obter uma lista de etiquetas de serviço de intervalos de IP na [ligação de transferência de intervalos de IP das etiquetas de serviço](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Por exemplo, se a região de Azure for **AustraliaEast,** pode obter uma lista de gama de IP a partir de **DataFactory.AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problema conhecido com Azure Storage

* Ao ligar-se à conta de armazenamento Azure, as regras da rede IP não têm qualquer efeito nos pedidos originários do tempo de funcionamento da integração Azure na mesma região que a conta de armazenamento. Para mais detalhes, [consulte este artigo.](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range) 

  Em vez disso, sugerimos a utilização [de serviços fidedignos durante a ligação ao Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Passos seguintes

* [Considerações de segurança para o movimento de dados na Azure Data Factory](data-movement-security-considerations.md)