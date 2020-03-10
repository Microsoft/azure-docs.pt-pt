---
title: Configure contas de laboratório nos Serviços de Laboratório Azure  Microsoft Docs
description: Este artigo descreve como criar uma conta de laboratório, ver todas as contas de laboratório ou apagar uma conta de laboratório nos Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361006"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configure contas de laboratório em Serviços de Laboratório Azure 
Nos Serviços de Laboratório Azure, uma conta de laboratório é um recipiente para tipos de laboratório geridos, como laboratórios de sala de aula. Um administrador cria uma conta de laboratório com os Serviços de Laboratório Azure e dá acesso aos donos de laboratório que podem criar laboratórios na conta. 

Este artigo mostra como executar as seguintes tarefas: 

- Especifique um intervalo de endereços para VMs no laboratório
- Configure o encerramento automático de VMs na desconexão

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especifique um intervalo de endereços para VMs no laboratório
O procedimento seguinte tem medidas para especificar um intervalo de endereços para VMs no laboratório. Se atualizar a gama que especificou anteriormente, a gama de endereços modificada aplica-se apenas aos VMs que são criados após a alteração. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual for espreitada para a conta de laboratório, o intervalo de endereços fornecido não pode sobrepor-se ao intervalo de endereços da rede virtual peered.

1. Na página **da Conta lab,** selecione **as definições** de Laboratórios no menu esquerdo.
2. Para o campo **de intervalo de endereços,** especifique o intervalo de endereços para VMs que serão criados em laboratório. A gama de endereços deve constar da notação de encaminhamento inter-domínio sem classe (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesta gama de endereços.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Configure gama de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Paragem automática de VMs na desconexão
Pode ativar ou desativar o encerramento automático de VMs de laboratório do Windows (modelo ou aluno) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os VMs devem esperar que o utilizador se reconecte antes de desligar automaticamente.

![Definição automática de paragem na conta de laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Esta definição aplica-se a todos os laboratórios criados na conta do laboratório. Um dono de laboratório pode anular este cenário ao nível do laboratório. A alteração desta configuração na conta do laboratório só afetará os laboratórios que são criados após a mudança.

Para saber como um dono de laboratório pode configurar este cenário a nível de laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Ligue a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Ver configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
