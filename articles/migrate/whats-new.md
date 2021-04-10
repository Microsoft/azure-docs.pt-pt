---
title: Novidades em Azure Migrate
description: Saiba mais sobre as novidades e novidades recentes no serviço Azure Migrate.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: cca4612d3b22296209b4adfc6be97cbe95477aa3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786673"
---
# <a name="whats-new-in-azure-migrate"></a>Novidades em Azure Migrate

[O Azure Migrate](migrate-services-overview.md) ajuda-o a descobrir, avaliar e migrar para o local servidores, apps e dados para a nuvem do Microsoft Azure. Este artigo resume novos lançamentos e funcionalidades em Azure Migrate.

## <a name="update-march-2021"></a>Atualização (março de 2021)
- Suporte para fornecer múltiplas credenciais de servidor no aparelho Azure Migrate para descobrir aplicações instaladas (inventário de software), análise de dependência de agentes e descobrir instâncias e bases de dados do SQL Server no seu ambiente VMware. [Saiba mais](tutorial-discover-vmware.md#provide-server-credentials)
- A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. [Saiba mais](concepts-azure-sql-assessment-calculation.md) Consulte os tutoriais [de descoberta](tutorial-discover-vmware.md) e [avaliação](tutorial-assess-sql.md) para começar.
- A migração de VMware sem agente suporta agora a replicação simultânea de 500 VMs por vCenter.
- A Azure Migrate instala agora automaticamente o agente Azure VM nos VMware VMs enquanto os migra para Azure utilizando o método sem agente da migração de VMware.

## <a name="update-january-2021"></a>Atualização (janeiro de 2021)
-  Azure Migrate: A ferramenta de migração do servidor permite-lhe agora migrar máquinas virtuais VMware, servidores físicos e máquinas virtuais de outras nuvens para máquinas virtuais Azure com discos encriptados com encriptação do lado do servidor com teclas geridas pelo cliente (CMK).

## <a name="update-december-2020"></a>Atualização (dezembro de 2020)
- A Azure Migrate instala agora automaticamente o agente Azure VM nos VMware VMs enquanto os migra para Azure utilizando o método sem agente da migração de VMware.
- A migração de VMware VMs para máquinas virtuais Azure com discos encriptados utilizando encriptação do lado do servidor (SSE) com teclas geridas pelo cliente (CMK), utilizando a migração do servidor Azure Migrate Server (replicação sem agente) está agora disponível através do portal Azure.

## <a name="update-september-2020"></a>Atualização (setembro 2020)
- A migração de servidores para Zonas de Disponibilidade é agora suportada.
- A migração de VMs e servidores físicos baseados na UEFI para Azure geração 2 VMs é agora suportada. Com esta versão, a ferramenta Azure Migrate: Server Migration não realizará a conversão da Gen 2 VM para a Gen 1 VM durante a migração.
- Um novo painel de avaliação do Azure Migrate Power BI está disponível para ajudá-lo a comparar custos em diferentes definições de avaliação. O dashboard vem com um utilitário PowerShell que cria automaticamente as avaliações que se ligam ao painel Power BI. [Saiba mais.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- A análise da dependência (sem agente) pode agora ser executada simultaneamente em 1000 VMs.
- A análise de dependência (sem agente) pode agora ser ativada ou desativada em escala utilizando scripts PowerShell. [Saiba mais.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Visualize as ligações de rede no Power BI utilizando os dados recolhidos através da análise de dependência (sem agente) [Saiba mais.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- A migração de VMware VMs com tamanho de disco de dados até 32 TB é agora suportada usando o método de migração de VMware sem agente do servidor.

## <a name="update-august-2020"></a>Atualização (agosto 2020)

- Melhor experiência de embarque onde uma chave de projeto Azure Migrate é gerada a partir do portal e é usada para completar o registo do aparelho.
- Opção para descarregar os ficheiros OVA/VHD ou os scripts do instalador do portal para configurar os aparelhos VMware e Hyper-V, respectivamente.
- Gestor de configuração de aparelhos atualizado com uma experiência melhorada do utilizador.
- Suporte de múltiplas credenciais para a descoberta de Hiper-VMs.

## <a name="update-july-2020"></a>Atualização (julho 2020)

- A migração de VMware sem agente suporta agora a replicação simultânea de 300 VMs por vCenter

## <a name="update-june-2020"></a>Atualização (junho de 2020)

- As avaliações para migração de VMware VMs para [Azure VMware Solution (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) são agora suportadas. [Saiba mais](how-to-create-azure-vmware-solution-assessment.md)
- Suporte para múltiplas credenciais no aparelho para a descoberta do servidor físico.
- Suporte para permitir o login do Azure a partir de um aparelho para inquilino onde a restrição do inquilino tenha sido configurada.


## <a name="update-april-2020"></a>Atualização (abril de 2020)

Azure Migrate apoia destacamentos no Governo de Azure. 

- Pode descobrir e avaliar VMware VMs, Hiper-VMs e servidores físicos.
- Pode migrar VMware VMs, Hiper-VMs e servidores físicos para Azure.
- Para a migração VMware, você pode usar migração sem agente ou baseada em agentes. [Saiba mais](server-migrate-overview.md).
- [Reveja](migrate-support-matrix.md#supported-geographies-azure-government) geografias e regiões apoiadas para o Governo de Azure.
- [A análise da dependência baseada em agentes](concepts-dependency-visualization.md#agent-based-analysis) não é apoiada no Governo de Azure.
- As funcionalidades de pré-visualização são suportadas no Governo de Azure, especificamente [na análise de dependência sem agentes](concepts-dependency-visualization.md#agentless-analysis)e na descoberta de [aplicações.](how-to-discover-applications.md)


## <a name="update-march-2020"></a>Atualização (março 2020)

Uma instalação baseada em scripts está agora disponível para configurar o [aparelho Azure Migrate](migrate-appliance.md):

- A instalação baseada em scripts é uma alternativa à . Instalação do aparelho OVA (VMware)/VHD (Hiper-V).
- Fornece um script de instalador PowerShell que pode ser usado para configurar o aparelho para VMware/Hyper-V numa máquina existente que executa o Windows Server 2016.

## <a name="update-november-2019"></a>Atualização (novembro 2019)

Foram adicionadas várias novidades à Azure Migrate:

- **Avaliação física do servidor**. A avaliação dos servidores físicos no local é agora suportada, além da migração física do servidor que já está suportada.
- **Avaliação baseada nas importações.** A avaliação das máquinas utilizando metadados e dados de desempenho fornecidos num ficheiro CSV é agora suportada.
- **Descoberta de aplicações**: A Azure Migrate suporta agora a descoberta ao nível da aplicação de aplicações, funções e funcionalidades que utilizam o aparelho Azure Migrate. Isto é atualmente suportado apenas para VMware VMs, e está limitado apenas à descoberta (a avaliação não é suportada atualmente). [Saiba mais](how-to-discover-applications.md)
- **Visualização de dependência de agente**: Já não é necessário instalar explicitamente agentes para visualização de dependência. Tanto os agentes como os agentes são agora apoiados.
- **Ambiente de Trabalho Virtual**: Utilize ferramentas ISV para avaliar e migrar no local infraestruturas de ambiente de trabalho virtuais (VDI) para o Windows Virtual Desktop em Azure.
- **Aplicação Web**: O Assistente de Migração do Serviço de Aplicações Azure, utilizado para avaliar e migrar aplicações web, está agora integrado no Azure Migrate.

Foram adicionados novos instrumentos de avaliação e migração à Azure Migrate:

- **RackWare**: Oferecendo migração em nuvem.
- **Movere**: Avaliação de oferta.

[Saiba mais](migrate-services-overview.md) sobre a utilização de ferramentas e ofertas isv para avaliação e migração em Azure Migrate.

## <a name="azure-migrate-current-version"></a>Versão atual do Azure Migrate

A versão atual do Azure Migrate (lançado em julho de 2019) fornece uma série de novas funcionalidades:

- **Plataforma de migração unificada**: A Azure Migrate oferece agora um único portal para centralizar, gerir e acompanhar a sua viagem de migração para Azure, com um melhor fluxo de implementação e experiência no portal.
- **Ferramentas de avaliação e migração**: A Azure Migrate fornece ferramentas nativas e integra-se com outros serviços Azure, bem como com ferramentas independentes de fornecedor de software (ISV). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração do ISV.
- **Avaliação de Azure Migrate**: Utilizando a ferramenta de avaliação do servidor Azure Migrate, pode avaliar VMware VMs e VMs hiper-V para migração para Azure. Você também pode avaliar para migração usando outros serviços Azure, e ferramentas ISV.
- **Migração Azure Migrate**: Utilizando a ferramenta de migração do servidor Azure Migrate, pode migrar no local VMware VMs e VMs hiper-V para Azure, bem como servidores físicos, outros servidores virtualizados e VMs de nuvem privada/pública. Além disso, pode migrar para Azure usando ferramentas ISV.
- **Aparelho Azure Migrate**: A Azure Migrate implanta um aparelho leve para a descoberta e avaliação de VMware VMware e VMs hiper-V no local.
    - Este aparelho é utilizado pela Azure Migrate Server Assessment e pela Migração do Servidor Azure Migrate para migração sem agentes.
    - O aparelho descobre continuamente metadados de servidor e dados de desempenho, para efeitos de avaliação e migração.  
- **VMware VM migração**: A migração do servidor Azure Migrate server fornece um par de métodos para migrar VMware VMs para Azure no local.  Uma migração sem agentes utilizando o aparelho Azure Migrate e uma migração baseada em agentes que utiliza um aparelho de replicação e implanta um agente em cada VM que pretende migrar. [Saiba mais](server-migrate-overview.md)
 - **Avaliação da base de dados e migração**: A partir de Azure Migrate, pode avaliar bases de dados no local para migração para Azure utilizando o Assistente de Migração da Base de Dados de Azure. Pode migrar bases de dados utilizando o Serviço de Migração da Base de Dados Azure.
- **Migração de aplicativos web**: Você pode avaliar aplicações web usando um URL de ponto final público com o Azure App Service. Para a migração de aplicações internas .NET, pode descarregar e executar o Assistente de Migração do Serviço de Aplicações.
- **Caixa de Dados**: Importe grandes quantidades de dados offline para Azure usando Azure Data Box em Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Versão anterior do Azure Migrate

Se estiver a utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMS VMware no local), deverá agora utilizar a versão atual. Na versão anterior, já não é possível criar novos projetos Azure Migrate, nem realizar novas descobertas. Ainda pode aceder a projetos existentes. Para isso no portal Azure > **Todos os serviços**, procure **a Azure Migrate.** Nas notificações do Azure Migrate, há um link para aceder a antigos projetos da Azure Migrate.



## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
- Experimente os nossos tutoriais para avaliar [VMware VMs](./tutorial-assess-vmware-azure-vm.md) e [VMs Hiper-V.](tutorial-assess-hyper-v.md)