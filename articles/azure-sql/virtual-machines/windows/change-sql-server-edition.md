---
title: Mudança no lugar da edição do SQL Server
description: Saiba como alterar a edição da sua máquina virtual SQL Server em Azure para reduzir custos ou upgrades para ativar mais funcionalidades.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 06587d80851839188f8d8dcfa870d331e8fa96d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293361"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Mudança no lugar da edição do SQL Server no Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como alterar a edição do SQL Server numa máquina virtual Windows em Azure. 

A edição do SQL Server é determinada pela chave do produto, e é especificada durante o processo de instalação utilizando o suporte de instalação. A edição dita quais [as funcionalidades](/sql/sql-server/editions-and-components-of-sql-server-2017) disponíveis no produto SQL Server. Pode alterar a edição do SQL Server com os meios de instalação e reduzir custos ou upgrades para permitir mais funcionalidades.

Uma vez que a edição do SQL Server tenha sido alterada internamente para o SQL Server VM, deve então atualizar a propriedade de edição do SQL Server no portal Azure para efeitos de faturação. 

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração no local da edição do SQL Server, precisa do seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um [SQL Server VM no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registado no fornecedor de recursos [SQL VM](sql-vm-resource-provider-register.md).
- Configurar os meios de comunicação com a **edição desejada** do SQL Server. Os clientes que tiverem o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) poderão obter o suporte de dados de instalação no [Centro de Licenciamento em Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm Software Assurance podem utilizar os meios de configuração a partir de uma imagem VM do Azure Marketplace SQL Server que tem a sua edição desejada (normalmente localizada em `C:\SQLServerFull` ). 


## <a name="upgrade-an-edition"></a>Atualizar uma edição

> [!WARNING]
> A atualização da edição do SQL Server reiniciará o serviço para o SQL Server, juntamente com quaisquer serviços associados, tais como Serviços de Análise e Serviços R. 

Para atualizar a edição do SQL Server, obtenha o meio de configuração do SQL Server para a edição desejada do SQL Server e, em seguida, faça o seguinte:

1. Abra Setup.exe a partir dos meios de instalação do SQL Server. 
1. Vá à **Manutenção** e escolha a opção **Edição Upgrade.** 

   ![Seleção para atualização da edição do SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Selecione **Next** até chegar à página **de edição de upgrade pronto** e, em seguida, selecione **Upgrade**. A janela de configuração pode parar de responder durante alguns minutos enquanto a mudança está a fazer efeito. Uma página **Completa** confirmará que a sua atualização de edição está terminada. 

Depois de atualizada a edição do SQL Server, modifique a propriedade de edição da máquina virtual SQL Server no portal Azure. Isto irá atualizar os metadados e a faturação associados a este VM.

## <a name="downgrade-an-edition"></a>Downgrade uma edição

Para desclassificar a edição do SQL Server, é necessário desinstalar completamente o SQL Server e reinstalá-lo novamente com os meios de configuração de edição pretendidos. 

> [!WARNING]
> Desinstalar o SQL Server pode incorrer em tempo de inatividade adicional. 

Pode desclassificar a edição do SQL Server seguindo estes passos:

1. Ressou todas as bases de dados, incluindo as bases de dados do sistema. 
1. Mover bases de dados do sistema (master, modelo e msdb) para um novo local. 
1. Desinstale completamente o SQL Server e todos os serviços associados. 
1. Reinicie a máquina virtual. 
1. Instale o SQL Server utilizando os meios de comunicação com a edição desejada do SQL Server.
1. Instale os mais recentes pacotes de serviços e atualizações cumulativas.  
1. Substitua as novas bases de dados do sistema que foram criadas durante a instalação pelas bases de dados do sistema que anteriormente mudou para um local diferente. 

Depois de desclassificada a edição do SQL Server, modifique a propriedade de edição da máquina virtual SQL Server no portal Azure. Isto irá atualizar os metadados e a faturação associados a este VM.

## <a name="change-edition-in-portal"></a>Alterar edição no portal 

Depois de ter alterado a edição do SQL Server utilizando os meios de instalação, e tiver registado o seu SQL Server VM com o [fornecedor de recursos SQL VM,](sql-vm-resource-provider-register.md)pode então utilizar o portal Azure para modificar a propriedade Edition do SQL Server VM para efeitos de faturação. Para o fazer, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Aceda ao recurso de máquina virtual SQL Server. 
1. Em **Definições**, selecione **Configurar**. Em seguida, selecione a edição desejada do SQL Server a partir da lista de drop-down no âmbito **da Edição**. 

   ![Alterar metadados de edição](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Reveja o aviso que diz que deve alterar primeiro a edição do SQL Server e que a propriedade da edição deve corresponder à edição do SQL Server. 
1. Selecione **Aplicar** para aplicar as alterações de metadados da sua edição. 


## <a name="remarks"></a>Observações

- A propriedade de edição para o SQL Server VM deve coincidir com a edição da instância SQL Server instalada para todas as máquinas virtuais do SQL Server, incluindo os tipos de licenças pay-as-you-go e bring-your-your-own-license.
- Se deixar cair o seu recurso VM do SQL Server, voltará à definição de edição codificada da imagem.
- A capacidade de alterar a edição é uma característica do fornecedor de recursos SQL VM. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um SQL Server VM com o fornecedor de recursos. No entanto, os clientes que se instalam automaticamente no SQL Server terão de registar manualmente [o seu SQL Server VM](sql-vm-resource-provider-register.md).
- Adicionar um SQL Server VM a um conjunto de disponibilidade requer recriar o VM. Quaisquer VMs adicionados a um conjunto de disponibilidade voltarão à edição padrão, e a edição terá de ser modificada novamente.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](doc-changes-updates-release-notes.md)


