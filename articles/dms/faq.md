---
title: FAQ sobre como utilizar o serviço de migração de base de dados do Azure | Documentos da Microsoft
description: Saiba as perguntas mais frequentes sobre como utilizar o serviço de migração de base de dados do Azure para efetuar migrações de base de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: f66babf5ee72876692a5ae1b371f811534db6875
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452339"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>FAQ sobre como utilizar o serviço de migração de base de dados do Azure
Este artigo apresenta uma lista de perguntas frequentes sobre como utilizar o serviço de migração de base de dados do Azure em conjunto com respostas relacionadas.

### <a name="q-what-is-azure-database-migration-service"></a>P. O que é o serviço de migração de base de dados do Azure?
O serviço de migração de base de dados do Azure é um serviço completamente gerido criado para ativar migrações totalmente integradas de várias origens de base de dados para plataformas de dados do Azure com o período de indisponibilidade mínimo. O serviço está atualmente em disponibilidade geral, com esforços de desenvolvimento em curso que se concentrou em:
- Confiabilidade e desempenho.
- Adição iterativa de pares origem-destino.
- Contínuo investimento em migrações sem obstáculos.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>P. Os pares de origem-destino o serviço de migração de base de dados do Azure suporta atualmente?
Atualmente, o serviço suporta uma variedade de cenários de migração. Para obter uma listagem completa do Estado de cada cenário de migração disponíveis, consulte o artigo [estado de cenários de migração suportados pelo serviço de migração de base de dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status). Outros cenários de migração estão em pré-visualização limitada e exigem submeter uma nomeação através do site de pré-visualização do DMS. Para obter uma lista completa dos cenários em pré-visualização limitada e para se inscrever para participar de uma dessas ofertas, consulte a [site de pré-visualização do DMS](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>P. Como o serviço de migração de base de dados do Azure se compara a outras ferramentas de migração de base de dados do Microsoft como o Assistente de migração de base de dados (DMA) ou o SQL Server Migration Assistant (SSMA)?
O serviço de migração de base de dados do Azure é o método preferencial para a migração de base de dados para o Microsoft Azure em escala. Para obter mais detalhes sobre como o serviço de migração de base de dados do Azure se compara à outra Microsoft ferramentas de migração de base de dados e para obter recomendações sobre como utilizar o serviço para vários cenários, consulte a postagem de blog [base de dados da diferenciar Microsoft Ferramentas de migração e serviços](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>P. Como o compara o Azure Database Migration Service da oferta do Azure Migrate?
O serviço do Azure Migrate ajuda com a migração de máquinas de virtuais no local para o IaaS do Azure. O serviço avalia a adequabilidade de migração e de dimensionamento com base no desempenho e fornece estimativas de custos para executar máquinas virtuais no local no Azure. O Azure Migrate é útil para migrações lift-and-shift de cargas de trabalho baseadas em VM para VMs IaaS do Azure no local. No entanto, ao contrário do serviço de migração de base de dados do Azure, Azure Migrate não é um serviço de migração de base de dados especializados oferta para plataformas de banco de dados relacional de PaaS do Azure, como a base de dados do Azure SQL ou SQL Azure ou de instância gerida da base de dados SQL do Azure.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>P. Que versões do SQL Server suporta o serviço de migração de base de dados do Azure como uma origem?
Ao migrar do SQL Server, o serviço de migração de base de dados do Azure suporta SQL Server 2005 por meio do SQL Server 2017.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>P. O que é um resumo das etapas necessárias para utilizar o serviço de migração de base de dados do Azure para efetuar uma migração de base de dados?
Durante uma migração de base de dados simples e típico,:
1.  Crie um bases de dados de destino.
2.  Migrar o esquema de bases de dados utilizando o [Assistente de migração de base de dados](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Criar uma instância do Azure Database Migration Service.
4.  Crie um projeto de migração, especificar a bases de dados de origem, a bases de dados de destino e as tabelas a migrar.
5.  Inicie o carregamento completo.
6.  Escolha a validação subsequente.
7.  Efetue uma alternância de manual do seu ambiente de produção para a nova base de dados com base na cloud. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>P. Quais são os pré-requisitos para utilizar o serviço de migração de base de dados do Azure?
Existem vários pré-requisitos necessários para garantir que o serviço de migração de base de dados do Azure é executado de sem problemas ao realizar migrações de base de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares origem-destino) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.
Os pré-requisitos de serviço de migração de base de dados do Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:
- Utilizar o modelo de implementação Azure Resource Manager para criar uma VNET para o Azure Database Migration Service, que proporciona conectividade site a site aos seus servidores de origens no local mediante a utilização do [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Confirmar que as regras de Grupos de Segurança de Rede da Rede Virtual do Azure (VNET) não bloqueia as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Filtrar tráfego de rede com grupos de segurança de rede).
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
 
Para obter uma lista de todos os pré-requisitos necessários para competir cenários de migração específica com o serviço de migração de base de dados do Azure, veja os tutoriais relacionados no serviço de migração de base de dados do Azure [documentação](https://docs.microsoft.com/azure/dms/dms-overview) no Docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>P. Como faço para localizar o endereço IP para o serviço de migração de base de dados do Azure para que eu possa criar uma lista de permissões para as regras de firewall utilizadas para aceder a minha base de dados de origem para migração?
Terá de adicionar regras de firewall que permite que o serviço de migração de base de dados do Azure aceder à sua base de dados de origem para migração. O endereço IP para o serviço é dinâmico, mas se estiver a utilizar o Express Route, este endereço em privado é atribuído pela sua rede empresarial. A forma mais fácil de identificar o endereço IP adequado é procurar no mesmo grupo de recursos como o recurso de serviço de migração de base de dados do Azure aprovisionado para encontrar a Interface de rede associado. Normalmente, o nome do recurso de Interface de rede começa com o prefixo NIC e seguido por um caractere exclusivo e uma seqüência numérica, por exemplo NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar este recurso de interface de rede, pode ver o endereço IP que precisa ser incluído na lista de permissões na descrição geral do recurso página do portal do Azure.

Também poderá incluir a origem de porta do SQL Server escuta na lista de permissões. Por padrão, ele 's a porta 1433, mas a origem que do SQL Server pode ser configurado para escutar também outras portas. Neste caso, precisa incluir essas portas na lista de permissões. É possível determinar a porta que o SQL Server está a escutar utilizando uma consulta de vista de gestão dinâmica:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Também é possível determinar a porta que o SQL Server está escutando consultando o registo de erros do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>P. Existem quaisquer recomendações para otimizar o desempenho do serviço de migração de base de dados do Azure?
Pode fazer algumas coisas para acelerar a sua migração de base de dados com o serviço:
- Quando cria a instância de serviço para permitir que o serviço tirar partido das várias vCPUs para paralelização e transferência de dados mais rápida, utilize a várias CPU geral finalidade escalão de preço.
- Temporariamente aumentar verticalmente a sua instância de destino da SQL Database do Azure para o SKU de escalão Premium durante a operação de migração de dados para minimizar a base de dados do SQL Azure limitação que pode afetar as atividades de transferência de dados ao utilizar os SKUs de nível inferior.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>P. Como configurar uma rede Virtual do Azure?
Enquanto vários tutoriais de Microsoft que podem ajudá-lo pelo processo de configuração de uma VNET do Azure, a documentação oficial é apresentada no artigo [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>P. Por que é o meu serviço de migração de base de dados do Azure parado ou indisponível?
Se o utilizador explicitamente para o Azure Database Migration Service (DMS) ou se o serviço estiver inativo durante um período de 24 horas, o serviço será numa parada ou automaticamente o estado em pausa. Em cada caso, o serviço estará disponível e num estado parado.  Para retomar as migrações de Active Directory, reinicie o serviço.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>P. Em que pode deixar os comentários sobre o serviço de migração de base de dados do Azure?
Queremos a sua opinião. Envie quaisquer comentários e / ideias ter sobre o serviço de migração de base de dados do Azure através de voz do utilizador, [aqui](https://feedback.azure.com/forums/906100-azure-database-migration-service), ou contacte a equipa através de [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="next-steps"></a>Passos Seguintes
Para uma descrição geral do serviço de migração de base de dados do Azure e a disponibilidade regional, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md). 
