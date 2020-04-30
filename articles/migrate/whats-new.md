---
title: O que há de novo em Azure Migrate
description: Saiba quais são as novidades e as novidades recentes no serviço Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677235"
---
# <a name="whats-new-in-azure-migrate"></a>O que há de novo em Azure Migrate

[A Azure Migrate](migrate-services-overview.md) ajuda-o a descobrir, avaliar e migrar no local servidores, apps e dados para a nuvem do Microsoft Azure. Este artigo resume novos lançamentos e funcionalidades no Azure Migrate.


## <a name="update-april-2020"></a>Atualização (abril de 2020)

A Azure Migrate apoia as implantações no Governo de Azure. 

- Pode descobrir e avaliar VMs VMware, VMs Hiper-V e servidores físicos.
- Pode migrar VMware VMs, VMs hiper-V e servidores físicos para o Azure.
- Para a migração vMware, você pode usar migração sem agente ou baseada em agente. [Saiba mais](server-migrate-overview.md).
- [Reveja](migrate-support-matrix.md#supported-geographies-azure-government) geografias e regiões apoiadas para o Governo de Azure.
- [A análise da dependência baseada em agentes](concepts-dependency-visualization.md#agent-based-analysis) não é apoiada no Governo de Azure.
- As funcionalidades em pré-visualização são suportadas no Governo azure, especificamente na análise de [dependência sem agente,](concepts-dependency-visualization.md#agentless-analysis)e na [descoberta de aplicações.](how-to-discover-applications.md)

## <a name="update-march-2020"></a>Atualização (março de 2020)

Uma instalação baseada em scripts está agora disponível para configurar o [aparelho Migratório Azure:](migrate-appliance.md)

- A instalação baseada em scripts é uma alternativa à . INSTALAÇÃO OVA (VMware)/VHD (Hyper-V) do aparelho.
- Fornece um script de instalação PowerShell que pode ser usado para configurar o aparelho para VMware/Hyper-V numa máquina existente que executa o Windows Server 2016.

## <a name="update-november-2019"></a>Atualização (novembro de 2019)

Foram adicionadas várias novidades ao Azure Migrate:

- **Avaliação física do servidor.** A avaliação dos servidores físicos no local é agora suportada, além da migração física do servidor que já é suportada.
- **Avaliação baseada em importações.** A avaliação das máquinas utilizando metadados e dados de desempenho fornecidos num ficheiro CSV é agora suportada.
- **Descoberta de aplicações**: A Azure Migrate suporta agora a descoberta a nível de aplicações de apps, funções e funcionalidades utilizando o aparelho Azure Migrate. Atualmente, isto é suportado apenas para VMware VMs, e está limitado apenas à descoberta (a avaliação não é atualmente suportada). [Mais informações](how-to-discover-applications.md)
- **Visualização da dependência sem agente:** Já não é necessário instalar explicitamente agentes para visualização da dependência. Tanto o agente como o agente são agora apoiados.
- **Ambiente de trabalho virtual**: Utilize ferramentas ISV para avaliar e migrar no local a infraestrutura virtual de ambiente de trabalho (VDI) para o Windows Virtual Desktop em Azure.
- **Aplicação web**: O Assistente de Migração do Serviço de Aplicações Azure, utilizado para avaliar e migrar aplicações web, está agora integrado no Azure Migrate.

Foram adicionados novos instrumentos de avaliação e migração ao Azure Migrate:

- **Rackware**: Oferecendo migração em nuvem.
- **Movere**: Avaliação de oferta.

[Saiba mais](migrate-services-overview.md) sobre a utilização de ferramentas e ofertas isv para avaliação e migração em Azure Migrate.

## <a name="azure-migrate-current-version"></a>Versão atual do Azure Migrate

A versão atual do Azure Migrate (lançado em julho de 2019) fornece uma série de novas funcionalidades:

- **Plataforma de migração unificada**: A Azure Migrate fornece agora um único portal para centralizar, gerir e acompanhar a sua viagem de migração até Azure, com uma melhor experiência de fluxo de implantação e portal.
- **Ferramentas de avaliação e migração**: A Azure Migrate fornece ferramentas nativas e integra-se com outros serviços Azure, bem como com ferramentas independentes de fornecedor de software (ISV). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração do ISV.
- **Avaliação do Azure Migrate**: Utilizando a ferramenta de avaliação do servidor migratório Azure, pode avaliar VMware VMs e VMs Hiper-V para migração para Azure. Também pode avaliar para a migração usando outros serviços Azure, e ferramentas ISV.
- **Migração de migração Azure**: Utilizando a ferramenta de migração do servidor migratório Azure, pode migrar no local VMware VMs e VMs Hiper-V para Azure, bem como servidores físicos, outros servidores virtualizados e VMs de nuvem privada/pública. Além disso, pode migrar para Azure utilizando ferramentas ISV.
- **Aparelho migratório Azure**: A Azure Migrate implanta um aparelho leve para descoberta e avaliação de VMware VMware no local e VMs hiper-V.
    - Este aparelho é utilizado pela Azure Migrate Server Assessment e pela Migração do Servidor Migratório Azure para migração sem agente.
    - O aparelho descobre continuamente metadados de servidores e dados de desempenho, para efeitos de avaliação e migração.  
- **Migração VMware VM**: A Migração do Servidor Migratório Azure fornece um par de métodos para migrar no local VMware VMware VMs para Azure.  Uma migração sem agente utilizando o aparelho Azure Migrate, e uma migração baseada em agentes que usa um aparelho de replicação, e coloca um agente em cada VM que pretende migrar. [Mais informações](server-migrate-overview.md)
 - **Avaliação e migração**de bases de dados : A partir do Azure Migrate, pode avaliar as bases de dados no local para migração para Azure utilizando o Assistente de Migração de Bases de Dados Azure. Pode migrar bases de dados utilizando o Serviço de Migração de Bases de Dados Azure.
- **Migração de aplicativos web**: Você pode avaliar aplicações web usando um URL de ponto final público com o Serviço de Aplicações Azure. Para a migração de aplicações internas .NET, pode descarregar e executar o Assistente de Migração do Serviço de Aplicações.
- **Data Box**: Importar grandes quantidades de dados offline para O Azure utilizando a Caixa de Dados Azure em Migração Azure.

## <a name="azure-migrate-previous-version"></a>Versão anterior do Azure Migrate

Se estiver a utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMware VMno no local), deverá agora utilizar a versão atual. Na versão anterior, já não é possível criar novos projetos azure migrate, ou realizar novas descobertas. Ainda pode aceder a projetos existentes. Para isso no portal Azure > **Todos os serviços,** procure o **Azure Migrate.** Nas notificações do Azure Migrate, há uma ligação para aceder a antigos projetos do Azure Migrate.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
- Experimente os nossos tutoriais para avaliar [VMware VMs](tutorial-assess-vmware.md) e [VMs Hiper-V](tutorial-assess-hyper-v.md).
