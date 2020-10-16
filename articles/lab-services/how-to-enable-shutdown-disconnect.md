---
title: Configure o encerramento automático de VMs para um laboratório nos Serviços do Laboratório Azure
description: Saiba como ativar ou desativar o encerramento automático de VMs quando uma ligação remota do ambiente de trabalho estiver desligada.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 67b51941207fdd4913df9a92362959bbd468d336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649886"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Configure o encerramento automático de VMs para um laboratório

Este artigo mostra-lhe como pode configurar o encerramento automático de VMs para um laboratório.

Pode ativar várias funcionalidades de controlo de custos de paragem automática para evitar custos adicionais quando as máquinas virtuais não estão a ser utilizadas ativamente. A combinação das três funcionalidades de paragem e desconexão automáticas que se seguem captura a maioria dos casos em que os utilizadores deixam acidentalmente as suas máquinas virtuais em funcionamento:
 
* Desconectar automaticamente os utilizadores de máquinas virtuais que o SISTEMA considera inativas.
* Desligue automaticamente as máquinas virtuais quando os utilizadores se desligam.
* Desligue automaticamente as máquinas virtuais que são iniciadas mas os utilizadores não se conectam.

Reveja mais detalhes sobre as funcionalidades de paragem automática no controlo de custos Maximize com a secção [de definições de paragem automática.](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)

Um administrador de conta de laboratório pode configurar esta definição para a conta de laboratório na qual cria laboratórios. Para obter mais informações, consulte [configurar o encerramento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md). Como dono de laboratório, pode anular a definição ao criar um laboratório ou depois de o laboratório ser criado. 

## <a name="configure-for-the-lab-level"></a>Configuração para o nível de laboratório

Pode configurar a definição de paragem automática nos Serviços de [Laboratório Azure](https://labs.azure.com/).

* ao criar um laboratório (em **políticas de laboratório),** ou
* depois de o laboratório ser criado (em **Definições)**

> [!div class="mx-imgBorder"]
> ![Configurar no momento da criação de laboratório](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Certifique-se de rever os detalhes sobre a paragem automática no controlo de custos Do Maximize com a secção [de definições de paragem automática.](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)

> [!WARNING]
> Se desligar o sistema operativo Linux ou Windows (OS) num VM antes de desligar uma sessão DE PDR para o VM, a função de autoshutdown não funcionará corretamente.  
## <a name="next-steps"></a>Passos seguintes

[Painel para laboratórios de sala de aula](use-dashboard.md)
