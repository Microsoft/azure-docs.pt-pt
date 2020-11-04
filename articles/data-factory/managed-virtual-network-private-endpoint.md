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
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: a34f2e50a5eb4ab87a02a5128cb912fa220849f1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317068"
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

A Azure Data Factory suporta ligações privadas. O link privado permite-lhe aceder aos serviços Azure (PaaS) (como Azure Storage, Azure Cosmos DB, Azure Synapse Analytics (anteriormente SQL Data Warehouse)).

Quando utiliza uma ligação privada, o tráfego entre as suas lojas de dados e a Rede Virtual gerida atravessa inteiramente a rede de espinha dorsal da Microsoft. A Private Link protege contra riscos de exfiltração de dados. Estabelece-se uma ligação privada a um recurso criando um ponto final privado.

O ponto final privado utiliza um endereço IP privado na Rede Virtual gerida para efetivamente trazer o serviço para o mesmo. Os pontos finais privados são mapeados para um recurso específico em Azure e não em todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. Saiba mais sobre [links privados e pontos finais privados.](../private-link/index.yml)

> [!NOTE]
> Recomenda-se que crie pontos finais privados geridos para se conectar a todas as suas fontes de dados Azure. 
 
> [!WARNING]
> Se uma loja de dados PaaS (Blob, ADLS Gen2, Azure Synapse Analytics) tiver um ponto final privado já criado contra ele, e mesmo que permita o acesso de todas as redes, a ADF só poderá aceder-lhe utilizando o ponto final privado gerido. Certifique-se de criar um ponto final privado nesses cenários. 

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
- Azure Synapse Analytics (anteriormente SQL Data Warehouse)
- Azure CosmosDB SQL
- Azure Key Vault
- Serviço de Ligação Privada Azure
- Azure Search
- Base de Dados do Azure para MySQL
- Base de Dados do Azure para PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>A azure Data Factory Rede Virtual Gerida está disponível nas seguintes regiões do Azure:
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A. Oeste 2
- E.U.A. Centro-Sul
- E.U.A. Central
- Europa do Norte
- Europa Ocidental
- Sul do Reino Unido
- Sudeste Asiático
- Leste da Austrália
- Austrália Sudeste

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicações de saída através de ponto final público da ADF Managed Virtual Network
- Apenas a porta 443 está aberta para comunicações de saída.
- O Azure Storage e o Azure Data Lake Gen2 não são suportados para serem conectados através do ponto final público da ADF Managed Virtual Network.

### <a name="linked-service-creation-of-azure-key-vault"></a>Criação de serviço ligado do Cofre chave Azure 
- Quando cria um Serviço Linked para Azure Key Vault, não existe referência de runtime de integração Azure. Então não pode criar Private Endpoint durante a criação do Azure Key Vault. Mas quando cria o Linked Service para lojas de dados que faz referência ao Azure Key Vault Linked Service e este Serviço Linked refere-se a Azure Integration Runtime com Rede Virtual Gerida ativada, então é capaz de criar um ponto final privado para o Serviço Ligado ao Cofre de Chaves Azure durante a criação. 
- A operação de **ligação** de teste para o Serviço Ligado do Cofre de Chave Azure apenas valida o formato URL, mas não faz qualquer operação de rede.

## <a name="next-steps"></a>Passos seguintes

- Tutorial: [Construir um pipeline de cópias utilizando rede virtual gerida e pontos finais privados](tutorial-copy-data-portal-private.md) 
- Tutorial: [Construa o pipeline de fluxo de dados de mapeamento utilizando rede virtual gerida e pontos finais privados](tutorial-data-flow-private.md)