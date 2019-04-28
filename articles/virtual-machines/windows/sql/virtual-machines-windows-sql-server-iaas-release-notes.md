---
title: SQL Server nas notas de versão VM do Azure | Documentos da Microsoft
description: Saiba mais sobre os novos recursos e aprimoramentos do SQL Server numa VM do Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: 23e072369aa8ac6ca6ada5ec185df1a8d7e03c5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477183"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server nas notas de versão da Máquina Virtual do Azure

Azure permite-lhe implementar uma máquina virtual com uma imagem do SQL Server incorporado. Este artigo resume as novas funcionalidades e melhorias nas versões recentes do [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). O artigo também apresenta uma lista de atualizações de conteúdo relevantes não diretamente relacionadas com o lançamento, mas publicadas no mesmo intervalo de tempo. Para aprimoramentos a outros serviços do Azure, consulte [as atualizações de serviço](https://azure.microsoft.com/updates)

## <a name="march-2019"></a>Março de 2019

### <a name="service-improvements"></a>Melhorias de serviço

| Melhorias de serviço | Detalhes |
| --- | --- |
| **Capacidade de suporte de imagem personalizada** | Agora pode instalar o [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) para imagens de sistema operacional e SQL personalizadas, que oferece a funcionalidade limitada do [licenciamento flexível](virtual-machines-windows-sql-ahb.md). Quando registar a sua imagem personalizada com o fornecedor de recursos do SQL, especifique o tipo de licença como 'AHUB' como caso contrário, o registo falhará.  | 
| **Capacidade de suporte de instância com nome** | Agora pode utilizar o [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) com uma instância nomeada, se a instância predefinida tiver sido desinstalada corretamente. | 
| **Aprimoramento de portal** | A experiência do portal do Azure para implementar uma VM do SQL Server foi renovada para melhorar a usabilidade. Para obter mais informações, consulte o breve [início rápido](quickstart-sql-vm-create-portal.md) e mais completa [procedimentos](virtual-machines-windows-portal-sql-server-provision.md) guia para implantar uma VM do SQL Server. |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Melhorias de documentação

Nenhuma


## <a name="february-2019"></a>Fevereiro de 2019

### <a name="service-improvements"></a>Melhorias de serviço

| Melhorias de serviço | Detalhes |
| --- | --- |
| **Melhoria do portal** | Agora, é possível alterar o modelo de licenciamento de uma VM do SQL Server de pay as you go para bring-your-own-license utilizando o [portal do Azure](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Simplificação de implementação de AG com a CLI de VM de SQL do Azure** | Agora é mais fácil do que nunca para implementar um grupo de disponibilidade para uma VM do SQL Server no Azure. [CLI da VM do SQL do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) permite-lhe criar o serviço de escuta do WSFC, o ILB e AG, tudo a partir da linha de comandos e, em tempo Recorde! Para obter mais informações, consulte [utilização CLI na VM de SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Dezembro de 2018

| Melhorias de serviço | Detalhes |
| --- | --- |
| **Novo fornecedor de recursos do grupo de cluster do SQL** | Um novo fornecedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) que define os metadados do Cluster de ativação pós-falha do Windows. Associar uma VM do SQL Server para o *SqlVirtualMachineGroups* inicializa o serviço de Cluster de ativação pós-falha do Windows e associa a VM ao cluster.  |
|**Automatizar a configuração de uma implantação de grupo de disponibilidade com modelos de início rápido do Azure** |Agora é possível criar o Cluster de ativação pós-falha do Windows, Junte-se a VMs do SQL Server para o mesmo, criar o serviço de escuta e configurar o Balanceador de carga interno com dois modelos de início rápido do Azure. Para obter mais informações, consulte [modelo de início rápido do Azure de utilização para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registo do fornecedor de recursos de VM do SQL automática** | VMs do SQL Server implementado depois este mês são automaticamente registrados com o novo fornecedor de recursos do SQL Server. VMs do SQL Server implementadas antes deste mês ainda têm de ser manualmente registados. Para obter mais informações, consulte [registar a VM de SQL existente com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembro de 2018

| Melhorias de serviço | Detalhes |
| --- | --- |
| **Fornecedor de recursos de VM do SQL nova** |  Um fornecedor de recursos novo para VMs do SQL Server (Microsoft.SqlVirtualMachine) que fornece um melhor gerenciamento de sua VM do SQL Server. Para obter mais informações sobre a registar a sua VM, consulte [registar a VM de SQL existente com o novo fornecedor de recursos](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Mudar o modelo de licenciamento** |Agora pode alternar entre o modelo de pagamento por utilização e a trazer a sua própria licença para a VM do SQL através da CLI do Azure ou do Powershell. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprovisionar um Windows do servidor SQL VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Elevada disponibilidade e recuperação após desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs do Linux**:

* [Descrição geral do SQL Server numa VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionar uma Máquina Virtual do SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
