---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4ca02b3d6f4dec805c6ce58d4470c4c8e5f3454f
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375895"
---
#### <a name="to-create-a-volume-container"></a>Para criar um contentor de volume
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione e clique num dispositivo. 

    ![Painel Contentor de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. No dashboard do dispositivo, clique em **+ Adicionar contentor de volumes**

    ![Lâmina de recipiente de volume 2](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. No painel **Adicionar contentor de volumes** :
   
   1. O dispositivo é selecionado automaticamente.
   2. Forneça um **Nome** para o contentor do volume. O nome tem de ter entre 3 e 32 carateres. Não pode mudar o nome de um contentor de volumes depois de o mesmo ter sido criado.
   3. Selecione **Ativar Encriptação do Armazenamento na Nuvem** para ativar a encriptação dos dados enviados do dispositivo para a nuvem.
   4. Forneça e confirme uma **Chave de Encriptação do Armazenamento na Nuvem** que tenha entre 8 e 32 carateres. Esta chave é utilizada pelo dispositivo para aceder aos dados encriptados.
   5. Selecione uma **Conta de Armazenamento** associada a este contentor de volume Pode escolher uma conta de armazenamento existente ou a conta predefinida que é gerada no momento da criação do serviço. Também pode utilizar a opção **Adicionar nova** para especificar uma conta de armazenamento que não esteja associada a esta subscrição do serviço.
   6. Selecione **Sem limite** na lista pendente **Especificar largura de banda** se desejar consumir toda a largura de banda disponível. Também pode configurar esta opção para **Personalizar** para utilizar controlos de largura de banda e especificar um valor entre 1 e 1000 Mbps.
      Se tiver disponíveis as informações de utilização da largura de banda, poderá conseguir alocar a largura de banda com base no agendamento, especificando **Selecionar um modelo de largura de banda**. Para um procedimento passo-a-passo, veja [Adicionar um modelo de largura de banda](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Lâmina de recipiente de volume 3](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Clique em **Criar**.

        ![Lâmina de recipiente de volume 4](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Será notificado quando o contentor de volumes for criado com êxito.

       ![Notificação de criação do contentor de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   O contentor de volumes recentemente criado está listado na lista de contentores de volumes do seu dispositivo.

   ![Painel Adicionar contentor de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


