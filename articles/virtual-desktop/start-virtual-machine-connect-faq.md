---
title: Windows Virtual Desktop Start VM Connect FAQ - Azure
description: Perguntas frequentes e boas práticas para a utilização do VM iniciar no connect.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0fd6edf3fbcd6fea7503acd7265850723b5ec09f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080493"
---
# <a name="start-vm-on-connect-faq-preview"></a>Iniciar VM no Connect FAQ (pré-visualização)

> [!IMPORTANT]
> A funcionalidade Start VM on Connect encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo abrange perguntas frequentes sobre a funcionalidade Start Virtual Machine (VM) no Connect (pré-visualização) para piscinas de anfitriões virtuais do Windows Desktop.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Os VM são automaticamente translocados quando um utilizador deixa de os utilizar?

N.º Você precisará configurar políticas adicionais para assinar utilizadores fora das suas sessões e executar scripts de automação Azure para negociar VMs.

Para configurar a política de negociação:

1. Ligue-se remotamente ao VM para o que pretende definir a política.

2. Abra o **Editor de Política de Grupo,** em seguida, vá à configuração local **do**  >  **computador**  >  **Modelos Administrativos windows** Componentes Componentes  >    >  **Remotos desktop Session**  >  **Host**  >  **Time Limits**.

3. Encontre a política que diz **Definir o prazo para sessões desligadas** e, em seguida, alterar o seu valor para **Enabled**.

4. Depois de ativar a apólice, selecione **Terminar uma sessão desligada**.

>[!NOTE]
>Certifique-se de que define o prazo para a política "Terminar uma sessão desligada" para um valor superior a cinco minutos. Um prazo baixo pode fazer com que as sessões dos utilizadores terminem se a sua rede perder a ligação por muito tempo, resultando em trabalho perdido.

A assinatura dos utilizadores não vai negociar os seus VMs. Para aprender a negociar VMs, consulte [Iniciar ou parar VMs durante as horas de folga](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Os utilizadores podem desligar o VM dos seus clientes?

Sim. Os utilizadores podem desligar o VM utilizando o menu Iniciar dentro da sessão, tal como fariam com uma máquina física. No entanto, fechar o VM não vai negociar com o VM. Para aprender a negociar VMs, consulte [Iniciar ou parar VMs durante as horas de folga](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a configurar iniciar vM em ligar, consulte [iniciar a máquina virtual em ligação (pré-visualização)](start-virtual-machine-connect.md).

Se tiver perguntas mais gerais sobre o Windows Virtual Desktop, consulte as nossas [FAQ gerais.](faq.md)
