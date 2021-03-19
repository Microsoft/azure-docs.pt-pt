---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86545033"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Para criar pontos finais públicos na aplicação da cloud

1. Inicie sessão no portal do Azure.
2. Aceda a **Máquinas Virtuais** e, em seguida, selecione e clique na máquina virtual que está a ser utilizada como a sua aplicação da cloud.
    
3. Tem de criar uma regra de grupo de segurança de rede (NSG) para controlar o fluxo de tráfego que entra e sai da sua máquina virtual. Execute os seguintes passos para criar uma regra NSG.
    1. Selecione **Grupo de segurança de rede**.
        ![Screenshot da página da máquina virtual. Na secção Definições, destaca-se o grupo de segurança da rede.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Clique no grupo de segurança de rede predefinido que é apresentado.
        ![Screenshot da página do grupo de segurança da rede. O grupo de segurança da rede padrão é destacado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selecione **Regras de segurança de entrada**.
        ![Screenshot de uma página mostrando as propriedades do grupo de segurança de rede padrão. No painel de navegação, destacam-se as regras de segurança de entrada.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Clique em **+ Adicionar** para criar uma regra de segurança de entrada.
        ![Screenshot da página de regras de segurança de entrada. O sinal de mais e a palavra Add estão ao lado um do outro e são destacados.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        No painel Adicionar regra de segurança de entrada:

        1. Em **Nome**, escreva o seguinte nome para o ponto final: WinRMHttps.
        
        2. Para a **Prioridade**, selecione um número inferior a 1000 (que é a prioridade para a regra predefinida). Quanto mais elevado for o valor, mais reduzida é a prioridade.

        3. Defina a **Origem** para **Qualquer**.

        4. Para o **Serviço**, selecione **WinRM**. O **Protocolo** é automaticamente definido para **TCP** e o **Intervalo da porta** está definido como **5986**.

        5. Clique em **OK** para criar a regra.

            ![Screenshot da lâmina de regra de segurança de entrada adicionar. Os valores são preenchidos conforme descrito no procedimento, e o botão OK é realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. O passo final é associar o grupo de segurança de rede a uma sub-rede ou uma interface de rede específica. Execute os passos seguintes para associar o seu grupo de segurança de rede a uma sub-rede.
    1. Aceda a **Sub-redes**.
    2. Clique em **+ Associar**.
        ![Screenshot da página subnetas. O sinal de mais e a palavra Associado estão ao lado um do outro e são destacados.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selecione a rede virtual e, em seguida, selecione a sub-rede apropriada.
    4. Clique em **OK** para criar a regra.

        ![Screenshot da página da sub-rede Associate. A rede virtual é selecionada e o botão OK é realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Uma vez criada a regra, pode ver os respetivos detalhes para determinar o endereço IP Virtual Público (VIP). Registe esse endereço.


