---
title: Gerir políticas de laboratório em Azure DevTest Labs Microsoft Docs
description: Aprenda a definir políticas de laboratório como tamanhos de VM, VMs máximos por utilizador e automatização de encerramento.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530357"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir todas as políticas para um laboratório em Azure DevTest Labs

A Azure DevTest Labs permite controlar os custos e minimiza o desperdício nos seus laboratórios gerindo políticas (configurações) para cada laboratório. Este artigo explica em detalhes passo a passo como definir cada política.  

## <a name="set-allowed-virtual-machine-sizes"></a>Conjunto permitido tamanhos de máquina virtual
A política de definição dos tamanhos de VM permitidos ajuda a minimizar os resíduos de laboratório, permitindo-lhe especificar quais os tamanhos de VM permitidos no laboratório. Se esta política for ativada, apenas os tamanhos VM desta lista podem ser utilizados para criar VMs.

1. No [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)selecione um laboratório e, em seguida, **selecione Configuração e políticas**.

    ![Aceda à configuração e políticas do laboratório](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. No painel de **configuração e políticas** do laboratório, selecione **tamanhos de máquinas virtuais permitidos**.
   
    ![Tamanhos permitidos de máquinas virtuais](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecione **On** para ativar esta política e **desligue-a** para desativá-la.

1. Se ativar esta política, selecione um ou mais tamanhos VM que podem ser criados no seu laboratório.

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-user"></a>Definir máquinas virtuais por utilizador
A política **para máquinas virtuais por utilizador** permite especificar o número de VMs que podem ser criados por um utilizador individual. Se um utilizador tentar criar ou reclamar um VM quando o limite do utilizador tiver sido atingido, uma mensagem de erro indica que o VM não pode ser criado/reclamado. 

1. No painel de **configuração e políticas** do laboratório, selecione **máquinas virtuais por utilizador**.
   
    ![Máquinas virtuais por utilizador](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por utilizador. Se não quiser limitar o número de VMs por utilizador, selecione **Nº**. Se selecionar **Sim,** insira um valor numérico indicando o número de VMs que podem ser criados ou reclamados por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que podem usar SSD (disco de estado sólido). Se não quiser limitar o número de VMs que podem utilizar SSD, selecione **Nº**. Se selecionar **Sim,** insira um valor indicando o número de VMs que podem ser criados usando SSD. 

1. Selecione **Guardar**.

## <a name="set-virtual-machines-per-lab"></a>Definir máquinas virtuais por laboratório
A política para **máquinas virtuais por laboratório** permite especificar o número de VMs que podem ser criados para o laboratório atual. Se um utilizador tentar criar um VM quando o limite do laboratório tiver sido atingido, uma mensagem de erro indica que o VM não pode ser criado. 

1. No painel de **configuração e políticas** do laboratório, selecione **máquinas virtuais por laboratório.**
   
    ![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecione **Sim** para limitar o número de VMs por laboratório. Se não quiser limitar o número de VMs por laboratório, selecione **Nº**. Se selecionar **Sim,** insira um valor numérico indicando o número de VMs que podem ser criados ou reclamados por um utilizador. 

1. Selecione **Sim** para limitar o número de VMs que podem usar SSD (disco de estado sólido). Se não quiser limitar o número de VMs que podem utilizar SSD, selecione **Nº**. Se selecionar **Sim,** insira um valor indicando o número de VMs que podem ser criados usando SSD. 

1. Selecione **Guardar**.

## <a name="set-auto-shutdown"></a>Definir encerramento automático
A política de paragem automática ajuda a minimizar os resíduos de laboratório, permitindo-lhe especificar a hora em que os VM deste laboratório desligam.

1. No painel de **configuração e políticas** do laboratório, selecione **Desligação automática**.
   
    ![Encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **On** para ativar esta política e **desligue-a** para desativá-la.

1. Se ativar esta política, especifique a hora (e o fuso horário) para desligar todos os VMs do laboratório atual.

1. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 15 minutos antes do tempo de paragem automático especificado. Se escolher **Sim,** insira um ponto final de URL webhook ou um endereço de e-mail especificando onde deseja que a notificação seja publicada ou enviada. O utilizador recebe a notificação e é-lhe dada a opção de adiar a paralisação.

   Para obter mais informações sobre webhooks, consulte [Criar um webhook ou a API Azure Function](../azure-functions/functions-bindings-http-webhook.md). 

1. Selecione **Guardar**.

Por padrão, uma vez ativado, esta política aplica-se a todos os VMs no laboratório atual. Para remover esta definição de um VM específico, abra o painel de gestão do VM e altere a sua **definição de paragem automática.**

## <a name="set-auto-shutdown-policy"></a>Definir política de encerramento automático
Como dono de laboratório, pode configurar um horário de encerramento para todos os VMs do seu laboratório. Ao fazê-lo, pode poupar custos de funcionamento de máquinas que não estão a ser utilizadas (inativas). Pode impor uma política de encerramento em todos os seus VMs de laboratório centralmente, mas também poupar aos seus utilizadores de laboratório o esforço de configurar um horário para as suas máquinas individuais. Esta funcionalidade permite-lhe definir a política na sua programação de laboratório, começando por não oferecer qualquer controlo ao controlo total, aos utilizadores do laboratório. Como proprietário de laboratório, pode configurar esta política tomando os seguintes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **a política de encerramento automático** na secção **Agendas** do menu esquerdo.
3. Selecione uma das opções. As seguintes secções dão-lhe mais detalhes sobre estas opções: A política definida aplica-se apenas aos novos VM criados em laboratório e não aos VM já existentes. 

    ![Opções de política de encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O utilizador define um horário e pode optar por não o fazer
Se definir o seu laboratório para esta política, os utilizadores do laboratório podem substituir ou optar por não cumprir o horário do laboratório. Esta opção confere aos utilizadores de laboratório o controlo total sobre o horário de paragem automática dos seus VM. Os utilizadores do laboratório não vêem qualquer alteração na sua página de programação de paragem automática VM.

![Opção de política de encerramento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O utilizador define um horário e não pode optar por não o fazer
Se definir o seu laboratório para esta apólice, os utilizadores de laboratório podem anular o horário do laboratório. No entanto, não podem optar por não fazer política de encerramento automático. Esta opção garante que todas as máquinas do seu laboratório estão sob um horário de paragem automática. Os utilizadores de laboratório podem atualizar o horário de paragem automática dos seus VMs e configurar notificações de encerramento.

![Opção de política de encerramento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O utilizador não tem controlo sobre o horário definido pela administração do laboratório
Se definir o seu laboratório para esta política, os utilizadores de laboratório não podem anular ou optar fora do horário do laboratório. Esta opção oferece ao laboratório o controlo completo do horário de todas as máquinas do laboratório. Os utilizadores de laboratório só podem configurar notificações de paragem automática para os seus VMs.

![Opção de política de encerramento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Iniciar automaticamente
A política de arranque automático permite especificar quando os VMs no laboratório atual devem ser iniciados.  

1. No painel de **configuração e políticas** do laboratório, selecione **Autostart**.
   
    ![A captura do ecrã mostra as opções de arranque automáticas para uma máquina virtual.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **On** para ativar esta política e **desligue-a** para desativá-la.

3. Se ativar esta política, especifique a hora de início programada, o fuso horário e os dias da semana para o qual a hora se aplica. 

4. Selecione **Guardar**.

Uma vez ativado, esta política não é aplicada automaticamente a quaisquer VMs no laboratório atual. Para aplicar esta definição a um VM específico, abra o painel de gestão do VM e altere a sua definição **de Arranque Automático.**

## <a name="set-expiration-date"></a>Definir data de validade
Pode definir uma data de validade quando [criar o VM](devtest-lab-add-vm.md). Nas **definições avançadas,** escolha o ícone do calendário para especificar uma data em que o VM é automaticamente eliminado. Por predefinição, o VM nunca expira.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de definir e aplicar as várias definições de política de VM para o seu laboratório, aqui ficam algumas coisas a experimentar a seguir:

* [Compreender endereços IP partilhados](devtest-lab-shared-ip.md) - Explica como os endereços IP partilhados são usados em Laboratórios DevTest para minimizar o número de endereços IP públicos necessários para se ligar aos seus VMs de laboratório.
* [Configure a gestão de custos](devtest-lab-configure-cost-management.md) - Ilustra como utilizar o **gráfico mensal de tendências de custos estimados**  
  para visualizar o custo estimado do mês em curso e o custo de fim de mês projetado.
* [Crie uma imagem personalizada](devtest-lab-create-template.md) - Quando criar um VM, especifique uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada a partir de um ficheiro VHD.
* [Configure Imagens marketplace](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas ao criar VMs em laboratório.
* [Criar um VM em laboratório](devtest-lab-add-vm.md) - Ilustra como criar um VM a partir de uma imagem base (personalizada ou marketplace), e como trabalhar com artefactos no seu VM.
