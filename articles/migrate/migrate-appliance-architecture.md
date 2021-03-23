---
title: Arquitetura de aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na descoberta, avaliação e migração do servidor.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785245"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura de aplicação do Azure Migrate

Este artigo descreve a arquitetura e processos do aparelho Azure Migrate. O aparelho Azure Migrate é um aparelho leve que é implantado no local, para descobrir servidores para migração para Azure.

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Costumava**
--- | --- | ---
**Descoberta e avaliação de servidores em execução em ambiente VMware** | Azure Migrate: Descoberta e avaliação | Descubra servidores em execução no seu ambiente VMware<br/><br/> Realize a descoberta do inventário de software instalado, análise de dependência de agentes e descubra as instâncias e bases de dados do SQL Server.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.
**Migração sem agente de servidores em execução em ambiente VMware** | Azure Migrate:Migração de servidores | Descubra os servidores em execução no seu ambiente VMware.<br/><br/> Replicar servidores sem instalar quaisquer agentes neles.
**Descoberta e avaliação de servidores em execução em ambiente Hiper-V** | Azure Migrate: Descoberta e avaliação | Descubra servidores em execução no seu ambiente Hiper-V.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.
**Descoberta e avaliação de servidores físicos ou virtualizados no local** |  Azure Migrate: Descoberta e avaliação |  Descubra servidores físicos ou virtualizados no local.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O aparelho pode ser implantado utilizando um par de métodos:

- O aparelho pode ser implantado utilizando um modelo para servidores em execução em ambiente VMware ou Hiper-V[(modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hiper-V](how-to-set-up-appliance-hyper-v.md)).
- Se não quiser utilizar um modelo, pode implantar o aparelho para ambiente VMware ou Hiper-V utilizando um [script instalador PowerShell](deploy-appliance-script.md).
- No Governo Azure, deve utilizar o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](deploy-appliance-script-government.md)
- Para servidores físicos ou virtualizados no local ou em qualquer outra nuvem, utilize sempre o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](how-to-set-up-appliance-physical.md)
- Os links de descarregamento estão disponíveis nas tabelas abaixo.

## <a name="appliance-services"></a>Serviços de eletrodomés

O aparelho dispõe dos seguintes serviços:

- **Gestor de configuração do aparelho**: Esta é uma aplicação web que pode ser configurada com detalhes de origem para iniciar a descoberta e avaliação dos servidores. 
- **Agente de** descoberta : O agente recolhe metadados de configuração do servidor que podem ser usados para criar como avaliações no local.
- **Agente de avaliação**: O agente recolhe metadados de desempenho do servidor que podem ser utilizados para criar avaliações baseadas no desempenho.
- **Serviço de atualização automática**: O serviço mantém todos os agentes a trabalhar no aparelho atualizados. Funciona automaticamente uma vez a cada 24 horas.
- **Agente DRA**: Orquestra a replicação do servidor e coordena a comunicação entre servidores replicados e Azure. Usado apenas para replicar servidores para Azure usando migração sem agente.
- **Gateway**: Envia dados replicados para Azure. Usado apenas para replicar servidores para Azure usando migração sem agente.
- **SQL discovery and assessment agent**: envia os metadados de configuração e desempenho de instâncias e bases de dados do SQL Server para O Azure.

> [!Note]
> Os últimos 3 serviços só estão disponíveis no aparelho utilizado para a descoberta e avaliação dos servidores em execução no seu ambiente VMware.<br/> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **Leste da Austrália**. Se já tiver um projeto no Leste da Austrália e quiser experimentar esta funcionalidade, verifique se concluiu estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

## <a name="discovery-and-collection-process"></a>Processo de descoberta e recolha

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Arquitetura de eletrodoméstico":::

O aparelho comunica com as fontes de descoberta utilizando o seguinte processo.

**Processo** | **Aparelho VMware** | **Aparelho Hiper-V** | **Aparelho físico**
---|---|---|---
**Iniciar a descoberta** | O aparelho comunica com o servidor vCenter na porta TCP 443 por predefinição. Se o servidor vCenter ouvir uma porta diferente, pode configurá-la no gestor de configuração do aparelho. | O aparelho comunica com os anfitriões Hyper-V na porta WinRM 5985 (HTTP). | O aparelho comunica com servidores Windows sobre a porta WinRM 5985 (HTTP) com servidores Linux sobre a porta 22 (TCP).
**Recolha metadados de configuração e desempenho** | O aparelho recolhe os metadados dos servidores em execução no vCenter Server utilizando APIs vSphere ligando-os na porta 443 (porta predefinido) ou em qualquer outra porta que o servidor vCenter da porta ou seja ouvido. | O aparelho recolhe os metadados dos servidores em execução em anfitriões Hiper-V utilizando uma sessão de Modelo de Informação Comum (CIM) com anfitriões na porta 5985.| O aparelho recolhe metadados de servidores windows utilizando a sessão Common Information Model (CIM) com servidores na porta 5985 e a partir de servidores Linux utilizando conectividade SSH na porta 22.
**Enviar dados de descoberta** | O aparelho envia os dados recolhidos para Azure Migrate: Discovery and assessment e Azure Migrate: Server Migration over SSL port 443.<br/><br/> O aparelho pode ligar-se ao Azure através da internet ou via ExpressRoute (requer o olhar da Microsoft). | O aparelho envia os dados recolhidos para a Azure Migrate: Descoberta e avaliação sobre a porta SSL 443.<br/><br/> O aparelho pode ligar-se ao Azure através da internet ou via ExpressRoute (requer o olhar da Microsoft).| O aparelho envia os dados recolhidos para a Azure Migrate: Descoberta e avaliação sobre a porta SSL 443.<br/><br/> O aparelho pode ligar-se ao Azure através da internet ou via ExpressRoute (requer o olhar da Microsoft).
**Frequência de recolha de dados** | Os metadados de configuração são recolhidos e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são recolhidos a cada 20 segundos e são agregados para enviar um ponto de dados para Azure a cada 10 minutos. <br/><br/> Os dados de inventário de software são enviados para a Azure uma vez a cada 12 horas. <br/><br/> Os dados de dependência sem agente são recolhidos a cada 5 minutos, agregados em aparelhos e enviados para Azure a cada 6 horas. <br/><br/> Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos.| Os metadados de configuração são recolhidos e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são recolhidos a cada 30 segundos e são agregados para enviar um ponto de dados para Azure a cada 10 minutos.|  Os metadados de configuração são recolhidos e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são recolhidos a cada 5 minutos e são agregados para enviar um ponto de dados para Azure a cada 10 minutos.
**Avaliar e migrar** | Pode criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a ferramenta Azure Migrate: Discovery and assessment.<br/><br/>Além disso, também pode começar a migrar servidores em execução no seu ambiente VMware utilizando a ferramenta Azure Migrate: Server Migration para orquestrar a replicação do servidor sem agente.| Pode criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a ferramenta Azure Migrate: Discovery and assessment. | Pode criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a ferramenta Azure Migrate: Discovery and assessment.

## <a name="next-steps"></a>Passos seguintes

[Reveja](migrate-appliance.md) a matriz de suporte do aparelho.