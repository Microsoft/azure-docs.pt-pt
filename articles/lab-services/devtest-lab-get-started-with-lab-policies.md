---
title: Gerir políticas de laboratório básicas no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como configurar algumas das políticas básicas (definições) para um laboratório no DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773847"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir políticas de básicas para um laboratório no Azure DevTest Labs

Os laboratórios DevTest do Azure permite-lhe controlar o custo e minimize o desperdício em seus laboratórios através da gestão de políticas (definições) para cada um deles. Neste artigo, começar, com as políticas de aprender como a definição de dois das políticas mais críticas – a limitar o número de máquinas virtuais (VM) que podem ser criados ou solicitadas por um único utilizador e a configuração de encerramento automático. Para ver como definir cada política de laboratório, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Aceder a políticas de um laboratório no Azure DevTest Labs
Os seguintes passos guiá-lo por meio de configuração de políticas para um laboratório no Azure DevTest Labs:

Para ver (e alterar) as políticas num laboratório, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.   

1. Selecione **Konfigurace a zásady**.

    ![Painel de definições de política](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. O **Konfigurace a zásady** painel contém um menu de definições que pode especificar. Este artigo abrange apenas as definições para **máquinas virtuais por utilizador**, **encerramento automático**, e **início automático**. Para saber mais sobre as restantes definições, consulte [gerir todas as políticas para um laboratório no Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Máquinas virtuais do conjunto por utilizador
A política para **máquinas virtuais por utilizador** permite-lhe especificar o número máximo de VMs que podem ser criadas por um utilizador individual. Se um utilizador tenta criar ou uma VM de afirmação quando o limite de utilizador tiver sido cumprido, uma mensagem de erro indica que a VM não pode ser criado/solicitadas. 

1. O laboratório **Konfigurace a zásady** menu, selecione **máquinas virtuais por utilizador**.
   
    ![Máquinas virtuais por utilizador](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por utilizador. Se não pretender limitar o número de VMs por utilizador, selecione **não**. Se selecionou **Sim**, introduza um valor numérico que indica o número máximo de VMs que podem ser criadas ou solicitadas por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que pode utilizar o SSD (disco de estado sólido). Se não pretender limitar o número de VMs que pode utilizar o SSD, selecione **não**. Se selecionou **Sim**, introduza um valor que indica o número máximo de VMs que podem ser criadas usando SSD. 

1. Selecione **Guardar**.

## <a name="set-auto-shutdown"></a>Encerramento do conjunto automático
A política de encerramento automático ajuda a minimizar o desperdício de laboratório, permitindo que especifique o tempo que VMs este laboratório são encerradas.

1. O laboratório **Konfigurace a zásady** painel, selecione **encerramento automático**.
   
    ![Encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **nos** para ativar esta política, e **desativar** desabilitá-lo.

1. Se ativar esta política, especifique o tempo (e o fuso horário) para encerrar a todas as VMs do laboratório atual.

1. Especifique **Sim** ou **não** para a opção Enviar uma notificação a 15 minutos antes do tempo de encerramento automático especificado. Se escolher **Sim**, introduza um ponto de final do URL do webhook ou especificando onde pretende que a notificação para ser publicado ou enviados do endereço de e-mail. O utilizador recebe a notificação e é dada a opção para atrasar o encerramento.

   Para obter mais informações sobre os webhooks, consulte [criar um webhook ou uma função de Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selecione **Guardar**.

Por predefinição, uma vez ativada, esta política aplica-se a todas as VMs do laboratório atual. Para remover esta definição de uma VM específica, abra o painel de gestão da VM e altere seu **encerramento automático** definição.

## <a name="set-auto-start"></a>Início do conjunto automático
A política de início automático permite-lhe especificar quando as VMs do laboratório atual devem ser iniciadas.  

1. O laboratório **Konfigurace a zásady** painel, selecione **início automático**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **nos** para ativar esta política, e **desativar** desabilitá-lo.

3. Se ativar esta política, especifique a hora de início agendada, fuso horário e os dias da semana para os quais se aplica a hora. 

4. Selecione **Guardar**.

Uma vez ativada, esta política não é automaticamente aplicada a todas as VMs do laboratório atual. Para aplicar esta definição para uma VM existente, abra o painel de gestão da VM e altere seu **início automático** definição.

## <a name="next-steps"></a>Passos Seguintes

- [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md) -Saiba como modificar outras políticas de laboratório.
