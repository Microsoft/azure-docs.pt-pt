---
title: FAQ-serviço de migração de banco de dados do Azure
description: Perguntas frequentes sobre como usar o serviço de migração de banco de dados do Azure para executar migrações de banco de dados
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9fdbf3888b4302946fe20259a333842caa122836
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717785"
---
# <a name="faq-about-using-azure-database-migration-service"></a>FAQ sobre como usar o serviço de migração de banco de dados do Azure

Este artigo lista as perguntas frequentes sobre como usar o serviço de migração de banco de dados do Azure junto com respostas relacionadas.

## <a name="overview"></a>Visão geral

**P. o que é o serviço de migração de banco de dados do Azure?**
O serviço de migração de banco de dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure com tempo de inatividade No momento, o serviço está em disponibilidade geral, com esforços de desenvolvimento em andamento com foco em:

* Confiabilidade e desempenho.
* Adição iterativa de pares de origem-destino.
* Investimento contínuo em migrações sem conflitos.

**P. para quais pares de origem/destino o serviço de migração de banco de dados do Azure oferece suporte atualmente?**
O serviço atualmente dá suporte a uma variedade de pares de origem/destino ou cenários de migração. Para obter uma lista completa do status de cada cenário de migração disponível, consulte o [status do artigo de cenários de migração com suporte no serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Outros cenários de migração estão em versão prévia e exigem o envio de uma indicação por meio do site de visualização do DMS. Para obter uma lista completa dos cenários na visualização e para se inscrever para participar de uma dessas ofertas, consulte o [site de visualização do DMS](https://aka.ms/dms-preview/).

**P. quais versões do SQL Server o serviço de migração de banco de dados do Azure oferece suporte como fonte?**
Ao migrar do SQL Server, as fontes com suporte para o serviço de migração de banco de dados do Azure são SQL Server 2005 por meio de SQL Server 2019.

**P: ao usar o serviço de migração de banco de dados do Azure, qual é a diferença entre uma migração offline e online?**
Você pode usar o serviço de migração de banco de dados do Azure para realizar migrações online e offline. Com uma migração *offline* , o tempo de inatividade do aplicativo começa quando a migração é iniciada. Com uma migração *online* , o tempo de inatividade é limitado ao período de redução no final da migração. Sugerimos que teste uma migração offline para determinar se o período de inatividade é aceitável; se não for, faça uma migração online.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium. Para obter mais informações, consulte a página [preços](https://azure.microsoft.com/pricing/details/database-migration/) do serviço de migração de banco de dados do Azure.

**P. como o serviço de migração de banco de dados do Azure se compara a outras ferramentas de migração de banco de dados da Microsoft, como o banco de dados Assistente de Migração (DMA) ou o Assistente de Migração do SQL Server (SSMA)?**
O serviço de migração de banco de dados do Azure é o método preferencial para a migração de banco de dados para Microsoft Azure em escala. Para obter mais detalhes sobre como o serviço de migração de banco de dados do Azure se compara a outras ferramentas de migração de banco de dados da Microsoft e recomendações sobre como usar o serviço para vários cenários, consulte a postagem de blog [diferenciando ferramentas e serviços de migração de banco de dados](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)

**P. como o serviço de migração de banco de dados do Azure se compara à oferta de migrações para Azure?**
As migrações para Azure auxiliam na migração de máquinas virtuais locais para IaaS do Azure. O serviço avalia a adequação da migração e o dimensionamento com base no desempenho e fornece estimativas de custo para executar suas máquinas virtuais locais no Azure. As migrações para Azure são úteis para migrações de comparação de precisão e deslocamento de cargas de trabalho baseadas em VM locais para VMs IaaS do Azure. No entanto, ao contrário do serviço de migração de banco de dados do Azure, Instância Gerenciada do Banco de Dados SQL do Azure as migrações para Azure não são uma oferta de serviço de migração de banco de dados especializada para plataformas de banco de dados relacionais do Azure PaaS

## <a name="setup"></a>Configuração

**P. quais são os pré-requisitos para usar o serviço de migração de banco de dados do Azure?**
Há vários pré-requisitos necessários para garantir que o serviço de migração de banco de dados do Azure seja executado sem problemas ao executar migrações de banco de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de destino de origem) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos do serviço de migração de banco de dados do Azure que são comuns em todos os cenários de migração com suporte incluem a necessidade de:

* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que suas regras de grupo de segurança de rede virtual não bloqueiem as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir com cenários de migração específicos usando o serviço de migração de banco de dados do Azure, consulte os tutoriais relacionados na [documentação](https://docs.microsoft.com/azure/dms/dms-overview) do serviço de migração de banco de dados do azure em docs.Microsoft.com.

**P. Como fazer localizar o endereço IP do serviço de migração de banco de dados do Azure para que eu possa criar uma lista de permissões para as regras de firewall usadas para acessar meu banco de dados de origem para migração?**
Talvez seja necessário adicionar regras de firewall, permitindo que o serviço de migração de banco de dados do Azure acesse o banco de dados de origem para migração. O endereço IP para o serviço é dinâmico, mas se você estiver usando o ExpressRoute, esse endereço será atribuído de forma privada por sua rede corporativa. A maneira mais fácil de identificar o endereço IP apropriado é examinar o mesmo grupo de recursos que o recurso de serviço de migração de banco de dados do Azure provisionado para localizar a interface de rede associada. Geralmente, o nome do recurso de interface de rede começa com o prefixo da NIC e seguido por um caractere exclusivo e uma sequência numérica, por exemplo, NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar esse recurso de interface de rede, você pode ver o endereço IP que precisa ser incluído na lista de permissões na página Visão geral do recurso portal do Azure.

Talvez você também precise incluir a origem da porta que SQL Server está escutando na lista de permissões. Por padrão, a porta 1433, mas a SQL Server de origem pode ser configurada para escutar em outras portas também. Nesse caso, você também precisa incluir essas portas na lista de permissões. Você pode determinar a porta que SQL Server está escutando usando uma consulta de exibição de gerenciamento dinâmico:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Você também pode determinar a porta que SQL Server está ouvindo consultando o log de erros do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**P. Como fazer configurar um Rede Virtual do Microsoft Azure?**
Embora vários tutoriais da Microsoft possam orientá-lo durante o processo de configuração de uma rede virtual, a documentação oficial aparece no artigo [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Utilização

**P. o que é um resumo das etapas necessárias para usar o serviço de migração de banco de dados do Azure para executar uma migração de banco de dados?**
Durante uma migração de banco de dados simples e típica, você:

1. Crie um banco (s) de dados de destino.
2. Avalie seus bancos de dados de origem.
    * Para migrações homogêneas, avalie seus bancos de dados existentes usando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogéneas (de fontes de concorrência), avalie a sua base de dados existente com [O SSMA](https://aka.ms/get-ssma). Você também usa o SSMA para converter objetos de banco de dados e migrar o esquema para sua plataforma de destino.
3. Crie uma instância do Serviço de Migração de Bases de Dados Azure.
4. Crie um projeto de migração especificando os bancos de dados de origem, os bancos de dados de destino e as tabelas a serem migradas.
5. Inicie a carga completa.
6. Escolha a validação subsequente.
7. Execute uma comutação manual de seu ambiente de produção para o novo banco de dados baseado em nuvem.

## <a name="troubleshooting-and-optimization"></a>Solução de problemas e otimização

**P. estou Configurando um projeto de migração no DMS e estou tendo dificuldades para se conectar ao meu banco de dados de origem. O que devo fazer?**
Se você tiver problemas para se conectar ao sistema de banco de dados de origem enquanto trabalha na migração, crie uma máquina virtual na rede virtual com a qual você configurou sua instância DMS. Na máquina virtual, você deve ser capaz de executar um teste do Connect, como usar um arquivo UDL para testar uma conexão com SQL Server ou baixar Robo 3T para testar conexões do MongoDB. Se o teste de conexão tiver sucesso, você não deverá ter um problema com a conexão com o banco de dados de origem. Se o teste de conexão não tiver sucesso, contate o administrador da rede.

**P. por que meu serviço de migração de banco de dados do Azure está indisponível ou parado?**
Se o usuário parar explicitamente o serviço de migração de banco de dados do Azure (DMS) ou se o serviço estiver inativo por um período de 24 horas, o serviço estará em um estado interrompido ou automaticamente pausado. Em cada caso, o serviço estará indisponível e em um status parado.  Para retomar as migrações ativas, reinicie o serviço.

**P. há alguma recomendação para otimizar o desempenho do serviço de migração de banco de dados do Azure?**
Você pode fazer algumas coisas para acelerar a migração de banco de dados usando o serviço:

* Use o tipo de preço Uso Geral de várias CPUs ao criar sua instância de serviço para permitir que o serviço Aproveite vários vCPUs para paralelização e transferência de dados mais rápida.
* Escale horizontalmente sua instância de destino do banco de dados SQL do Azure para a SKU da camada Premium durante a operação de migração, para minimizar a limitação do banco de dados SQL do Azure que pode afetar as atividades de transferência, ao usar SKUs de nível inferior.

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do serviço de migração de banco de dados do Azure e disponibilidade regional, consulte o artigo o [que é o serviço de migração de banco de dados do Azure](dms-overview.md).
