---
title: Acerca do Azure Migrate | Microsoft Docs
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 03918976935f9071ba2a7951e29195118943a8ef
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845831"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida das migrações para Azure.

As migrações para Azure ajudam a migrar para o Azure. As migrações para Azure fornecem um hub centralizado para acompanhar a descoberta, avaliação e migração de infraestrutura, aplicativos e dados locais para o Azure. O Hub fornece ferramentas do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. Fornece:

- **Plataforma de migração**unificada: Use um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: As migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas de ISV. Selecione as ferramentas de avaliação e migração certas com base em seus requisitos organizacionais. 
- **Avaliação do servidor**de migrações para Azure: Use a ferramenta de avaliação do servidor para avaliar VMs VMware locais e VMs do Hyper-V para migração para o Azure.
- **Migração de servidor**de migrações para Azure: Use a ferramenta de migração de servidor para migrar VMs VMware locais, VMs do Hyper-V, VMs de nuvem e servidores físicos para o Azure.
- **Avaliação de banco de dados**de migrações para Azure: Avaliar bancos de dados locais para migração para o Azure.
- **Migração de banco de dados**de migrações para Azure: Migre bancos de dados locais para o Azure.


## <a name="azure-migrate-versions"></a>Versões de migrações para Azure

Há duas versões do serviço migrações para Azure:

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: Se você estava usando a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local era suportada), agora você deve usar a versão atual. Não é mais possível criar projetos de migrações para Azure usando a versão anterior e recomendamos que você não execute novas descobertas. Para acessar projetos existentes, na portal do Azure > **todos os serviços**, procure migrações para **Azure**. No painel migrações para Azure, há uma notificação e um link para acessar projetos antigos de migrações para Azure.

## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas do Azure, as migrações para Azure integram-se com várias ofertas de ISV. Você identifica a ferramenta de que precisa e a adiciona a um projeto de migrações para Azure. Você pode controlar centralmente sua jornada de migração de dentro do projeto de migrações para Azure, entre as ferramentas do Azure e do ISV.

**ISV** | **Funcionalidade**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar
[Dispositivo 42](https://docs.device42.com/) | Avaliar
[O turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar
[Tecnologia de coaluguel](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar

### <a name="selecting-an-isv-tool"></a>Selecionando uma ferramenta ISV

Depois de adicionar uma ferramenta ISV a um projeto de migrações para Azure, comece a usar a ferramenta obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure. O licenciamento para ferramentas ISV está de acordo com o modelo de licenciamento ISV.

## <a name="azure-migrate-server-assessment"></a>Avaliação do servidor de migrações para Azure

A avaliação de servidor de migrações para Azure descobre e avalia VMs VMware locais e VMs do Hyper-V para migração para o Azure. Ele ajuda a identificar o seguinte:

- **Preparação do Azure:** Avalie se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** Estimar o tamanho das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Estima os custos para executar servidores locais no Azure.
- **Visualização de dependência:** Identifique dependências entre servidores e a melhor maneira de mover servidores dependentes para o Azure. 

A avaliação do servidor usa um dispositivo leve que você implanta no local e se registra na avaliação do servidor.

- O dispositivo descobre máquinas locais, conecta-se à avaliação do servidor e envia continuamente metadados e dados relacionados ao desempenho para migrações para o Azure.
- A descoberta é sem agente. Nada precisa ser instalado em VMs descobertas.
- Depois que os computadores são descobertos, você os reúne em grupos que normalmente consistem em VMs que você gostaria de migrar juntos.
- Cria uma avaliação para um grupo. Em seguida, você pode analisar a avaliação para descobrir sua estratégia de migração.

## <a name="azure-migrate-server-migration"></a>Migração de servidor de migrações para Azure

A migração de servidor de migrações para Azure ajuda a migrar VMs VMware locais, VMs do Hyper-V, servidores físicos, outros computadores virtualizados e VMs de nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou sem uma avaliação. 

## <a name="azure-migrate-database-assessment"></a>Avaliação de banco de dados migrações para Azure

As migrações para Azure integram-se com o Assistente de Migração de Dados (DMA) para avaliar bancos de dados SQL Server locais para migração para o BD SQL do Azure, Azure SQL Instância Gerenciada ou VMs do Azure em execução SQL Server. O DMA fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos sem suporte, bem como novos recursos dos quais você pode se beneficiar após a migração e ajuda a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migração de banco de dados de migração do Azure

As migrações para Azure integram-se ao serviço de migração de banco de dados do Azure (DMS) para migrar banco de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e instâncias gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Avaliação e migração de aplicativos Web

No Hub migrações para Azure, você pode avaliar e migrar aplicativos Web locais para o Azure.

- **Avaliar aplicativos Web Online**: Use Azure App Assistente de Migração de serviço para avaliar sites locais para migração para o serviço Azure App.
- **Migrar aplicativos Web**: Migre aplicativos Web .NET e PHP para o Azure, usando Azure App Assistente de Migração de serviço.

[Saiba mais.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migração de dados offline

Você pode usar a família de produtos Data Box offline para mover grandes quantidades de dados para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Passos Seguintes

- Experimente nossos tutoriais para avaliar [VMs do VMware](tutorial-assess-vmware.md) e VMs do [Hyper-V](tutorial-assess-hyper-v.md).
- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
