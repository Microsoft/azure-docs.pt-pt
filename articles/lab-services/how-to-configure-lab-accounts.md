---
title: Configure o encerramento automático de VMs nos Serviços de Laboratório Azure
description: Este artigo descreve como configurar o encerramento automático de VMs na conta de laboratório.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c0a147a81aaed88313a1b9aa4b0754d9a3badcb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650039"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configure o encerramento automático de VMs para uma conta de laboratório

Pode ativar várias funcionalidades de controlo de custos de paragem automática para evitar custos adicionais quando as máquinas virtuais não estão a ser utilizadas ativamente. A combinação das três funcionalidades de paragem e desconexão automáticas que se seguem captura a maioria dos casos em que os utilizadores deixam acidentalmente as suas máquinas virtuais em funcionamento:
 
- Desconectar automaticamente os utilizadores de máquinas virtuais que o SISTEMA considera inativas.
- Desligue automaticamente as máquinas virtuais quando os utilizadores se desligam.
- Desligue automaticamente as máquinas virtuais que são iniciadas mas os utilizadores não se conectam.

Reveja mais detalhes sobre as funcionalidades de paragem automática no controlo de custos Maximize com a secção [de definições de paragem automática.](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)

## <a name="enable-automatic-shutdown"></a>Ativar o encerramento automático

1. No [portal Azure](https://portal.azure.com/) navegue para a página **de Conta de Laboratório.**
1. Selecione **as definições** de Laboratório no menu esquerdo.
1. Selecione a definição de paragem automática que é apropriada para o seu cenário.  

    > [!div class="mx-imgBorder"]
    > ![Definição automática de paragem na conta do laboratório](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    A configuração(s) aplica-se a todos os laboratórios criados na conta de laboratório. Um criador de laboratório (educador) pode anular esta definição ao nível do laboratório. A alteração desta definição na conta do laboratório só afetará os laboratórios que são criados após a alteração.

    Para desativar a definição, desmarque a caixa de verificação nesta página. 

## <a name="next-steps"></a>Passos seguintes

Para saber como um dono de laboratório pode configurar ou sobrepor esta definição ao nível do laboratório, consulte [o encerramento automático de VMs para um laboratório](how-to-enable-shutdown-disconnect.md)
