---
title: Mudança no lugar da edição do SQL Server
description: Saiba como alterar a edição da sua máquina virtual SQL Server em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605454"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Mudança no lugar da edição do SQL Server no Azure VM

Este artigo descreve como alterar a edição do SQL Server numa máquina virtual do Windows em Azure. 

A edição do SQL Server é determinada pela chave do produto, e é especificada durante o processo de instalação utilizando os meios de instalação. A edição dita quais as [funcionalidades](/sql/sql-server/editions-and-components-of-sql-server-2017) disponíveis no produto SQL Server. Pode alterar a edição do SQL Server com os meios de instalação e reduzir para reduzir custos ou upgrade satisfaça para permitir mais funcionalidades.

Uma vez que a edição do SQL Server tenha sido alterada internamente para o SQL Server VM, deve então atualizar a propriedade de edição do SQL Server no portal Azure para efeitos de faturação. 

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração na edição do SQL Server, precisa do seguinte: 

- Uma [subscrição Azure.](https://azure.microsoft.com/free/)
- Um VM de [servidor SQL no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registado com o fornecedor de recursos [SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- Configurar meios com a **edição desejada** do SQL Server. Os clientes que possuem [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter os seus meios de instalação a partir do Centro de Licenciamento de [Volume.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Os clientes que não possuem Software Assurance podem utilizar os suportes de configuração a partir de uma `C:\SQLServerFull`imagem VM Do Servidor Azure Marketplace SQL que tem a sua edição desejada (tipicamente localizada). 


## <a name="upgrade-an-edition"></a>Atualizar uma edição

> [!WARNING]
> A atualização da edição do SQL Server reiniciará o serviço para o SQL Server, juntamente com quaisquer serviços associados, como serviços de análise e Serviços de R. 

Para atualizar a edição do SQL Server, obtenha os meios de configuração do SQL Server para a edição desejada do SQL Server e, em seguida, faça o seguinte:

1. Abra configuração.exe a partir dos meios de instalação do Servidor SQL. 
1. Vá à **Manutenção** e escolha a opção Upgrade da **Edição.** 

   ![Seleção para upgrade da edição do SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecione **Seguinte** até chegar à página **Ready to upgrade e,** em seguida, selecione **Upgrade**. A janela de configuração pode parar de responder durante alguns minutos enquanto a mudança está a produzir efeito. Uma página **Completa** confirmará que a atualização da sua edição está concluída. 

Depois da edição do SQL Server ser atualizada, modifique a propriedade da edição da máquina virtual SQL Server no portal Azure. Isto irá atualizar os metadados e faturação associados a este VM.

## <a name="downgrade-an-edition"></a>Downgrade uma edição

Para desvalorizar a edição do SQL Server, é necessário desinstalar completamente o SQL Server e reinstalá-lo novamente com os meios de configuração de edição pretendidos. 

> [!WARNING]
> Desinstalar o Servidor SQL pode incorrer em tempo de inatividade adicional. 

Pode desvalorizar a edição do SQL Server seguindo estes passos:

1. Recue todas as bases de dados, incluindo as bases de dados do sistema. 
1. Mova as bases de dados do sistema (mestre, modelo e msdb) para um novo local. 
1. Desinstale completamente o Servidor SQL e todos os serviços associados. 
1. Reiniciar a máquina virtual. 
1. Instale o Servidor SQL utilizando os meios de comunicação com a edição desejada do Servidor SQL.
1. Instale os mais recentes pacotes de serviços e atualizações cumulativas.  
1. Substitua as novas bases de dados do sistema que foram criadas durante a instalação com as bases de dados do sistema que anteriormente se mudou para um local diferente. 

Depois da edição do SQL Server ser desvalorizada, modifique a propriedade da edição da máquina virtual SQL Server no portal Azure. Isto irá atualizar os metadados e faturação associados a este VM.

## <a name="change-edition-in-portal"></a>Alterar edição no portal 

Depois de ter mudado a edição do SQL Server utilizando os meios de instalação e ter registado o seu VM SQL Server com o fornecedor de [recursos SQL VM,](virtual-machines-windows-sql-register-with-resource-provider.md)pode então utilizar o portal Azure para modificar a propriedade Edition do SQL Server VM para efeitos de faturação. Para o fazer, siga estes passos: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Vá ao seu recurso virtual SQL Server. 
1. Em **definições,** **selecione Configurar**. Em seguida, selecione a sua edição desejada do SQL Server a partir da lista de lançamentos em **Edição**. 

   ![Alterar metadados de edição](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Reveja o aviso que diz que deve alterar primeiro a edição do SQL Server e que a propriedade da edição deve corresponder à edição do SQL Server. 
1. Selecione **Aplicar** para aplicar as alterações de metadados da sua edição. 


## <a name="remarks"></a>Observações

- A propriedade de edição para o SQL Server VM deve corresponder à edição da instância SQL Server instalada para todas as máquinas virtuais do SQL Server, incluindo tanto os tipos de licenças pay-as-you-go e bring-your-your-own-license.
- Se deixar cair o seu recurso VM Do Servidor SQL, voltará à definição de edição codificada da imagem.
- A capacidade de alterar a edição é uma característica do fornecedor de recursos SQL VM. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um VM de Servidor SQL com o fornecedor de recursos. No entanto, os clientes que se auto-instalam no SQL Server terão de registar manualmente o seu VM do [Servidor SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Adicionar um VM de servidor SQL a um conjunto de disponibilidade requer recriar o VM. Quaisquer VMs adicionados a um conjunto de disponibilidade sairá de volta à edição padrão, e a edição terá de ser modificada novamente.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


