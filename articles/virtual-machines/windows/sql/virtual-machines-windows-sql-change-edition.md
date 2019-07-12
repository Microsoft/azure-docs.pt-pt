---
title: Como executar uma atualização in-loco de edição do SQL Server em VM do Azure | Documentos da Microsoft
description: Saiba como alterar a edição da sua VM do SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607549"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Como executar uma atualização in-loco de edição do SQL Server em VM do Azure

Este artigo descreve como alterar a edição do SQL Server para um SQL Server existente numa máquina virtual do Windows no Azure. 

A edição do SQL Server é determinada pela chave do produto e é especificada com o processo de instalação. A edição determina quais [funcionalidades](/sql/sql-server/editions-and-components-of-sql-server-2017) estão disponíveis dentro do produto SQL Server. Pode alterar a edição do SQL Server com a mídia de instalação e de qualquer mudança para versão anterior para reduzir o custo ou atualizar para o ativar mais funcionalidades.

Se atualizou a edição do SQL Server usando a mídia de instalação após o registro com o fornecedor de recursos de VM do SQL Server, em seguida, para atualizar o Azure de faturação da mesma forma que deve definir a propriedade de edição do SQL Server do recurso de VM do SQL Server da seguinte forma:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue para o recurso de máquina virtual do SQL Server. 
1. Sob **configurações**, selecione **configurar** e, em seguida, selecione a sua edição pretendida do SQL Server na lista suspensa em **Edition**. 

   ![Metadados de edição de alteração](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Reveja o aviso notificando-o de que tem de alterar edição do SQL Server pela primeira vez, e essa propriedade edition tem de corresponder à edição do SQL Server. 
1. Selecione **aplicar** para aplicar as alterações de metadados de edição. 


## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração no local da edição do SQL Server, terá de fazer o seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um Windows [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registado com o [fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Configure o suporte de dados com a edição pretendida do SQL Server. Os clientes que tenham [do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) pode obter sua mídia de instalação da [Centro de licenciamento em Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes não tenham o software assurance, pode utilizar o suporte de dados de configuração de uma imagem de VM do SQL Server do marketplace com sua edição pretendido.


## <a name="upgrade-edition"></a>Atualizar a edição

  > [!WARNING]
  > - **A atualizar a edição do SQL Server irá reiniciar o serviço para o SQL Server, bem como quaisquer serviços associados, como o Analysis Services e R Services.** 

Para atualizar a edição do SQL Server, obter o suporte de dados de configuração do SQL Server para a edição pretendida do SQL Server e, em seguida, faça o seguinte:

1. Inicie Setup.exe da mídia de instalação do SQL Server. 
1. Navegue para **manutenção** e escolha o **atualização de edição** opção. 

   ![Atualizar a edição do SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecione **próxima** até chegar a **pronto para atualizar a edição** página e, em seguida, selecione **atualizar**. A janela de configuração pode travar durante alguns minutos enquanto a alteração é entrarem em vigor e, em seguida, verá uma **Complete** página confirmar que a atualização de edição foi concluída. 

Após a atualização de edição do SQL Server, deve modificar a propriedade de "Edition" da Máquina Virtual de Sql no portal do Azure, conforme mostrado acima; Isto irá atualizar os metadados e faturação associados a esta VM.

## <a name="downgrade-edition"></a>Edição de mudança para versão anterior

  > [!WARNING]
  > - **Desatualização de edição do SQL Server necessita de desinstalar completamente o SQL Server, que podem incorrer em períodos de indisponibilidade adicionais**. 


Para mudar a edição do SQL Server, terá de desinstalar completamente do SQL Server e reinstalá-lo novamente com o suporte de dados de configuração de edição pretendido. 

Para mudar a edição do SQL Server através dos seguintes passos:

1. Cópia de segurança de todos os bancos de dados, incluindo as bases de dados do sistema. 
1. Mova bases de dados do sistema (master, o modelo e msdb) para uma nova localização. 
1. Desinstale completamente o SQL Server e associados todos os serviços. 
1. Reinicie a máquina virtual. 
1. Instale o SQL Server com o suporte de dados com a edição pretendida do SQL Server.
1. Instale as mais recentes service packs e atualizações cumulativas.  
1. Substitua o novo sistema bases de dados que foram criados durante a instalação com as bases de dados do sistema que moveu anteriormente para uma localização diferente. 

Assim que a edição do SQL Server está desatualizada, deve modificar a propriedade de "Edition" da máquina virtual SQL no portal do Azure, conforme mostrado acima; Isto irá atualizar os metadados e faturação associados a esta VM.

## <a name="remarks"></a>Observações

 - A propriedade de edição para a VM do SQL Server tem de corresponder a edição do SQL Server instalado para a máquina virtual para todas as máquinas virtuais do SQL incluindo PAYG e BYOL tipos de licença.
 - Se remover o seu recurso de VM do SQL Server, irá voltar para a definição de edição codificada da imagem.
  - A capacidade de alterar a edição é uma funcionalidade do fornecedor de recursos de VM do SQL Server. Implementar uma imagem do marketplace através do portal do Azure automaticamente registra uma VM do SQL Server com o fornecedor de recursos. No entanto, os clientes que Self-estão a instalar o SQL Server serão necessário manualmente [registar a sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- Adicionar uma VM do SQL Server a um conjunto de disponibilidade necessita de recriar a VM. Como tais, qualquer VMs adicionadas a um disponibilidade conjunto serão enviadas de volta para a edição padrão e a edição, terá de ser modificados novamente.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


