---
title: O que há de novo nas migrações para Azure
description: Saiba mais sobre as atualizações novas e recentes no serviço de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b3b8e24e7d84e492aeaba64573b6e909707bc5a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185761"
---
# <a name="whats-new-in-azure-migrate"></a>O que há de novo nas migrações para Azure

As [migrações para Azure](migrate-services-overview.md) ajudam a descobrir, avaliar e migrar servidores, aplicativos e dados locais para a nuvem Microsoft Azure. Este artigo resume os novos recursos nas migrações para Azure.



## <a name="update-november-2019"></a>Atualização (novembro de 2019)

Vários novos recursos foram adicionados à migração do Azure:

- **Avaliação do servidor físico**. Agora há suporte para a avaliação de servidores físicos locais, além da migração de servidor físico que já tem suporte.
- **Avaliação baseada em importação**. Agora há suporte para a avaliação de computadores que usam metadados e dados de desempenho fornecidos em um arquivo CSV.
- **Descoberta de aplicativos**: o migrações para Azure agora dá suporte à descoberta em nível de aplicativo de aplicativos, funções e recursos usando o dispositivo de migrações para Azure. Atualmente, isso tem suporte apenas para VMs VMware e é limitado apenas à descoberta (atualmente, não há suporte para avaliação). [Saber mais](how-to-discover-applications.md)
- **Visualização de dependência sem agente**: você não precisa mais instalar agentes explicitamente para visualização de dependência. Agora há suporte para ambos baseados em agente e em agente.
- **Área de trabalho virtual**: Use ferramentas de ISV para avaliar e migrar a infraestrutura de área de trabalho virtual (VDI) local para a área de trabalho virtual do Windows no Azure.
- **Aplicativo Web**: o serviço de Azure app assistente de migração, usado para avaliar e migrar aplicativos Web, agora está integrado às migrações para Azure.

Novas ferramentas de avaliação e migração foram adicionadas às migrações para Azure:

- **Rack**: oferta de migração na nuvem.
- **Mover**: oferecer avaliação.

[Saiba mais](migrate-services-overview.md) sobre como usar ferramentas e ofertas de ISV para avaliação e migração em migrações para Azure.

## <a name="release-version-july-2019"></a>Versão de lançamento (julho de 2019)

A versão atual do migrações para Azure foi lançada em julho de 2019.

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações.
- **Versão anterior**: para o cliente que usa a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Na versão anterior, você não pode mais criar novos projetos de migrações para Azure ou executar novas descobertas. Você ainda pode acessar projetos existentes. Para fazer isso no portal do Azure > **todos os serviços**, procure **migrações para Azure**. Nas notificações de migração do Azure, há um link para acessar projetos antigos de migrações para Azure.


### <a name="azure-migrate-features"></a>Recursos de migrações para Azure

A versão atual do migrações para Azure fornece vários recursos novos:


- **Plataforma de migração unificada**: as migrações para Azure agora fornecem um único portal para centralizar, gerenciar e acompanhar sua jornada de migração para o Azure, com uma experiência aprimorada de fluxo de implantação e Portal.
- **Ferramentas de avaliação e migração**: as migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas ISV (fornecedor independente de software). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração de ISV.
- **Avaliação de migrações para Azure**: usando a ferramenta de avaliação de servidor de migrações para Azure, você pode avaliar VMs do VMware e VMs do Hyper-V para migração para o Azure. Você também pode avaliar a migração usando outros serviços do Azure e ferramentas de ISV.
- **Migração de migrações para Azure**: usando a ferramenta de migração de servidor de migrações para Azure, você pode migrar VMs VMware locais e VMs do Hyper-V para o Azure, bem como servidores físicos, outros servidores virtualizados e VMs de nuvem privada/pública. Além disso, você pode migrar para o Azure usando as ferramentas de ISV.
- **Dispositivo de migrações para Azure**: as migrações para Azure implantam um dispositivo leve para descoberta e avaliação de VMs VMware locais e VMs do Hyper-V.
    - Esse dispositivo é usado pela avaliação de servidor de migrações para Azure e migração de servidor de migrações para Azure para migração sem agente.
    - O dispositivo descobre continuamente os metadados do servidor e os dados de desempenho para fins de avaliação e migração.  
- **Migração de VM do VMware**: migração de servidor de migrações para Azure fornece alguns métodos para migrar VMs VMware locais para o Azure.  Uma migração sem agente usando o dispositivo de migrações para Azure e uma migração baseada em agente que usa um dispositivo de replicação e implanta um agente em cada VM que você deseja migrar. [Saber mais](server-migrate-overview.md)
 - **Avaliação e migração de banco de dados**: de migrações para Azure, você pode avaliar bancos de dados locais para migração para o Azure usando o assistente de migração de banco de dados do Azure. Você pode migrar bancos de dados usando o serviço de migração de banco de dados do Azure.
- **Migração de aplicativo Web**: você pode avaliar aplicativos Web usando uma URL de ponto de extremidade pública com o serviço Azure app. Para a migração de aplicativos .NET internos, você pode baixar e executar o serviço de aplicativo Assistente de Migração.
- **Data Box**: importe dados offline de grandes quantidades no azure usando Azure data Box em migrações para Azure.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
- Experimente nossos tutoriais para avaliar [VMs do VMware](tutorial-assess-vmware.md) e VMs do [Hyper-V](tutorial-assess-hyper-v.md).
