---
title: Arquitetura de aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80389023"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura de aplicação do Azure Migrate

Este artigo descreve a arquitetura e processos do eletrodoméstico Azure Migrate. O aparelho Azure Migrate é um aparelho leve que é implantado nas instalações, para descobrir VMs e servidores físicos para migração para Azure. 

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Utilizado para** 
--- | --- | ---
**Avaliação vmware vm** | Azure Migrate:Avaliação do Servidor | Descubra VMs VMware.<br/><br/> Descubra aplicações e dependências de máquinas.<br/><br/> Recolher metadados de máquinas e metadados de desempenho e enviar para o Azure.
**Migração VMware VM (sem agente)** | Migração Azure:Migração do servidor | Descubra VMware VMs<br/><br/>  Replicar VMware VMs com [migração sem agente](server-migrate-overview.md).
**Avaliação de VM hiper-V** | Azure Migrate:Avaliação do Servidor | Descubra os VMs hiper-V.<br/><br/> Recolher metadados de máquinas e metadados de desempenho e enviar para o Azure.
**Máquina física** |  Azure Migrate:Avaliação do Servidor |  Descubra servidores físicos.<br/><br/> Recolher metadados de máquinas e metadados de desempenho e enviar para o Azure.

## <a name="appliance-components"></a>Componentes de aparelhos

O aparelho tem vários componentes.

- **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.
- **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure. 
- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.
- **Agente DRA**: Orquestra a replicação vm e coordena a comunicação entre máquinas replicadas e Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.
- **Gateway**: Envia dados replicados para o Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.
- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).



## <a name="appliance-deployment"></a>Implantação de aparelhos

- O aparelho Azure Migrate pode ser configurado utilizando um modelo para [hiper-V](how-to-set-up-appliance-hyper-v.md) ou [VMware](how-to-set-up-appliance-vmware.md) ou utilizando um instalador de scriptpowerShell para [VMware/Hyper-V,](deploy-appliance-script.md)e para [servidores físicos](how-to-set-up-appliance-physical.md). 
- Os requisitos de apoio ao aparelho e os pré-requisitos de implantação são resumidos na [matriz](migrate-appliance.md)de suporte do aparelho .


## <a name="appliance-registration"></a>Registo de aparelhos

Durante a instalação do aparelho, registe o aparelho com a Migração Azure e ocorrem as ações resumidas na mesa.

**Ação** | **Detalhes** | **Permissões**
--- | --- | ---
**Registar fornecedores de fontes** | Estes fornecedores de recursos estão registados na subscrição que escolher durante a configuração do aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault.<br/><br/> Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. | Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.
**Criar comunicação de aplicações Azure AD** | A Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho e os respetivos serviços em funcionamento no Azure.<br/><br/> Esta aplicação não tem privilégios para fazer chamadas do Gestor de Recursos Do Azure, ou acesso RBAC em qualquer recurso. | Precisa [destas permissões](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) para que o Azure Migrate crie a app.
**Criar abóbada chave de aplicativos Azure AD** | Esta aplicação é criada apenas para migração sem agente de VMware VMs para Azure.<br/><br/> É usado exclusivamente para aceder ao cofre chave criado na subscrição do utilizador para migração sem agente.<br/><br/> Tem acesso RBAC no cofre chave Azure (criado no inquilino do cliente), quando a descoberta é iniciada a partir do aparelho. | Precisa [destas permissões](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) para que o Azure Migrate crie a app.



## <a name="collected-data"></a>Dados recolhidos

Os dados recolhidos pelo cliente para todos os cenários de implantação são resumidos na [matriz](migrate-appliance.md)de suporte do aparelho .

## <a name="discovery-and-collection-process"></a>Processo de descoberta e recolha

![Arquitetura](./media/migrate-appliance-architecture/architecture.png)

O aparelho comunica com os vCenter Servers e os anfitriões/cluster hyper-V utilizando o seguinte processo.

1. **Começar a descoberta:**
    - Quando inicia a descoberta no aparelho Hyper-V, comunica com os anfitriões Hyper-V nas portas WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando começa a ser descoberto no aparelho VMware, comunica-se com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter ouvir uma porta diferente, pode configurá-lo na aplicação web do aparelho.
2. **Recolher metadados e dados de desempenho:**
    - O aparelho utiliza uma sessão do Common Information Model (CIM) para recolher dados de VM Hiper-V do hospedeiro Hyper-V nas portas 5985 e 5986.
    - O aparelho comunica com a porta 443 por defeito, para recolher dados VMware VM do vCenter Server.
3. **Enviar dados**: O aparelho envia os dados recolhidos para a Avaliação do Servidor Migratório De Migração Azure e migração do servidor migratório Azure através da porta SSL 443. O aparelho pode ligar-se ao Azure através da internet, ou pode utilizar o ExpressRoute com o público/microsoft a espreitar.
    - Para obter dados de desempenho, o aparelho recolhe dados de utilização em tempo real.
        - Os dados de desempenho são recolhidos a cada 20 segundos para vMware, e a cada 30 segundos para hyper-V, para cada métrica de desempenho.
        - Os dados recolhidos são enrolados para criar um único ponto de dados durante 10 minutos.
        - O valor máximo de utilização é selecionado de todos os pontos de dados de 20/30 segundos, e enviado para o Azure para cálculo de avaliação.
        - Com base no valor percentil especificado nas propriedades de avaliação (50/90/95/99), os pontos de dez minutos são classificados em ordem ascendente, e o valor de percentil apropriado é usado para calcular a avaliação
    - Para a Migração do Servidor, o aparelho começa a recolher dados vM e replica-os para o Azure.
4. **Avaliar e migrar:** Pode agora criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a Avaliação do Servidor Migratório Azure. Além disso, também pode começar a migrar VMware VMs usando a Migração do Servidor De Migração de Servidores Migradores Azure migrate para orquestrar a replicação vm sem agente.





## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes da Migração Azure que estão a funcionar no aparelho são atualizados. Isto acontece automaticamente porque a atualização automática está ativada no aparelho por defeito. Pode alterar esta definição predefinida para atualizar manualmente os agentes.

Desliga a atualização automática no registo, definindo a tecla HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" para 0 (DWORD).

 

## <a name="next-steps"></a>Passos seguintes

[Reveja](migrate-appliance.md) a matriz de suporte do aparelho.

