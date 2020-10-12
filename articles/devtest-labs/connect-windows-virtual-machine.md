---
title: Ligue-se às suas máquinas virtuais Windows em Azure DevTest Labs
description: Saiba como ligar-se à sua máquina virtual Windows em laboratório (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540720"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Ligue-se a um Windows VM no seu laboratório (Azure DevTest Labs)
Este artigo mostra-lhe como ligar-se a um VM do Windows no seu laboratório. 

## <a name="connect-to-a-windows-vm"></a>Ligar a uma VM do Windows
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Procure e selecione Laboratórios DevTest":::    
1. Da lista de laboratórios, selecione o seu **laboratório.**

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Procure e selecione Laboratórios DevTest":::            
1. Na página inicial do seu laboratório, selecione o seu VM do Windows da lista **de máquinas virtuais.** 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Procure e selecione Laboratórios DevTest":::                
1. Na página **da máquina Virtual** para o seu VM, selecione **Connect** na barra de ferramentas. Se o VM estiver parado, selecione **Iniciar** primeiro para iniciar o VM.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Procure e selecione Laboratórios DevTest":::                    
1. Se estiver a utilizar o navegador Edge, vê o link para o **ficheiro RDP descarregado** na parte inferior do navegador. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="Procure e selecione Laboratórios DevTest":::                        
1. Abra o ficheiro RDP e introduza as suas credenciais VM que digitou ao criar o VM. Deve estar ligado ao Windows VM agora. 

## <a name="next-steps"></a>Passos Seguintes
[Como: ligar-se a um Linux VM](connect-linux-virtual-machine.md)
