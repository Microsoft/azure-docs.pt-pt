---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467386"
---
Para verificar a VPN, pode criar uma conta de armazenamento que só pode ser acedida pela rede virtual que criou. Siga estes passos para criar e associar esta conta de armazenamento à rede virtual que criou:

1. Criar uma conta de armazenamento. Pode utilizar a conta de armazenamento que pretende utilizar com o seu dispositivo Azure Stack Edge. Tente aceder à conta de armazenamento a partir de uma rede externa (fora da rede selecionada). A conta de armazenamento deve estar acessível.
2. No portal Azure, vá à conta de armazenamento. 
3. Vá a **Firewalls e redes virtuais.** No painel direito para permitir o **acesso a partir de,** escolha **redes selecionadas**. Na **nossa conta de armazenamento Secure com redes virtuais,** escolha **+ Adicionar a rede virtual existente.**

    ![Verificar VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. Na lâmina das **redes Adicionar:**

    - Selecione uma subscrição. Esta é a mesma subscrição que está associada ao seu recurso Azure Stack Edge/ Data Box Gateway. 
    - A partir da lista de dropdown, escolha a rede virtual para associar a esta conta de armazenamento. Selecione a rede virtual que criou no passo anterior.
    - Nas **Subnetas,** escolha o *_* predefinido_* _ e o _GatewaySubnet*. Os pontos finais de serviço serão ativados para esta rede virtual/combinações sub-rede. O acesso à capacidade de acesso demora até 15 minutos a ser concluído.
    - Selecione **Ativar**.

    ![Verificar VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Guardar **Definições**.

    ![Verificar VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Depois de serem guardadas as definições, pode ver as sub-redes para as quais a rede virtual está ativada.

    ![Verificar VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Para verificar se os dados passarão agora apenas sobre a VPN, siga estes passos: 
    - Tente aceder à conta de armazenamento a partir de uma rede externa (fora da rede selecionada). A conta de armazenamento não deve ser acessível. 
    - Tente aceder à conta de armazenamento a partir de rede/sub-redes virtuais que ativou em redes selecionadas. A conta de armazenamento deve estar acessível. 
 
Só pode aceder a esta conta de armazenamento se tiver a VPN ativada. Se desativar a VPN, também terá de ajustar as definições de rede da conta de armazenamento. 

Para mais informações, aceda a [firewalls de armazenamento Configure Azure e redes virtuais.](../articles/storage/common/storage-network-security.md) 

