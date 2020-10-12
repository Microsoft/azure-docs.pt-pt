---
title: Estratégias de acesso a dados
description: A Azure Data Factory suporta agora gamas de endereços IP estáticos.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: a4d8d7eaed40b876adecb82f339be4a4c434325f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616861"
---
# <a name="data-access-strategies"></a>Estratégias de acesso a dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um objetivo vital de segurança de uma organização é proteger as suas lojas de dados do acesso aleatório através da internet, podendo ser uma loja de dados Cloud/SaaS. 

Normalmente, uma loja de dados em nuvem controla o acesso utilizando os mecanismos abaixo:
* Ligação privada de uma rede virtual para fontes de dados ativadas
* Regras de firewall que limitam a conectividade por endereço IP
* Mecanismos de autenticação que exigem que os utilizadores provem a sua identidade
* Mecanismos de autorização que restringem os utilizadores a ações e dados específicos

> [!TIP]
> Com a [introdução da gama de endereços IP estático,](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)pode agora permitir intervalos IP de lista para a região de execução de integração Azure para garantir que não tem de permitir todos os endereços IP Azure nas suas lojas de dados em nuvem. Desta forma, pode restringir os endereços IP que são autorizados a aceder às lojas de dados.

> [!NOTE] 
> Os intervalos de endereços IP estão bloqueados para o tempo de execução da integração do Azure e atualmente é utilizado apenas para movimento de dados, pipeline e atividades externas. Fluxos de dados e tempo de execução de integração Azure que permitem rede virtual gerida agora não utilizam estas gamas IP. 

Isto deve funcionar em muitos cenários, e nós entendemos que um endereço IP estático único por tempo de execução de integração seria desejável, mas isso não seria possível usando atualmente o Tempo de Execução da Integração Azure, que é sem servidor. Se necessário, pode sempre configurar um Tempo de Integração Auto-hospedado e utilizar o seu IP estático com ele. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Estratégias de acesso a dados através da Azure Data Factory

* **[Link Privado](https://docs.microsoft.com/azure/private-link/private-link-overview)** - Pode criar um tempo de execução de integração Azure dentro da Rede Virtual Gerida pela Azure Data Factory e irá aproveitar pontos finais privados para se conectar de forma segura a lojas de dados suportadas. O tráfego entre a Rede Virtual Gerida e as fontes de dados percorre a rede de espinha dorsal da Microsoft e não é exposição à rede pública.
* **[Serviço Fidedigno](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** - O Azure Storage (Blob, ADLS Gen2) suporta a configuração de firewall que permite selecionar serviços de plataforma fidedignas para aceder à conta de armazenamento de forma segura. Os Serviços Fidedignos impõem a autenticação de Identidade Gerida, que garante que nenhuma outra fábrica de dados pode ligar-se a este armazenamento, a menos que seja aprovada para o fazer utilizando a sua identidade gerida. Pode encontrar mais detalhes **[neste blog.](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** Por isso, isto é extremamente seguro e recomendado. 
* **IP Estático Único** - Você precisará configurar um tempo de integração auto-hospedado para obter um IP estático para conectores data factory. Este mecanismo garante que pode bloquear o acesso de todos os outros endereços IP. 
* **[Gama IP estática](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** - Pode utilizar os endereços IP do Azure Integration Runtime para permitir a sua lista no seu armazenamento (por exemplo, S3, Salesforce, etc.). Certamente restringe endereços IP que podem ligar-se às lojas de dados, mas também se baseia em regras de Autenticação/Autorização.
* **[Tag de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** - Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure (como Azure Data Factory). A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. É útil para filtrar o acesso de dados em lojas de dados hospedadas no IaaS na Rede Virtual.
* **Permitir serviços Azure** - Alguns serviços permitem que todos os serviços da Azure se conectem a ele caso escolha esta opção. 

Para obter mais informações sobre mecanismos de segurança de rede suportados nas lojas de dados em Azure Integration Runtime e Self-hosted Integration Runtime, consulte abaixo de duas tabelas.  
* **Tempo de execução da integração Azure**

    | Arquivos de Dados                  | Mecanismo de Segurança da Rede Suportado nas Lojas de Dados | Ligação Privada     | Serviço de Confiança     | Gama IP estática | Etiquetas de Serviço | Permitir serviços Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Lojas de dados Azure PaaS       | Azure Cosmos DB                                     | Sim              | -                   | Sim             | -            | Sim                  |
    |                              | Azure Data Explorer                                 | -                | -                   | Sim*            | Sim*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Sim             | -            | Sim                  |
    |                              | Base de Dados Azure para MariaDB, MySQL, PostgreSQL       | -                | -                   | Sim             | -            | Sim                  |
    |                              | Armazenamento de Ficheiros do Azure                                  | Sim              | -                   | Sim             | -            | .                    |
    |                              | Armazenamento Azure (Blob, ADLS Gen2)                     | Sim              | Sim (apenas MSI auth) | Sim             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL Ml  | Sim (apenas Azure SQL DB/DW)        | -                   | Sim             | -            | Sim                  |
    |                              | Cofre de chave Azure (para obter segredos/ cadeia de conexão) | sim      | Sim                 | Sim             | -            | -                    |
    | Outras lojas de dados PaaS/ SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.    | -                | -                   | Sim             | -            | -                    |
    | Azure laaS                   | SQL Server, Oráculo, etc.                          | -                | -                   | Sim             | Sim          | -                    |
    | LaaS no local              | SQL Server, Oráculo, etc.                          | -                | -                   | Sim             | -            | -                    |
    
    **Aplicável apenas quando o Azure Data Explorer é injetado em rede virtual e o intervalo IP pode ser aplicado em NSG/Firewall.* 

* **Tempo de execução de integração auto-hospedado (em Vnet/no local)**
    
    | Lojas de Dados                  | Mecanismo de Segurança da Rede Suportado nas Lojas de Dados         | IP estático | Serviços fidedignos  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Lojas de dados Azure PaaS       | Azure Cosmos DB                                               | Sim       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Sim       | -                   |
    |                                | Base de Dados Azure para MariaDB, MySQL, PostgreSQL               | Sim       | -                   |
    |                                | Armazenamento de Ficheiros do Azure                                            | Sim       | -                   |
    |                                | Azure Storage (Blog, ADLS Gen2)                             | Sim       | Sim (apenas MSI auth) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL Ml          | Sim       | -                   |
    |                                | Cofre de chave Azure (para obter segredos/ cadeia de conexão) | Sim       | Sim                 |
    | Outras lojas de dados PaaS/ SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.              | Sim       | -                   |
    | Azure laaS                     | SQL Server, Oráculo, etc.                                  | Sim       | -                   |
    | LaaS no local              | SQL Server, Oráculo, etc.                                  | Sim       | -                   |    

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes artigos relacionados:
* [Arquivos de dados suportados](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault 'Serviços fidedignos'](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Azure Storage 'Trusted Microsoft Services'](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Identidade gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
