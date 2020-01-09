---
title: Acerca do Azure Migrate
description: Saiba mais sobre o serviço migrações para Azure.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: 983ba420067bf2e4b6416287d8898943142c90c0
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563569"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida do serviço migrações para Azure.

Use as migrações para Azure para migrar para o Azure. As migrações para Azure fornecem um hub centralizado para avaliar e migrar a infraestrutura, os aplicativos e os dados locais para o Azure, com os seguintes recursos:

- **Plataforma de migração unificada**: um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: uma variedade de ferramentas para avaliação e migração. O Hub inclui migrações para Azure: avaliação de servidor e migrações para Azure: migração de servidor. Ele se integra a outros serviços do Azure e a outras ferramentas e ofertas de fornecedores independentes de software (ISV).
- **Avaliação e migração**: no Hub migrações para Azure, você pode avaliar e migrar:
    - **Servidores**: avaliar e migrar servidores locais para VMs do Azure.
    - **Bancos de dados**: avaliar e migrar bancos de dados locais para o BD SQL do Azure ou para o azure SQL instância gerenciada.
    - **Aplicativos Web**: avalie e migre aplicativos Web locais para Azure app serviço, usando o assistente de serviço do Azure app.
    - **Áreas de trabalho virtuais**: avalie e MIGRE sua VDI (infraestrutura de área de trabalho virtual) local para a área de trabalho virtual do Windows no Azure.
    - **Dados**: migre grandes quantidades de dados para o Azure de forma rápida e econômica, usando Azure data Box produtos. 


## <a name="integrated-tools"></a>Ferramentas integradas

O Hub migrações para Azure fornece as ferramentas a seguir.

**Ferramenta** | **Avaliar/migrar** | **Detalhes**
--- | --- | ---
**Migrações para Azure: avaliação do servidor** | Avaliar servidores | Descubra e avalie as VMs VMware locais, VMs Hyper-V e servidores físicos para descobrir se estão prontas para a migração para o Azure.
**Migrações para Azure: migração de servidor** | Migrar servidores | Migre VMs VMware, VMs do Hyper-V, servidores físicos, bem como outras máquinas virtualizadas e VMs de nuvem pública, para o Azure. 
**Assistente de Migração de banco de dados (DMA)** | Avaliar os bancos de dados SQL Server locais para migração para o BD SQL do Azure, SQL Instância Gerenciada do Azure ou VMs do Azure em execução SQL Server. | O DMA fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos sem suporte, bem como novos recursos dos quais você pode se beneficiar após a migração e ajuda a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**DMS (serviço de migração de banco de dados)** | Migre bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e instâncias gerenciadas do SQL do Azure. | [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre o DMS.
**Movere** | Avaliar servidores | [Saiba mais](#movere) sobre o mover.
**Assistente de Migração de aplicativo Web** | Avalie e migre aplicativos Web locais para o Azure. |  Use o Assistente de Migração de serviço Azure App para avaliar sites locais para migração para o serviço Azure App.<br/><br/> Migre aplicativos Web .NET e PHP para o Azure, usando Azure App Assistente de Migração de serviço. [Saiba mais](https://appmigration.microsoft.com/) sobre o assistente.
**Azure Data Box** | Migração de dados offline. | Use Azure Data Box produtos para mover grandes quantidades de dados offline para o Azure. [Saiba mais](https:/docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>Integração de ISV

As migrações para Azure integram-se com várias ofertas de ISV. 

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


## <a name="azure-migrate-server-assessment-tool"></a>Migrações para Azure: ferramenta de avaliação do servidor

A ferramenta migrações para Azure: Server Assessment descobre e avalia as VMs VMware locais, VMs do Hyper-V e servidores físicos para migração para o Azure. Ele ajuda a identificar o seguinte:

- **Preparação do Azure:** Avalie se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** O tamanho estimado das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Custos estimados para execução de servidores locais no Azure.
- **Visualização de dependência:** Se você usar a avaliação do servidor com a visualização de dependência, poderá identificar efetivamente as dependências entre servidores e as maneiras ideais de mover os servidores dependentes para o Azure.

A avaliação do servidor usa um dispositivo leve que você implanta no local e se registra na avaliação do servidor.

- O dispositivo é executado em um servidor físico ou VM e é facilmente instalado usando um modelo baixado.
- O dispositivo descobre computadores locais e envia continuamente metadados de computadores e dados de desempenho para migrações para Azure.
- A descoberta de dispositivo é sem agente. Nada precisa ser instalado em computadores descobertos.
- Após a descoberta, você coleta computadores descobertos em grupos e avalia os grupos de migração.


## <a name="azure-migrate-server-migration-tool"></a>Migrações para Azure: ferramenta de migração de servidor

A ferramenta migrações para Azure: Server Migration ajuda a migrar VMs VMware locais, VMs do Hyper-V, servidores físicos, outros computadores virtualizados e VMs de nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou migrá-los sem uma avaliação.


## <a name="select-a-tool"></a>Selecionar ferramenta

No Hub migrações para Azure, selecione a ferramenta que você deseja usar para avaliação e adicione-a a um projeto de migrações para Azure. Se você adicionar uma ferramenta ISV ou um movimentador:

- Comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com as instruções da ferramenta. O licenciamento de ferramentas é determinado pela ferramenta ISV/. 
- Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções para se conectar.
- Acompanhe sua jornada de migração de dentro do projeto de migrações para Azure em todas as ferramentas.


## <a name="movere"></a>Movere

O mover é uma plataforma SaaS que aumenta a business intelligence com a apresentação precisa de ambientes de ti inteiros em um único dia. À medida que as organizações crescem, mudam e otimizam digitalmente, a solução fornece às empresas a confiança de que eles precisam para ter visibilidade e controle de seus ambientes, independentemente da plataforma, do aplicativo ou da geografia. O mover foi [adquirido](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) pela Microsoft e não é mais vendido como uma oferta autônoma.  O mover está disponível por meio dos programas avaliação de solução da Microsoft e economia na nuvem. [Saiba mais](https://www.movere.io) sobre o mover. Se você tiver dúvidas, envie-as para: movereq@microsoft.com ou entre em contato com seu representante da Microsoft.

Incentivamos você a examinar também as migrações para Azure, nosso serviço de migração interno. As migrações para Azure fornecem um hub central para simplificar a migração para a nuvem. O Hub apresenta suporte abrangente para cargas de trabalho diferentes, incluindo servidores físicos e virtuais, bancos de dados e aplicativos. A visibilidade de ponta a ponta facilita o acompanhamento do progresso durante a descoberta, avaliação e migração. Com as ferramentas do Azure e do Partner ISV internas, as migrações para Azure também têm uma ampla gama de recursos, incluindo a descoberta de servidores físicos e virtuais, o dimensionamento correto baseado em desempenho, o planejamento de custos, as avaliações baseadas em importação e o aplicativo sem agente análise de dependência. Se você estiver procurando ajuda especializada para começar, a Microsoft tem especialista no [provedor de serviços gerenciados de especialistas do Azure](https://azure.microsoft.com/partners) para orientá-lo durante a jornada. Confira o [site migrações para Azure](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versões de migrações para Azure

Há duas versões do serviço migrações para Azure:

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Não é mais possível criar projetos de migrações para Azure usando a versão anterior e recomendamos que você não execute novas descobertas. Para acessar projetos existentes, na portal do Azure, pesquise e selecione **migrações para Azure**. No painel **migrações para Azure** , há uma notificação e um link para acessar projetos antigos de migrações para Azure.



## <a name="next-steps"></a>Passos seguintes

- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-prepare-vmware.md), [VMs Hyper-V](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md).
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
