---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545423"
---
> [!NOTE] 
> Não é possível modificar as definições de encriptação e as credenciais de conta de armazenamento associadas a um recipiente de volume após a sua criação.

#### <a name="to-modify-a-volume-container"></a>Para modificar um recipiente de volume

1. Vá ao seu serviço StorSimple Device Manager e, em seguida, navegue para **os contentores de Volume de gestão >**.

2. A partir da lista tabular de recipientes de volume, selecione o recipiente de volume que pretende modificar. Na página **Dispositivos,** selecione o dispositivo, clique duas vezes no separador **"Volume".**

3. Na listagem tabular dos recipientes de volume, selecione o recipiente de volume que pretende modificar. Na lâmina que se abre, clique em **Modificar** a partir da barra de comando.

    ![Modificar o recipiente de volume](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Na lâmina do **recipiente de volume modificar,** faça os seguintes passos:
   
   1. O nome, a chave de encriptação e a conta de armazenamento associada ao recipiente de volume não podem ser alterados após a sua especificação. Mude a regulação da largura de banda associada.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Alterar a definição de largura de banda](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Clique em **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Na página seguinte da caixa de diálogo **do recipiente de modificação:**<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. A partir da lista de drop-down, escolha um modelo de largura de banda existente.
   1. Reveja as definições de agenda para o modelo de largura de banda especificado.
   1. Clique **em Guardar** e confirmar as alterações.
      
       ![Confirmar alterações](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      A lâmina **dos recipientes de volume** é atualizada para refletir as alterações.
