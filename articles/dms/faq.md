---
title: FAQ - Serviço de Migração de Bases de Dados Azure
description: Perguntas frequentes sobre a utilização do Serviço de Migração de Bases de Dados Azure para realizar migrações de bases de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650986"
---
# <a name="faq-about-using-azure-database-migration-service"></a>FAQ sobre a utilização do Serviço de Migração de Bases de Dados Azure

Este artigo lista comumente perguntas sobre a utilização do Serviço de Migração de Bases de Dados Azure juntamente com respostas relacionadas.

## <a name="overview"></a>Descrição geral

**Q. O que é o Serviço de Migração de Bases de Dados Azure?**
O Serviço de Migração de Bases de Dados Azure é um serviço totalmente gerido projetado para permitir migrações perfeitas de várias fontes de base de dados para plataformas Azure Data com o mínimo de tempo de inatividade. O serviço encontra-se atualmente em Disponibilidade Geral, com esforços de desenvolvimento em curso focados em:

* Fiabilidade e desempenho.
* Adição iterativa de pares alvo de origem.
* Continuação do investimento em migrações sem atritos.

**Q. Que par de fonte/alvo suporta atualmente o Serviço de Migração de Bases de Dados Azure?**
O serviço suporta atualmente uma variedade de pares de origem/alvo, ou cenários de migração. Para obter uma lista completa do estado de cada cenário de migração disponível, consulte o artigo [Estado dos cenários de migração apoiados pelo Serviço](https://docs.microsoft.com/azure/dms/resource-scenario-status)de Migração da Base de Dados Azure.

Outros cenários de migração estão em pré-visualização e exigem a apresentação de uma nomeação através do site de pré-visualização dMS. Para uma listagem completa dos cenários em pré-visualização e para se inscrever para participar numa destas ofertas, consulte o [site de pré-visualização dMS](https://aka.ms/dms-preview/).

**Q. Que versões do SQL Server suporta o Serviço de Migração de Bases de Dados Azure como fonte?**
Ao migrar do SQL Server, as fontes suportadas para o Serviço de Migração de Bases de Dados Azure são o SQL Server 2005 através do SQL Server 2019.

**P: Ao utilizar o Serviço de Migração de Bases de Dados Azure, qual é a diferença entre uma migração offline e uma migração online?**
Pode utilizar o Serviço de Migração de Bases de Dados Azure para realizar migrações offline e online. Com uma migração *offline,* o tempo de inatividade da aplicação começa quando a migração começa. Com uma migração *online,* o tempo de inatividade limita-se ao tempo de redução no final da migração. Sugerimos que teste uma migração offline para determinar se o período de inatividade é aceitável; se não for, faça uma migração online.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço de [Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

**P. Como é que o Azure Database Migration Service se compara a outras ferramentas de migração da base de dados da Microsoft, como o Assistant de Migração de Bases de Dados (DMA) ou o Assistente de Migração do Servidor SQL (SSMA)?**
O Azure Database Migration Service é o método preferido para a migração de bases de dados para o Microsoft Azure em escala. Para mais detalhes sobre como o Azure Database Migration Service se compara a outras ferramentas de migração de bases de dados da Microsoft e para recomendações sobre a utilização do serviço para vários cenários, consulte o blog [publicando Differentiating](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)Database Migration Tools and Services da Microsoft .

**Q. Como é que o Serviço de Migração de Bases de Dados Azure se compara à oferta de migração Azure?**
A Azure Migrate ajuda na migração de máquinas virtuais no local para o Azure IaaS. O serviço avalia a adequação da migração e o tamanho baseado no desempenho, e fornece estimativas de custos para executar as suas máquinas virtuais no local em Azure. A Azure Migrate é útil para migrações de cargas de trabalho baseadas em VM no local para VMs Azure IaaS. No entanto, ao contrário do Serviço de Migração de Bases de Dados Azure, o Azure Migrate não é um serviço de migração especializado para a base de dados relacional Azure PaaS, como a Base de Dados Azure SQL ou a Azure SQL Database Managed Instance.

## <a name="setup"></a>Configurar

**Q. Quais são os pré-requisitos para a utilização do Serviço de Migração de Bases de Dados Azure?**
Existem vários pré-requisitos necessários para garantir que o Serviço de Migração de Bases de Dados Azure funciona sem problemas ao realizar migrações de bases de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares de alvo de origem) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos de um cenário específico.

Os pré-requisitos do Serviço de Migração da Base de Dados Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir em cenários específicos de migração utilizando o Serviço de Migração de Bases de Dados Azure, consulte os tutoriais relacionados na [documentação](https://docs.microsoft.com/azure/dms/dms-overview) do Serviço de Migração da Base de Dados Azure sobre docs.microsoft.com.

**P. Como encontro o endereço IP do Serviço de Migração de Bases de Dados Azure para que eu possa criar uma lista de autorizações para as regras de firewall usadas para aceder à minha base de dados de origem para migração?**
Pode ser necessário adicionar regras de firewall que permitam ao Serviço de Migração de Bases de Dados Azure aceder à sua base de dados de origem para migração. O endereço IP do serviço é dinâmico, mas se estiver a utilizar o ExpressRoute, este endereço é atribuído em privado pela sua rede corporativa. A forma mais fácil de identificar o endereço IP apropriado é procurar no mesmo grupo de recursos que o seu recurso do Serviço de Migração de Bases de Dados Azure para encontrar a Interface de Rede associada. Normalmente, o nome do recurso Interface de Rede começa com o prefixo NIC e seguido por uma sequência de caracteres e números única, por exemplo NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar este recurso de interface de rede, pode ver o endereço IP que precisa de ser incluído na lista de licenciamento na página do portal Azure.

Também pode ser necessário incluir a fonte de porta que o SQL Server está a ouvir na lista de licenças. Por padrão, é a porta 1433, mas a fonte SQL Server pode estar configurada para ouvir outras portas também. Neste caso, você precisa incluir essas portas na lista de espera também. Pode determinar a porta que o SQL Server está a ouvir utilizando uma consulta de Visão de Gestão Dinâmica:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Também pode determinar a porta que o SQL Server está a ouvir consultando o registo de erro do Servidor SQL:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Q. Como configurar uma Rede Virtual Microsoft Azure?**
Enquanto vários tutoriais da Microsoft que podem acompanhá-lo através do processo de criação de uma rede virtual, a documentação oficial aparece no artigo [Rede Virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Utilização

**Q. Qual é o resumo dos passos necessários para utilizar o Serviço de Migração de Bases de Dados Azure para realizar uma migração de bases de dados?**
Durante uma migração típica e simples da base de dados, você:

1. Criar uma base de dados de alvos.
2. Avalie a sua base de dados de origem.
    * Para migrações homogéneas, avalie a (s) base de dados existente utilizando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogéneas (de fontes de concorrência), avalie a sua base de dados existente com [O SSMA](https://aka.ms/get-ssma). Também usa o SSMA para converter objetos de base de dados e migrar o esquema para a sua plataforma alvo.
3. Crie uma instância do Serviço de Migração de Bases de Dados Azure.
4. Crie um projeto de migração que especifique a base de dados de origem, a base de dados alvo e as tabelas para migrar.
5. Inicie a carga completa.
6. Escolha a validação subsequente.
7. Execute uma transição manual do seu ambiente de produção para a nova base de dados baseada na nuvem.

## <a name="troubleshooting-and-optimization"></a>Resolução e otimização de problemas

**Q. Estou a criar um projeto de migração no DMS, e estou a ter dificuldade em ligar-me à minha base de dados de origem. O que devo fazer?**
Se tiver problemas em ligar-se ao seu sistema de base de dados de origem enquanto trabalha na migração, crie uma máquina virtual na rede virtual com a qual configura a sua instância de DMS. Na máquina virtual, deverá ser capaz de executar um teste de ligação, como utilizar um ficheiro UDL para testar uma ligação ao SQL Server ou descarregar robo 3T para testar as ligações MongoDB. Se o teste de ligação for bem sucedido, não deverá ter problemas em ligar-se à sua base de dados de origem. Se o teste de ligação não for bem sucedido, contacte o administrador da rede.

**P. Porque é que o meu Serviço de Migração de Bases de Dados Azure está indisponível ou parado?**
Se o utilizador parar explicitamente o Serviço de Migração de Bases de Dados Azure (DMS) ou se o serviço estiver inativo durante um período de 24 horas, o serviço estará em estado de paragem ou pausa automática. Em cada caso, o serviço não estará disponível e em estado de paragem.  Para retomar as migrações ativas, reinicie o serviço.

**Q. Existem recomendações para otimizar o desempenho do Serviço de Migração de Bases de Dados Azure?**
Pode fazer algumas coisas para acelerar a migração da sua base de dados através do serviço:

* Utilize o nível de preços de propósito geral multi CPU quando criar a sua instância de serviço para permitir que o serviço aproveite vários vCPUs para paraparação e transferência de dados mais rápida.
* Dimensione temporariamente a sua instância de alvo de base de dados Azure SQL para o SKU de nível Premium durante a operação de migração de dados para minimizar o estrangulamento da Base de Dados Azure SQL que pode afetar as atividades de transferência de dados ao utilizar SKUs de nível inferior.

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e da disponibilidade regional, consulte o artigo O que é o Serviço de Migração de Bases de [Dados Azure.](dms-overview.md)
