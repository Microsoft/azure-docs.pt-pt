---
title: Acerca do Azure Migrate
description: Saiba mais sobre a avaliação e a migração do servidor com o serviço migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a64e7366281f15c94d6551c1f7be27f461737634
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185797"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida das migrações para Azure.

As migrações para Azure ajudam a migrar sua empresa do local para o Azure. As migrações para Azure fornecem um hub centralizado para acompanhar a descoberta, avaliação e migração de infraestrutura, aplicativos e dados locais para o Azure.  As migrações para Azure fornecem:

- **Plataforma de migração unificada**: um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: o Hub fornece avaliação e migração de ferramentas de migrações para Azure e integra-se a outros serviços do Azure, além de outras ferramentas e ofertas de fornecedores independentes de software (ISV).
- **Cargas de trabalho**: as migrações para Azure fornecem avaliação e migração para:
    - **Servidores**: Use a avaliação de servidor de migrações para Azure, migração de servidor de migrações para Azure e outras ferramentas para avaliação e migração de servidores para VMs do Azure.
    - **Bancos de dados**: Aproveite as ferramentas da Microsoft e ISV para avaliação e migração de bancos de dados locais para o BD SQL do Azure ou o azure SQL instância gerenciada.
    - **Aplicativos Web**: Use o assistente de serviço Azure app para avaliar e migrar aplicativos Web locais para o serviço Azure app.
    - **Áreas de trabalho virtuais**: Use ferramentas de ISV para avaliar e migrar a infraestrutura de área de trabalho virtual (VDI) local para a área de trabalho virtual do Windows no Azure.
    - **Dados**: Use a família de produtos de Azure data box para migrar grandes quantidades de dados de forma rápida e econômica para o Azure.

## <a name="azure-migrate-versions"></a>Versões de migrações para Azure

Atualmente, há duas versões do serviço migrações para Azure:

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Não é mais possível criar projetos de migrações para Azure usando a versão anterior e recomendamos que você não execute novas descobertas. Para acessar projetos existentes, na portal do Azure > **todos os serviços**, procure **migrações para Azure**. No painel migrações para Azure, há uma notificação e um link para acessar projetos antigos de migrações para Azure.



## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas do Azure, as migrações para Azure integram-se com várias ofertas de ISV. 

**ISV** | **Funcionalidade**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar servidores
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores
[Tecnologia de coaluguel](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores
[Dispositivo 42](https://docs.device42.com/) | Avaliar servidores
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar o VDI
[Rack](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrar servidores
[O turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bancos de dados

## <a name="azure-tool-integration"></a>Integração de ferramentas do Azure

A tabela resume outras ferramentas que são integradas em migrações para Azure.

**Ferramenta** | **Detalhes**
--- | ---
Migrações para Azure: avaliação do servidor | Avaliar servidores
Migrações para Azure: migração de servidor | Migrar servidores
Assistente de Migração de banco de dados (DMA) | Avaliar bancos de dados
DMS (serviço de migração de banco de dados) | Migrar bases de dados
Movere | Avaliar servidores
Assistente de Migração de aplicativo Web | Avaliar e migrar aplicativos Web



### <a name="selecting-a-tool"></a>Selecionando uma ferramenta

Identifique a ferramenta de que você precisa e adicione-a a um projeto de migrações para Azure.

- Se você estiver adicionando uma ferramenta ISV ou um movimentador:
    - Comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ferramentas. O licenciamento para ferramentas está de acordo com o modelo de licenciamento de ISV ou ferramenta.
    - Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure.
- Acompanhe centralmente sua jornada de migração de dentro do projeto de migrações para Azure, no Azure e em outras ferramentas.



## <a name="azure-migrate-server-assessment-tool"></a>Ferramenta de avaliação do servidor de migrações para Azure

Migrações para Azure: a ferramenta de avaliação do servidor descobre e avalia as VMs VMware locais, VMs do Hyper-V e servidores físicos para migração para o Azure. Ele ajuda a identificar o seguinte:

- **Preparação do Azure:** Avalie se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** O tamanho estimado das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Custos estimados para execução de servidores locais no Azure.
- **Visualização de dependência:** Dependências entre servidores (se a visualização de dependência estiver habilitada) e maneiras ideais de mover servidores dependentes para o Azure.

A avaliação do servidor usa um dispositivo leve que você implanta no local e se registra na avaliação do servidor.

- O dispositivo descobre computadores locais.
- Ele se conecta à avaliação do servidor e envia continuamente metadados de computadores e dados de desempenho para migrações para Azure.
- A descoberta de dispositivo é sem agente. Nada precisa ser instalado em computadores descobertos.
- Após a descoberta, você coleta computadores descobertos em grupos. Normalmente, você reúne computadores que você gostaria de migrar juntos.
- Cria uma avaliação para um grupo. Em seguida, você analisa a avaliação para descobrir sua estratégia de migração.

## <a name="azure-migrate-server-migration-tool"></a>Ferramenta de Migração do Servidor do Azure Migrate

Migrações para Azure: a ferramenta de migração de servidor ajuda a migrar VMs VMware locais, VMs do Hyper-V, servidores físicos, outros computadores virtualizados e VMs de nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou sem uma avaliação.


## <a name="database-migration-assistant"></a>Assistente de Migração do banco de dados

As migrações para Azure integram-se com o Assistente de Migração de Dados da Microsoft (DMA) para avaliar bancos de dados SQL Server locais para migração para o BD SQL do Azure, Azure SQL Instância Gerenciada ou VMs do Azure em execução SQL Server. O DMA fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos sem suporte, bem como novos recursos dos quais você pode se beneficiar após a migração e ajuda a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Serviço de Migração de Bases de Dados

As migrações para Azure integram-se ao serviço de migração de banco de dados do Azure (DMS) para migrar banco de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e instâncias gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
O mover é uma plataforma SaaS que aumenta a business intelligence com a apresentação precisa de ambientes de ti inteiros em um único dia. À medida que as organizações crescem, mudam e otimizam digitalmente, a solução fornece às empresas a confiança de que eles precisam para ter visibilidade e controle de seus ambientes, independentemente da plataforma, do aplicativo ou da geografia. O mover foi [adquirido](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) pela Microsoft e não é mais vendido como uma oferta autônoma.  O mover está disponível por meio dos programas avaliação de solução da Microsoft e economia na nuvem. [Saiba mais](https://www.movere.io) sobre o mover. Se você tiver dúvidas, envie-as para: movereq@microsoft.com ou entre em contato com seu representante da Microsoft.

Incentivamos você a examinar também as migrações para Azure, nosso serviço de migração interno. As migrações para Azure fornecem um hub central para simplificar a migração para a nuvem. O Hub apresenta suporte abrangente para cargas de trabalho diferentes, incluindo servidores físicos e virtuais, bancos de dados e aplicativos. A visibilidade de ponta a ponta facilita o acompanhamento do progresso durante a descoberta, avaliação e migração. Com as ferramentas do Azure e do Partner ISV internas, as migrações para Azure também têm uma ampla gama de recursos, incluindo a descoberta de servidores físicos e virtuais, o dimensionamento correto baseado em desempenho, o planejamento de custos, as avaliações baseadas em importação e o aplicativo sem agente análise de dependência. Se você estiver procurando ajuda especializada para começar, a Microsoft tem especialista no [provedor de serviços gerenciados de especialistas do Azure](https://azure.microsoft.com/partners) para orientá-lo durante a jornada. Confira o [site migrações para Azure](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Assistente de Migração de aplicativo Web

As migrações para Azure integram-se com o Assistente de Migração de serviço Azure App. No Hub migrações para Azure, você pode avaliar e migrar aplicativos Web locais para o Azure, usando o assistente, da seguinte maneira:

- **Avaliar aplicativos Web Online**: Use Azure App serviço assistente de migração para avaliar sites locais para migração para o serviço Azure app.
- **Migrar aplicativos Web**: migre aplicativos Web .net e PHP para o Azure, usando Azure app assistente de migração de serviço.

[Saiba mais](https://appmigration.microsoft.com/) sobre o assistente.



## <a name="offline-data-migration"></a>Migração de dados offline

Você pode usar os produtos Azure Data Box para mover grandes quantidades de dados offline para o Azure. [Saber mais](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Passos seguintes

- Experimente nossos tutoriais para avaliar [VMs do VMware](tutorial-assess-vmware.md) e VMs do [Hyper-V](tutorial-assess-hyper-v.md).
- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
