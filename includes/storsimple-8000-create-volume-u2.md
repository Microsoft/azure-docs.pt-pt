---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2abfa29671bd804ee75194ef621fe07f06c015e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125308"
---
#### <a name="to-create-a-volume"></a>Para criar um volume
1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione o seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-create-volume-u2/step5createvol1.png)

2. No painel **Adicionar um volume**:
   
   1. O campo **Selecionar dispositivo** é preenchido automaticamente com o seu dispositivo atual.

   2. Na lista pendente, selecione o contentor de volumes onde precisa de adicionar um volume. 

   3. Escreva um **Nome** para o volume. Não pode mudar o nome de um volume depois de o mesmo ter sido criado.

   4. Na lista pendente, selecione o **Tipo** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**.
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
       
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo.

   5. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, é reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não pode criar nenhum volume escalonado a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume escalonado. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.

      1. No campo **Anfitriões ligados**, clique na seta. 

         ![Anfitriões ligados](./media/storsimple-8000-create-volume-u2/step5createvol2.png)

      1. No painel **Anfitriões ligados**, escolha um ACR existente ou adicione um novo ACR efetuando os seguintes passos:

         1. Forneça um **Nome** para o ACR.
         2. Em **Nome do Iniciador iSCSI**, forneça o Nome Qualificado (IQN) do iSCSI do seu anfitrião do Windows. Se não tiver o IQN, veja [Get the IQN of a Windows Server host (Obter o IQN de um anfitrião do Windows Server)](#get-the-iqn-of-a-windows-server-host).

      1. Clique em **Criar**. É criado um volume com as definições especificadas.

         ![Clique em Criar](./media/storsimple-8000-create-volume-u2/step5createvol3.png)

         > [!NOTE]
         > Tenha em atenção que o volume que criou aqui não está protegido. Terá de criar e associar políticas de cópia de segurança a este volume para efetuar cópias de segurança agendadas. 

