---
title: FAQ sobre como utilizar o serviço de migração de base de dados do Azure | Documentos da Microsoft
description: Saiba as perguntas mais frequentes sobre como utilizar o serviço de migração de base de dados do Azure para efetuar migrações de base de dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717995"
---
# <a name="faq-about-using-azure-database-migration-service"></a>FAQ sobre a utilização do serviço de migração de base de dados do Azure

Este artigo apresenta uma lista de perguntas frequentes sobre como utilizar o Azure Database Migration Service em conjunto com respostas relacionadas.

## <a name="overview"></a>Descrição geral

**P. O que é o serviço de migração de base de dados do Azure?**
Serviço de migração de base de dados do Azure é um serviço completamente gerido criado para ativar migrações totalmente integradas de várias origens de base de dados para plataformas de dados do Azure com o período de indisponibilidade mínimo. O serviço está atualmente em disponibilidade geral, com esforços de desenvolvimento em curso que se concentrou em:

* Confiabilidade e desempenho.
* Adição iterativa de pares origem-destino.
* Contínuo investimento em migrações sem obstáculos.

**P. Os pares de origem/destino do Azure Database Migration Service suporta atualmente?**
Atualmente, o serviço suporta uma variedade de pares origem/destino ou a cenários de migração. Para obter uma listagem completa do Estado de cada cenário de migração disponíveis, consulte o artigo [estado de cenários de migração suportados pelo serviço de migração de base de dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Outros cenários de migração estão em pré-visualização e requerem a submeter uma nomeação através do site de pré-visualização do DMS. Para obter uma lista completa dos cenários em pré-visualização e para se inscrever para participar de uma dessas ofertas, consulte a [site de pré-visualização do DMS](https://aka.ms/dms-preview/).

**P. Que versões do SQL Server suporta Azure Database Migration Service como uma origem?**
Ao migrar do SQL Server, fontes suportadas para o serviço de migração de base de dados do Azure são o SQL Server 2005 por meio do SQL Server 2017.

**P: Ao utilizar o Azure Database Migration Service, o que é a diferença entre um offline e uma migração online?**
Pode utilizar o serviço de migração de base de dados do Azure para executar migrações offline e online. Com uma *offline* migração, o período de indisponibilidade de aplicação é iniciada quando inicia a migração. Com uma *online* migração, o período de indisponibilidade é limitado ao tempo para transitar no final da migração. Sugerimos que teste uma migração offline para determinar se o período de inatividade é aceitável; se não for, faça uma migração online.

> [!NOTE]
> Utilizar o Azure Database Migration Service para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium. Para obter mais informações, veja o Azure Database Migration Service [preços](https://azure.microsoft.com/pricing/details/database-migration/) página.

**P. Como o Azure Database Migration Service compara a outras ferramentas de migração de base de dados do Microsoft como o Assistente de migração de base de dados (DMA) ou o SQL Server Migration Assistant (SSMA)?**
Serviço de migração de base de dados do Azure é o método preferencial para a migração de base de dados para o Microsoft Azure em escala. Para obter mais detalhes sobre como o serviço de migração de base de dados do Azure se compara à outro Microsoft ferramentas de migração de base de dados e para obter recomendações sobre como utilizar o serviço para vários cenários, consulte a postagem de blog [migração de base de dados de diferenciar da Microsoft Ferramentas e serviços](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**P. Como o Azure Database Migration Service compara da oferta do Azure Migrate?**
O Azure Migrate ajuda com a migração de máquinas de virtuais no local para o IaaS do Azure. O serviço avalia a adequabilidade de migração e de dimensionamento com base no desempenho e fornece estimativas de custos para executar máquinas virtuais no local no Azure. O Azure Migrate é útil para migrações lift-and-shift de cargas de trabalho baseadas em VM para VMs IaaS do Azure no local. No entanto, ao contrário do serviço de migração de base de dados do Azure, Azure Migrate não é um serviço de migração de base de dados especializados oferta para plataformas de banco de dados relacional de PaaS do Azure, como a base de dados do Azure SQL ou a instância gerida da base de dados SQL do Azure.

## <a name="setup"></a>Configurar

**P. Quais são os pré-requisitos de utilização do serviço de migração de base de dados do Azure?**
Existem vários pré-requisitos necessários para garantir que o Azure Database Migration Service execute sem problemas ao realizar migrações de base de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares origem-destino) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos de serviço de migração de base de dados do Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Criar uma VNet para o Azure Database Migration Service com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que a rede Virtual do Azure (VNet) regras do grupo de segurança de rede não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do Azure VNet NSG, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Quando utilizar uma aplicação de firewall na frente de suas bases de dados de origem, terá de adicionar regras de firewall para permitir que o serviço de migração de base de dados do Azure acessar os bancos de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir cenários de migração específica com o serviço de migração de base de dados do Azure, veja os tutoriais relacionados no serviço de migração de base de dados do Azure [documentação](https://docs.microsoft.com/azure/dms/dms-overview) no docs.microsoft.com.

**P. Como faço para localizar o endereço IP para o serviço de migração de base de dados do Azure para que eu possa criar uma lista de permissões para as regras de firewall utilizadas para aceder a minha base de dados de origem para migração?**
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

**P. Como configurar uma rede Virtual do Azure?**
Enquanto vários tutoriais de Microsoft que podem ajudá-lo pelo processo de configuração de uma VNET do Azure, a documentação oficial é apresentada no artigo [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Utilização

**P. O que é um resumo das etapas necessárias para utilizar o serviço de migração de base de dados do Azure para efetuar uma migração de base de dados?**
Durante uma migração de base de dados simples e típico,:

1. Crie um bases de dados de destino.
2. Avalie suas bases de dados de origem.
    * Para migrações homogéneo, avaliar suas bases de dados existente, utilizando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogéneos (a partir de origens de compete), avalie as bases de dados existente com [SSMA](https://aka.ms/get-ssma). Também é usar o SSMA para converter os objetos de base de dados e migrar o esquema para sua plataforma de destino.
3. Crie uma instância do serviço de migração de base de dados do Azure.
4. Crie um projeto de migração, especificar as bases de dados de origem, a bases de dados de destino e as tabelas a migrar.
5. Inicie o carregamento completo.
6. Escolha a validação subsequente.
7. Efetue uma alternância de manual do seu ambiente de produção para a nova base de dados com base na cloud.

## <a name="troubleshooting-and-optimization"></a>Resolução de problemas e Otimização

**P. Estou definindo um projeto de migração no DMS e estou a ter dificuldade em a ligar ao meu banco de dados de origem. O que devo fazer?**
Se tiver problemas em ligar ao seu sistema de base de dados de origem ao trabalhar na migração, crie uma máquina virtual na VNet com o qual configura a sua instância DMS. Na máquina virtual, deve ser capaz de executar um teste de ligar, tal como através de um ficheiro UDL para testar uma ligação para o SQL Server ou baixar Robo 3T para testar ligações MongoDB. Se o teste de ligação for bem-sucedida, não deve ter um problema com a ligação à sua base de dados de origem. Se o teste de ligação não for bem sucedido, contacte o administrador de rede.

**P. Por que é o meu serviço de migração de base de dados do Azure parado ou indisponível?**
Se o utilizador explicitamente para o Azure Database Migration Service (DMS) ou se o serviço estiver inativo durante um período de 24 horas, o serviço irá estar numa parada ou automaticamente o estado em pausa. Em cada caso, o serviço estará disponível e num estado parado.  Para retomar as migrações de Active Directory, reinicie o serviço.

**P. Existem quaisquer recomendações para otimizar o desempenho do serviço de migração de base de dados do Azure?**
Pode fazer algumas coisas para acelerar a sua migração de base de dados com o serviço:

* Quando cria a instância de serviço para permitir que o serviço tirar partido das várias vCPUs para paralelização e transferência de dados mais rápida, utilize a várias CPU geral finalidade escalão de preço.
* Temporariamente aumentar verticalmente a sua instância de destino da SQL Database do Azure para o SKU de escalão Premium durante a operação de migração de dados para minimizar a base de dados do SQL Azure limitação que pode afetar as atividades de transferência de dados ao utilizar os SKUs de nível inferior.

## <a name="next-steps"></a>Passos seguintes

Para uma descrição geral do serviço de migração de base de dados do Azure e a disponibilidade regional, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md).
