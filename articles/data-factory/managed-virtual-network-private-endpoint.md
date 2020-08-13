---
title: Rede virtual gerida & geridos pontos finais privados
description: Conheça a rede virtual gerida e gere os pontos finais privados geridos na Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135968"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory Rede Virtual Gerida (pré-visualização)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explicará a Rede Virtual Gerida e os pontos finais privados geridos na Azure Data Factory.


## <a name="managed-virtual-network"></a>Rede virtual gerida

Quando criar um Tempo de Execução de Integração Azure (IR) dentro da Rede Virtual Gerida da Azure Data Factory (VNET), o tempo de integração será a provisionado com a Rede Virtual gerida e irá aproveitar os pontos finais privados para se conectar de forma segura às lojas de dados suportadas. 

A criação de um Azure IR dentro da Rede Virtual gerida garante que o processo de integração de dados é isolado e seguro. 

Benefícios da utilização da Rede Virtual Gerida:

- Com uma Rede Virtual Gerida, pode descarregar o fardo de gerir a Rede Virtual para a Azure Data Factory. Não precisa de criar uma sub-rede para o Tempo de Execução da Integração Azure que poderia eventualmente utilizar muitos IPs privados da sua Rede Virtual e exigiria um planeamento prévio de infraestruturas de rede. 
- Não requer conhecimentos profundos de networking da Azure para fazer integrações de dados de forma segura. Em vez disso, começar com ETL seguro é muito simplificado para os engenheiros de dados. 
- Rede Virtual gerida juntamente com pontos finais privados geridos protege contra exfiltração de dados. 

> [!IMPORTANT]
>Atualmente, o VNet gerido só é suportado na mesma região que a região da Azure Data Factory.
 

![Arquitetura de rede virtual gerida pela ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Pontos finais privados geridos

Os pontos finais privados geridos são pontos finais privados criados na Rede Virtual Gerida pela Azure Data Factory, estabelecendo uma ligação privada com os recursos da Azure. A Azure Data Factory gere estes pontos finais privados em seu nome. 

![Novo ponto final privado gerido](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

A Azure Data Factory suporta ligações privadas. O link privado permite-lhe aceder aos serviços Azure (PaaS) (como Azure Storage, Azure Cosmos DB, Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse)).

Quando utiliza uma ligação privada, o tráfego entre as suas lojas de dados e a Rede Virtual gerida atravessa inteiramente a rede de espinha dorsal da Microsoft. A Private Link protege contra riscos de exfiltração de dados. Estabelece-se uma ligação privada a um recurso criando um ponto final privado.

O ponto final privado utiliza um endereço IP privado na Rede Virtual gerida para efetivamente trazer o serviço para o mesmo. Os pontos finais privados são mapeados para um recurso específico em Azure e não em todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. Saiba mais sobre [links privados e pontos finais privados.](https://docs.microsoft.com/azure/private-link/)

> [!NOTE]
> Recomenda-se que crie pontos finais privados geridos para se conectar a todas as suas fontes de dados Azure. 
 
> [!WARNING]
> Se uma loja de dados PaaS (Blob, ADLS Gen2, SQL DW) tiver um ponto final privado já criado contra ele, e mesmo que permita o acesso de todas as redes, a ADF só poderá aceder-lhe utilizando o ponto final privado gerido. Certifique-se de criar um ponto final privado nesses cenários. 

Uma ligação de ponto final privado é criada num estado "pendente" quando cria um ponto final privado gerido na Azure Data Factory. É iniciado um fluxo de trabalho de aprovação. O proprietário do recurso de ligação privada é responsável por aprovar ou rejeitar a ligação.

![Gerir o ponto final privado](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Se o proprietário aprovar a ligação, o link privado é estabelecido. Caso contrário, o elo privado não será estabelecido. Em qualquer dos casos, o ponto final privado gerido será atualizado com o estado da ligação.

![Aprovar ponto final privado gerido](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Apenas um ponto final privado gerido num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada.

## <a name="limitations-and-known-issues"></a>Problemas e limitações conhecidos
### <a name="supported-data-sources"></a>Origens de Dados Suportadas
Abaixo as fontes de dados são suportadas para se conectar através de ligação privada a partir da Rede Virtual Gerida ADF.
- Armazenamento de Blobs do Azure
- Armazenamento de Tabelas do Azure
- Ficheiros do Azure
- Azure Data Lake Gen2
- Base de Dados Azure SQL (sem incluir a instância gerida do Azure SQL)
- Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse)
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicações de saída através de ponto final público da ADF Managed Virtual Network
- Apenas a porta 443 está aberta para comunicações de saída.
- O Azure Storage e o Azure Data Lake Gen2 não são suportados para serem conectados através do ponto final público da ADF Managed Virtual Network.

### <a name="other-known-issues"></a>Outras questões conhecidas
A depurada para a conectividade CosmosDB não funciona, incluindo o depurg dataFlow e o pipeline depurg.


## <a name="next-steps"></a>Próximos passos

- Tutorial: [Construir um pipeline de cópias utilizando rede virtual gerida e pontos finais privados](tutorial-copy-data-portal-private.md) 
- Tutorial: [Construa o pipeline de fluxo de dados de mapeamento utilizando rede virtual gerida e pontos finais privados](tutorial-data-flow-private.md)
