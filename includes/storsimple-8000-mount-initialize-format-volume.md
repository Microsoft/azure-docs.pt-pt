---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 23d0f16e025727f8d733f973a2c751ba7114fa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562235"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume
1. Inicie o iniciador do Microsoft iSCSI.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Deteção**, clique em **Detetar Portal**.
3. Na caixa de diálogo **Detetar Portal de Destino**, forneça o endereço IP da sua interface de rede ativada com permissão para iSCSI e clique em **OK**. 
4. Na janela **Propriedades do Iniciador iSCSI**, no separador **Destinos**, localize os **Destinos Detetados**. O estado do dispositivo deve ser apresentado como **Inativo**.
5. Selecione o dispositivo de destino e clique em **Ligar**. Quando o dispositivo estiver ligado, o estado deverá mudar para **Ligado**. (Para obter mais informações sobre como utilizar o iniciador Microsoft iSCSI, veja [Instalar e Configurar o Iniciador Microsoft iSCSI][1]).
6. No anfitrião do Windows, prima a tecla do Logótipo do Windows + X e clique em **Executar**. 
7. Na caixa de diálogo **Executar**, escreva **Diskmgmt.msc**. Clique em **OK**. Será apresentada a caixa de diálogo **Gestão de Discos**. O painel da direita apresentará os volumes no anfitrião.
8. Na janela **Gestão de Discos**, os volumes montados serão apresentados como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.
   
     ![Inicializar a formatação do volume](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. Clique novamente com o botão direito do rato no volume (clique no nome do disco) e, em seguida, clique em **Inicializar**.
10. Para formatar um volume simples, execute os seguintes passos:
    
    1. Selecione o volume, faça duplo clique nele (clique na área à direita) e clique em **Novo Volume Simples**.
    2. No assistente Novo Volume Simples, especifique o tamanho do volume e a letra de unidade e configure o volume como um sistema de ficheiros NTFS.
    3. Especifique um tamanho de unidade de alocação de 64 KB. Este tamanho de unidade de alocação funciona bem com os algoritmos de eliminação de duplicados utilizados na solução StorSimple.
    4. Efetue uma formatação rápida.

<!--Link references-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)