---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184264"
---
## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
1. Selecione o VM que pretende recolocar e, em seguida, selecione o botão *Dereserva na* lâmina *'Definições'.* Pode ser necessário deslocar para baixo para ver a secção **de Suporte e Resolução de Problemas** que contém o botão 'Reimplantar' como no exemplo seguinte:
   
    ![Lâmina Azure VM](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Para confirmar a operação, selecione o botão *Recolocação:*
   
    ![Recolocar uma lâmina VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. O **estado** das alterações ao VM à *medida* que o VM se prepara para a reafectação, como mostra o seguinte exemplo:
   
    ![Atualização de VM](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. O **Estado** muda então para *Iniciar* à medida que o VM se inicia num novo anfitrião Azure, como mostra o seguinte exemplo:
   
    ![Início de VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Após o VM terminar o processo de arranque, o **Estado** volta a *funcionar,* indicando que o VM foi redistribuído com sucesso:
   
    ![VM correndo](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

