---
title: Arquitetura de aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: d38796d4c4a1149d096f5bb06f7a11bc71b33cc5
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428158"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura de aplicação do Azure Migrate

Este artigo descreve a arquitetura e processos do aparelho Azure Migrate. O aparelho Azure Migrate é um aparelho leve que é implantado no local, para descobrir VMs e servidores físicos para migração para Azure. 

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Utilizado para** 
--- | --- | ---
**Avaliação VMware VM** | Azure Migrate:Avaliação do servidor | Descubra VMware VMs.<br/><br/> Descubra aplicativos e dependências de máquinas.<br/><br/> Recolha metadados de máquina e metadados de desempenho e envie para a Azure.
**VMware VM migração (sem agente)** | Azure Migrate:Migração de servidores | Detetar VMs VMware<br/><br/>  Replicar VMware VMs com [migração sem agentes](server-migrate-overview.md).
**Avaliação de VM hiper-V** | Azure Migrate:Avaliação do servidor | Descubra os VMs Hiper-V.<br/><br/> Recolha metadados de máquina e metadados de desempenho e envie para a Azure.
**Máquina física** |  Azure Migrate:Avaliação do servidor |  Descubra servidores físicos.<br/><br/> Recolha metadados de máquina e metadados de desempenho e envie para a Azure.

## <a name="appliance-components"></a>Componentes do aparelho

O aparelho tem vários componentes.

- **Aplicação de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.
- **Agente de**descoberta: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure. 
- **Agente de recolha**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.
- **Agente DRA**: Orquestra a replicação VM e coordena a comunicação entre máquinas replicadas e Azure. Utilizado apenas na replicação de VMware VMs para Azure usando migração sem agente.
- **Gateway**: Envia dados replicados para Azure. Utilizado apenas na replicação de VMware VMs para Azure usando migração sem agente.
- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).



## <a name="appliance-deployment"></a>Implantação do aparelho

- O aparelho Azure Migrate pode ser configurado utilizando um modelo para [Hiper-V](how-to-set-up-appliance-hyper-v.md) ou [VMware](how-to-set-up-appliance-vmware.md) ou utilizando um instalador de scripts PowerShell para [VMware/Hyper-V](deploy-appliance-script.md), e para [servidores físicos](how-to-set-up-appliance-physical.md). 
- Os requisitos de suporte do aparelho e os pré-requisitos de implantação são resumidos na [matriz de suporte](migrate-appliance.md)do aparelho .


## <a name="appliance-registration"></a>Registo do aparelho

Durante a instalação do aparelho, registe o aparelho com o Azure Migrate e ocorrem as ações resumidas na tabela.

**Ação** | **Detalhes** | **Permissões**
--- | --- | ---
**Fornecedores de fontes de registo** | Estes fornecedores de recursos estão registados na subscrição que escolher durante a configuração do aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault.<br/><br/> O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. | Para registar os fornecedores de recursos, precisa de uma função de Contribuinte ou Proprietário na subscrição.
**Criar comunicação de aplicativos AZure AD** | A Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho e os respetivos serviços em execução no Azure.<br/><br/> Esta aplicação não tem privilégios para fazer chamadas do Azure Resource Manager, ou acesso Azure RBAC em qualquer recurso. | Precisa [destas permissões](./tutorial-discover-vmware.md#prepare-an-azure-user-account) para que o Azure Migrate crie a app.
**Criar ad apps AD Azure** | Esta aplicação é criada apenas para migração sem agentes de VMware VMs para Azure.<br/><br/> É usado exclusivamente para aceder ao cofre-chave criado na subscrição do utilizador para migração sem agentes.<br/><br/> Tem acesso Azure RBAC no cofre de chaves Azure (criado no inquilino do cliente), quando a descoberta é iniciada a partir do aparelho. | Precisa [destas permissões](./tutorial-discover-vmware.md#prepare-an-azure-user-account) para que o Azure Migrate crie a app.



## <a name="collected-data"></a>Dados recolhidos

Os dados recolhidos pelo cliente para todos os cenários de implantação são resumidos na [matriz de suporte](migrate-appliance.md)do aparelho .

## <a name="discovery-and-collection-process"></a>Processo de descoberta e recolha

![Arquitetura](./media/migrate-appliance-architecture/architecture1.png)

O aparelho comunica com servidores/agrupamentos vCenter e hiper-V utilizando o seguinte processo.

1. **Iniciar a descoberta:**
    - Quando inicia a descoberta no aparelho Hyper-V, comunica-se com os anfitriões Hiper-V na porta WinRM 5985 (HTTP).
    - Quando começa a ser descoberto no aparelho VMware, comunica-se com o servidor vCenter na porta 443 da TCP por defeito. Se o servidor vCenter ouvir numa porta diferente, pode configurá-la na aplicação web do aparelho.
2. **Recolha metadados e dados de desempenho:**
    - O aparelho utiliza uma sessão de Modelo de Informação Comum (CIM) para recolher dados de VM hiper-V do hospedeiro Hyper-V na porta 5985.
    - O aparelho comunica com a porta 443 por predefinição, para recolher dados VMware VM do servidor vCenter.
3. **Enviar dados**: O aparelho envia os dados recolhidos para a Avaliação do Servidor Azure Migrate e para a Migração do Servidor Azure Migrar sobre a porta SSL 443. O aparelho pode ligar-se ao Azure através da internet ou via ExpressRoute (requer o olhar da Microsoft).
    - Para os dados de desempenho, o aparelho recolhe dados de utilização em tempo real.
        - Os dados de desempenho são recolhidos a cada 20 segundos para VMware, e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados recolhidos são recolhidos para criar um único ponto de dados durante 10 minutos.
        - O valor máximo de utilização é selecionado a partir de todos os pontos de dados de 20/30 segundos, e enviado ao Azure para avaliação.
        - Com base no valor percentil especificado nas propriedades de avaliação (50º/90º/95º/99º), os dez minutos são classificados em ordem ascendente, e o valor percentil apropriado é usado para calcular a avaliação
    - Para a migração do servidor, o aparelho começa a recolher dados VM e replica-os ao Azure.
4. **Avaliar e migrar:** Pode agora criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a Avaliação do Servidor Azure Migrate. Além disso, também pode começar a migrar VMware VMs usando Azure Migrate Server Migration para orquestrar a replicação de VM sem agente.

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes Azure Migrate que estão a trabalhar no aparelho são atualizados. Isto acontece automaticamente porque a atualização automática é ativada no aparelho por predefinição. Pode alterar esta definição predefinida para atualizar os agentes manualmente.

Desligue a atualização automática do registo definindo a tecla "AutoUpdate" HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance para 0 (DWORD).


## <a name="next-steps"></a>Passos seguintes

[Reveja](migrate-appliance.md) a matriz de suporte do aparelho.