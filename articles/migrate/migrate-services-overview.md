---
title: Acerca do Azure Migrate | Microsoft Docs
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498742"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida das migrações para Azure.

As migrações para Azure ajudam a migrar do local para o Azure. As migrações para Azure fornecem um hub centralizado para acompanhar a descoberta, avaliação e migração de infraestrutura, aplicativos e dados locais para o Azure. O Hub fornece ferramentas e serviços do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. As migrações para Azure fornecem:

- **Plataforma de migração unificada**: um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: Ferramentas nativas e integração com outros serviços do Azure, bem como com ferramentas de ISV. Selecione as ferramentas de avaliação e migração certas com base em seus requisitos organizacionais.
- **Cargas de trabalho**: as migrações para Azure fornecem ferramentas de avaliação e migração para:
    - **Servidores**: Use ferramentas da Microsoft ou ferramentas de ISV para avaliação e migração de servidores para VMs do Azure.
    - **Bancos de dados**: Aproveite as ferramentas da Microsoft e do ISV para avaliação e migração de bancos de dados locais para o Azure SQL DB ou azure SQL instância gerenciada.
    - **Aplicativos Web**: Use o assistente de serviço Azure app para avaliar e migrar aplicativos Web locais para o serviço Azure app.
    - **Áreas de trabalho virtuais**: Use ferramentas de ISV para avaliar e migrar a infraestrutura de área de trabalho virtual (VDI) local para a área de trabalho virtual do Windows no Azure.
    - **Dados**: Use a família de produtos Azure data box para migrar dados de forma rápida e econômica para o Azure.

## <a name="azure-migrate-versions"></a>Versões de migrações para Azure

Atualmente, há duas versões do serviço migrações para Azure:

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Não é mais possível criar projetos de migrações para Azure usando a versão anterior e recomendamos que você não execute novas descobertas. Para acessar projetos existentes, na portal do Azure > **todos os serviços**, procure **migrações para Azure**. No painel migrações para Azure, há uma notificação e um link para acessar projetos antigos de migrações para Azure.

## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas da Microsoft, as migrações para Azure integram-se com várias ofertas de ISV. 

**FICHEIRO** | **Funcionalidade**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar
[Dispositivo 42](https://docs.device42.com/) | Avaliar
[O turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar
[Tecnologia de coaluguel](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar



### <a name="selecting-an-isv-tool"></a>Selecionando uma ferramenta ISV

Você identifica a ferramenta de que precisa e a adiciona a um projeto de migrações para Azure.

- Depois de adicionar uma ferramenta de ISV, você começará obtendo uma licença ou inscrevendo-se para uma avaliação gratuita, de acordo com a política de ISV. O licenciamento para ferramentas ISV está de acordo com o modelo de licenciamento ISV.
- Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure. L
- Acompanhe centralmente sua jornada de migração de dentro do projeto de migrações para Azure, nas ferramentas do Azure e do ISV.


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


## <a name="database-assessment"></a>Avaliação do banco de dados

As migrações para Azure integram-se com o Assistente de Migração de Dados da Microsoft (DMA) para avaliar bancos de dados SQL Server locais para migração para o BD SQL do Azure, Azure SQL Instância Gerenciada ou VMs do Azure em execução SQL Server. O DMA fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos sem suporte, bem como novos recursos dos quais você pode se beneficiar após a migração e ajuda a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Migração de bases de dados

As migrações para Azure integram-se ao serviço de migração de banco de dados do Azure (DMS) para migrar banco de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e instâncias gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migração de aplicativo Web

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
