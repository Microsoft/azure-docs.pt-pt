---
title: FAQ - Serviço de Migração de Bases de Dados de Azure
description: Frequentemente, perguntas sobre a utilização do Azure Database Migration Service para realizar migrações de bases de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 460380d9435528dcd8cbb3f877c8034fdc34f50c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134153"
---
# <a name="faq-about-using-azure-database-migration-service"></a>FAQ sobre a utilização do Serviço de Migração da Base de Dados de Azure

Este artigo lista comumente perguntas sobre a utilização do Azure Database Migration Service juntamente com respostas relacionadas.

## <a name="overview"></a>Descrição geral

**Q. O que é o Serviço de Migração da Base de Dados Azure?**
O Azure Database Migration Service é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo. O serviço está atualmente em Disponibilidade Geral, com os esforços de desenvolvimento em curso focados em:

* Fiabilidade e desempenho.
* Adição iterativa de pares de alvos de origem.
* Continuação do investimento em migrações isentas de atritos.

**Q. Que pares de origem/alvo suportam atualmente o Serviço de Migração da Base de Dados Azure?**
O serviço suporta atualmente uma variedade de pares de origem/alvo, ou cenários de migração. Para obter uma listagem completa do estado de cada cenário de migração disponível, consulte o artigo [Estado dos cenários de migração suportados pelo Serviço de Migração da Base de Dados Azure](./resource-scenario-status.md).

Outros cenários de migração estão em pré-visualização e exigem a submissão de uma nomeação através do site de pré-visualização DMS. Para obter uma listagem completa dos cenários em pré-visualização e para se inscrever para participar numa destas ofertas, consulte o [site de pré-visualização do DMS](https://aka.ms/dms-preview/).

**Q. Que versões do SQL Server suportam o Serviço de Migração da Base de Dados Azure como fonte?**
Ao migrar do SQL Server, as fontes suportadas para o Azure Database Migration Service são SQL Server 2005 através do SQL Server 2019.

**P: Ao utilizar o Azure Database Migration Service, qual é a diferença entre uma migração offline e uma migração online?**
Pode utilizar o Azure Database Migration Service para realizar migrações offline e online. Com uma migração *offline,* o tempo de inatividade da aplicação começa quando a migração começa. Com uma migração *online,* o tempo de inatividade limita-se ao tempo de corte no final da migração. Sugerimos que teste uma migração offline para determinar se o período de inatividade é aceitável; se não for, faça uma migração online.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço [de Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

**Q. Como é que o Azure Database Migration Service se compara a outras ferramentas de migração da base de dados da Microsoft, como o Assistente de Migração de Bases de Dados (DMA) ou o SQL Server Migration Assistant (SSMA)?**
O Azure Database Migration Service é o método preferido para a migração de bases de dados para o Microsoft Azure em escala. Para obter mais detalhes sobre como o Azure Database Migration Service compara com outras ferramentas de migração da base de dados da Microsoft e para recomendações sobre a utilização do serviço para vários cenários, consulte o blog que publica [a Diferenciação das Ferramentas e Serviços de Migração da Base de Dados da Microsoft.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)

**Q. Como é que o Azure Database Migration Service se compara à oferta da Azure Migrate?**
A Azure Migrate ajuda na migração de máquinas virtuais no local para Azure IaaS. O serviço avalia a adequação da migração e o dimensionamento baseado no desempenho, e fornece estimativas de custos para executar as suas máquinas virtuais no local em Azure. A Azure Migrate é útil para migrações de elevação e mudança de cargas de trabalho baseadas em VM para Azure IaaS VMs. No entanto, ao contrário do Azure Database Migration Service, a Azure Migrate não é uma oferta especializada de serviços de migração de bases de dados para plataformas de base de dados relacionais Azure PaaS, como a Base de Dados Azure SQL ou a Azure SQL Managed Instance.

**Q. O Serviço de Migração da Base de Dados armazena os dados do cliente?**
N.º O Serviço de Migração de Bases de Dados não armazena dados do cliente.

## <a name="setup"></a>Configuração

**Q. Quais são os pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure?**
Existem vários pré-requisitos necessários para garantir que o Serviço de Migração da Base de Dados de Azure funciona sem problemas ao realizar migrações de bases de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares de alvos de origem) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos de um cenário específico.

Os pré-requisitos do Serviço de Migração da Base de Dados Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Certifique-se de que as regras do grupo de segurança da rede virtual não bloqueiam as seguintes portas de comunicação 443, 53, 5671-5672, 9350-9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir cenários específicos de migração utilizando o Serviço de Migração da Base de Dados Azure, consulte os tutoriais relacionados na [documentação](./dms-overview.md) do Serviço de Migração da Base de Dados Azure sobre docs.microsoft.com.

**Q. Como encontro o endereço IP do Serviço de Migração da Base de Dados Azure para que possa criar uma lista de indicações para as regras de firewall utilizadas para aceder à minha base de dados de origem para migração?**
Poderá ter de adicionar regras de firewall que permitam ao Serviço de Migração da Base de Dados Azure aceder à sua base de dados de origem para migração. O endereço IP para o serviço é dinâmico, mas se estiver a utilizar o ExpressRoute, este endereço é atribuído em privado pela sua rede corporativa. A forma mais fácil de identificar o endereço IP apropriado é olhar para o mesmo grupo de recursos que o seu recurso do Serviço de Migração da Base de Dados Azure para encontrar a interface de rede associada. Normalmente, o nome do recurso Interface de Rede começa com o prefixo NIC e seguido por uma sequência de caracteres e números únicos, por exemplo, NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar este recurso de interface de rede, pode ver o endereço IP que precisa de ser incluído na lista de autorizações na página do portal Azure.

Também pode ter de incluir a fonte de porta que o SQL Server está a ouvir na lista de autorizações. Por padrão, é a porta 1433, mas o sql Server de origem pode estar configurado para ouvir em outras portas também. Neste caso, também é necessário incluir as portas na lista de autorizações. Pode determinar a porta que o SQL Server está a ouvir utilizando uma consulta Dynamic Management View:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Também pode determinar a porta que o SQL Server está a ouvir consultando o registo de erro do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Q. Como posso criar uma Rede Virtual Microsoft Azure?**
Enquanto vários tutoriais da Microsoft que podem acompanhá-lo através do processo de criação de uma rede virtual, a documentação oficial aparece no artigo [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Utilização

**Q. O que é um resumo das etapas necessárias para utilizar o Serviço de Migração da Base de Dados Azure para realizar uma migração de bases de dados?**
Durante uma migração típica e simples de bases de dados, você:

1. Criar uma base de dados alvo.
2. Avalie a sua base de dados de origem.
    * Para migrações homogéneas, avalie a sua(s) base de dados existente utilizando [o DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogéneas (de fontes de concorrência), avalie a sua(s) base de dados existente com [SSMA](/sql/ssma/sql-server-migration-assistant). Também utiliza sSMA para converter objetos de base de dados e migrar o esquema para a sua plataforma alvo.
3. Crie uma instância do Azure Database Migration Service.
4. Criar um projeto de migração especificando a(s) base de dados de origem,s, base de dados-alvo e as tabelas para migrar.
5. Comece a carga completa.
6. Escolha a validação subsequente.
7. Efetue uma transição manual do seu ambiente de produção para a nova base de dados baseada na nuvem.

## <a name="troubleshooting-and-optimization"></a>Resolução de problemas e otimização

**Q. Estou a criar um projeto de migração em DMS, e estou a ter dificuldades em ligar-me à minha base de dados de origem. O que devo fazer?**
Se tiver problemas em ligar-se ao seu sistema de base de dados de origem enquanto trabalha na migração, crie uma máquina virtual na mesma sub-rede da rede virtual com a qual configura a sua instância DMS. Na máquina virtual, deverá ser capaz de realizar um teste de ligação, como utilizar um ficheiro UDL para testar uma ligação ao SQL Server ou descarregar robo 3T para testar as ligações mongoDB. Se o teste de ligação for bem sucedido, não deverá ter qualquer problema em ligar-se à sua base de dados de origem. Se o teste de ligação não for bem sucedido, contacte o administrador da rede.

**Q. Porque é que o meu Serviço de Migração da Base de Dados Azure está indisponível ou parado?**
Se o utilizador parar explicitamente o Serviço de Migração da Base de Dados de Azure (DMS) ou se o serviço estiver inativo por um período de 24 horas, o serviço estará num estado parado ou automático. Em cada caso, o serviço estará indisponível e em estado de paragem.  Para retomar as migrações ativas, reinicie o serviço.

**Q. Existem recomendações para otimizar o desempenho do Serviço de Migração da Base de Dados Azure?**
Pode fazer algumas coisas para acelerar a migração da sua base de dados utilizando o serviço:

* Utilize o nível de preços para fins gerais multi CPU quando criar a sua instância de serviço para permitir que o serviço aproveite vários vCPUs para para paralelização e transferência de dados mais rápida.
* Dimensione temporariamente a sua instância-alvo da Base de Dados Azure SQL para o SKU de nível Premium durante a operação de migração de dados para minimizar o estrangulamento da Base de Dados Azure SQL que pode afetar as atividades de transferência de dados quando utilizar SKUs de nível inferior.

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e disponibilidade regional, consulte o artigo [O que é o Serviço de Migração de Bases de Dados Azure.](dms-overview.md)
