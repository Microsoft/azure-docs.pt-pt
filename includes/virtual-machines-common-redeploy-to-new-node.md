---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184264"
---
## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
1. Selecione o VM que pretende recolocar e, em seguida, selecione o botão *Reutilizar* na lâmina *Definições.* Pode ser necessário deslocar-se para baixo para ver a secção **de suporte e resolução** de problemas que contém o botão 'Redeploy' como no seguinte exemplo:
   
    ![Lâmina VM Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Para confirmar a operação, selecione o botão *reimplantar:*
   
    ![Reutilizar uma lâmina VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. O **Estado** do VM altera-se à *atualização* à medida que o VM se prepara para reimplantar, como mostra o seguinte exemplo:
   
    ![Atualização vm](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. O **Status** muda então para *Iniciar* como o VM inicia um novo anfitrião Azure, como mostra o seguinte exemplo:
   
    ![VM começando](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Após o VM terminar o processo de arranque, o **Status** regressa então à *corrida,* indicando que o VM foi redistribuído com sucesso:
   
    ![VM em execução](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

