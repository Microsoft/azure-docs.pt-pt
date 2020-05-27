---
title: Estratégias de acesso a dados
description: A Azure Data Factory suporta agora as gamas de endereços IP estáticos.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 40da43b88e5f95335e8c1766128ac60502630c7b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853786"
---
# <a name="data-access-strategies"></a>Estratégias de acesso a dados

Um objetivo vital de segurança de uma organização é proteger as suas lojas de dados de acesso aleatório através da internet, pode ser uma loja de dados Cloud/SaaS. 

Normalmente, uma loja de dados em nuvem controla o acesso utilizando os mecanismos abaixo:
* Regras de firewall que limitam a conectividade por endereço IP
* Mecanismos de autenticação que exigem que os utilizadores provem a sua identidade
* Mecanismos de autorização que restringem os utilizadores a ações e dados específicos

> [!TIP]
> Com a [introdução da gama de endereços IP estáticos,](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)pode agora permitir a lista de gamas IP para a região de tempo de integração do Azure em particular para garantir que não tem de permitir todos os endereços IP Azure nas suas lojas de dados em nuvem. Desta forma, pode restringir os endereços IP que são autorizados a aceder às lojas de dados.

> [!NOTE] 
> As gamas de endereços IP estão bloqueadas para o tempo de execução da integração do Azure e atualmente são utilizadas apenas para o Movimento de Dados, pipeline e atividades externas. Os fluxos de dados agora não utilizam estas gamas IP. 

Isto deve funcionar em muitos cenários, e entendemos que um endereço IP estático único por tempo de funcionação de integração seria desejável, mas isso não seria possível usando o Azure Integration Runtime atualmente, o que é inservidor. Se necessário, pode sempre configurar um Tempo de Funcionação de Integração Auto-hospedado e utilizar o seu IP estático com ele. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Estratégias de acesso a dados através da Azure Data Factory

* **[Serviço fidedigno](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** - Azure Storage (Blob, ADLS Gen2) suporta configuração de firewall que permite selecionar serviços de plataforma Azure fidedignos para aceder à conta de armazenamento de forma segura. Serviços Fidedignos impõe a autenticação de identidade gerida, o que garante que nenhuma outra fábrica de dados pode ligar-se a este armazenamento a menos que a lista branca o faça usando a sua identidade gerida. Pode encontrar mais detalhes **[neste blog.](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** Portanto, isto é extremamente seguro e recomendado. 
* **IP Estático Único** - Você precisará configurar um tempo de execução de integração auto-hospedado para obter um IP estático para conectores de fábrica de dados. Este mecanismo garante que pode bloquear o acesso a partir de todos os outros endereços IP. 
* **[Gama IP estática](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** - Pode utilizar os endereços IP do Azure Integration Runtime para permitir a sua lista no seu armazenamento (por exemplo, S3, Salesforce, etc.). Certamente restringe endereços IP que podem ligar-se às lojas de dados, mas também depende das regras de Autenticação/Autorização.
* **[Etiqueta de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** - Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure (como a Azure Data Factory). A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. É útil quando o acesso de dados da lista branca no IaaS acolheu lojas de dados na Rede Virtual.
* **Permitir serviços Azure** - Alguns serviços permitem que todos os serviços Azure se conectem a ele caso escolha esta opção. 

Para obter mais informações sobre mecanismos de segurança de rede suportados em lojas de dados em Azure Integration Runtime e Self-hosted Integration Runtime, consulte abaixo de duas tabelas.  
* **Tempo de execução da integração azure**

    | Arquivos de Dados                  | Mecanismo de Segurança de Rede Suportado nas Lojas de Dados         | Serviço fidedigno     | Gama IP estática | Etiquetas de Serviço | Permitir serviços Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|
    | Lojas de dados Azure PaaS       | Azure Cosmos DB                                             | -                   | Sim             | -            | Sim                  |
    |                              | Azure Data Explorer                                         | -                   | Sim*            | Sim*         | -                    |
    |                              | Lago de Dados Azure Gen1                                        | -                   | Sim             | -            | Sim                  |
    |                              | Base de Dados Azure para MariaDB, MySQL, PostgreSQL               | -                   | Sim             | -            | Sim                  |
    |                              | Armazenamento de Ficheiros do Azure                                          | -                   | Yes             | -            | .                    |
    |                              | Armazenamento Azure (Blog, ADLS Gen2)                             | Sim (apenas MSI auth) | Yes             | -            | .                    |
    |                              | Azure SQL DB, SQL DW (Synapse Analytics), SQL Ml          | -                   | Sim             | -            | Sim                  |
    |                              | Cofre chave Azure (para buscar segredos/ cordas de ligação) | Sim                 | Sim             | -            | -                    |
    | Outras lojas de dados PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.            | -                   | Yes             | -            | -                    |
    | LaaS Azure                   | SQL Server, Oracle, etc.                                  | -                   | Sim             | Sim          | -                    |
    | LaaS no local              | SQL Server, Oracle, etc.                                  | -                   | Yes             | -            | -                    |
    
    **Aplicável apenas quando o Azure Data Explorer for injetado na rede virtual, e a gama IP pode ser aplicada no NSG/Firewall.* 

* **Tempo de execução de integração auto-hospedado (em Vnet/no local)**
    
    | Lojas de Dados                  | Mecanismo de Segurança de Rede Suportado nas Lojas de Dados         | IP estático | Serviços Fidedignos  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Lojas de dados Azure PaaS       | Azure Cosmos DB                                               | Yes       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Lago de Dados Azure Gen1                                          | Yes       | -                   |
    |                                | Base de Dados Azure para MariaDB, MySQL, PostgreSQL               | Yes       | -                   |
    |                                | Armazenamento de Ficheiros do Azure                                            | Yes       | -                   |
    |                                | Armazenamento Azure (Blog, ADLS Gen2)                             | Yes       | Sim (apenas MSI auth) |
    |                                | Azure SQL DB, SQL DW (Synapse Analytics), SQL Ml          | Yes       | -                   |
    |                                | Cofre chave Azure (para buscar segredos/ cordas de ligação) | Sim       | Sim                 |
    | Outras lojas de dados PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.              | Yes       | -                   |
    | LaaS Azure                     | SQL Server, Oracle, etc.                                  | Yes       | -                   |
    | LaaS no local              | SQL Server, Oracle, etc.                                  | Sim       | -                   |    

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte os seguintes artigos relacionados:
* [Arquivos de dados suportados](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Cofre chave Azure 'Serviços fidedignos'](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Armazenamento Azure 'Trusted Microsoft Services'](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Identidade gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
