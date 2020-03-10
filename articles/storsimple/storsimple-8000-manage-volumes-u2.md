---
title: Gerir volumes StorSimple (Atualização 3)
description: Explica como adicionar, modificar, monitorizar e eliminar volumes StorSimple e como desligá-los se necessário.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381859"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Utilize o serviço StorSimple Device Manager para gerir volumes (Atualizar 3 ou mais tarde)

## <a name="overview"></a>Descrição geral

Este tutorial explica como usar o serviço StorSimple Device Manager para criar e gerir volumes nos dispositivos da série StorSimple 8000 que executam o Update 3 e mais tarde.

O serviço StorSimple Device Manager é uma extensão no portal Azure que permite gerir a sua solução StorSimple a partir de uma única interface web. Utilize o portal Azure para gerir volumes em todos os seus dispositivos. Também pode criar e gerir serviços StorSimple, gerir dispositivos, políticas de backup e catálogo de backup, e ver alertas.

## <a name="volume-types"></a>Tipos de volume

Os volumes StorSimple podem ser:

* **Volumes fixados localmente**: Os dados nestes volumes permanecem sempre no dispositivo StorSimple local.
* **Volumes hierárquicos**: Os dados nestes volumes podem derramar para a nuvem.

Um volume de arquivo é um tipo de volume hierárquico. O tamanho maior do pedaço de deduplicação usado para volumes de arquivo permite ao dispositivo transferir segmentos maiores de dados para a nuvem.

Se necessário, pode alterar o tipo de volume de local para tiered ou de tiered para local. Para mais informações, vá alterar [o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>volumes afixados localmente

Os volumes fixados localmente são volumes totalmente provisionados que não nificam dados para a nuvem, garantindo assim garantias locais para dados primários, independentes da conectividade da nuvem. Os dados relativos aos volumes fixados localmente não são desduplicados e comprimidos; no entanto, os instantâneos de volumes fixados localmente são duplicados. 

Os volumes fixados localmente estão totalmente aprovisionados; portanto, deve ter espaço suficiente no seu dispositivo quando os criar. Pode fornecer volumes fixados localmente até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. O StorSimple reserva o espaço local restante no dispositivo para imagens, metadados e processamento de dados. Pode aumentar o tamanho de um volume fixado localmente para o espaço máximo disponível, mas não pode diminuir o tamanho de um volume uma vez criado.

Quando se cria um volume localmente fixado, o espaço disponível para a criação de volumes hierárquicos é reduzido. O inverso também é verdade: se tiver volumes hierárquicos existentes, o espaço disponível para criar volumes fixados localmente será inferior aos limites máximos acima indicados. Para obter mais informações sobre os volumes locais, consulte as [perguntas frequentes sobre volumes fixados localmente](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumes escalonados

Os volumes hierárquicos são volumes pouco provisionados em que os dados frequentemente acedidos permanecem locais no dispositivo e os dados menos utilizados são automaticamente nivelados para a nuvem. O fornecimento fino é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, a StorSimple utiliza um fornecimento fino para alocar espaço suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento em nuvem facilita esta abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para satisfazer as exigências em mudança.

Se estiver a utilizar o volume hierárquico para obter dados de arquivo, selecione o utilize este volume para uma caixa de **verificação** de dados de arquivo com menos frequência para alterar o tamanho do pedaço de duplicação para 512 KB. Se não selecionar esta opção, o volume correspondente será utilizado um pedaço de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.


### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte o quadro seguinte para obter a máxima capacidade prevista para cada dispositivo e tipo de volume. (Note que os volumes fixados localmente não estão disponíveis num dispositivo virtual.)

|  | Tamanho máximo do volume hierárquico | Tamanho máximo do volume fixado localmente |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuais** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>A lâmina de volumes

A lâmina **Volumes** permite-lhe gerir os volumes de armazenamento que são aprovisionados no dispositivo Microsoft Azure StorSimple para os seus iniciadores (servidores). Apresenta a lista de volumes nos dispositivos StorSimple ligados ao seu serviço.

 ![Página de volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Um volume consiste numa série de atributos:

* **Nome do Volume** – Um nome descritivo que deve ser único e ajuda a identificar o volume. Este nome também é usado em relatórios de monitorização quando filtra risa num volume específico. Uma vez criado o volume, não pode ser renomeado.
* **Estado** – Pode estar online ou offline. Se um volume estiver offline, não é visível para iniciadores (servidores) que são autorizados a aceder à utilização do volume.
* **Capacidade** – especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Os volumes fixados localmente são totalmente provisionados e residem no dispositivo StorSimple. Os volumes hierárquicos são pouco provisionados e os dados são duplicados. Com volumes pouco provisionados, o seu dispositivo não pré-atribui a capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade de volume configurada. A capacidade de volume é atribuída e consumida a pedido.
* **Tipo** – Indica se o volume está **nivida (o** padrão) ou **fixado localmente**.

Utilize as instruções deste tutorial para executar as seguintes tarefas:

* Adicione um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Eliminar um volume 
* Pegue um volume offline 
* Monitorizar um volume 

## <a name="add-a-volume"></a>Adicione um volume

Criou [um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implementação do seu dispositivo da série StorSimple 8000. Adicionar um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione o seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. No painel **Adicionar um volume**:
   
    1. O campo **Selecionar dispositivo** é preenchido automaticamente com o seu dispositivo atual.

    2. Na lista pendente, selecione o contentor de volumes onde precisa de adicionar um volume.

    3.  Escreva um **Nome** para o volume. Uma vez criado o volume, não é possível renomear o volume.

    4. Na lista pendente, selecione o **Tipo** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**.
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
       
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo.

    5. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, é reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não pode criar nenhum volume escalonado a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume escalonado. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.

    6. No campo **Anfitriões ligados**, clique na seta. Na lâmina **de anfitriões Conectados,** escolha um ACR existente ou adicione um novo ACR. Se escolher um novo ACR, em seguida, forneça um **Nome** para o seu ACR, forneça o **nome iSCSI Qualificado** (IQN) do seu anfitrião windows. Se não tiver o IQN, vá obter o IQN de um anfitrião do Windows Server. Clique em **Criar**. É criado um volume com as definições especificadas.

        ![Clique em Criar](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

O seu novo volume está agora pronto a ser utilizado.

> [!NOTE]
> Se criar um volume localmente fixado e, em seguida, criar outro volume localmente fixado imediatamente a seguir, os trabalhos de criação de volume funcionam sequencialmente. O primeiro trabalho de criação de volume deve terminar antes que o próximo trabalho de criação de volume possa começar.

## <a name="modify-a-volume"></a>Modificar um volume

Modifique um volume quando precisa expandi-lo ou alterar os anfitriões que acedem ao volume.

> [!IMPORTANT]
> * Se modificar o tamanho do volume do dispositivo, o tamanho do volume também tem de ser alterado no hospedeiro.
> * Os passos do lado do anfitrião aqui descritos são para o Windows Server 2012 (2012R2). Os procedimentos para o Linux ou outros sistemas operativos hospedeiros serão diferentes. Consulte as instruções do sistema operativo do anfitrião ao modificar o volume de um hospedeiro que executa outro sistema operativo.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. A partir da listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **Definições e Volumes**.

    ![Ir para a lâmina Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. A partir da listagem tabular de volumes, selecione o volume e clique à direita para invocar o menu de contexto. Selecione **Desligar** para retirar o volume que irá modificar offline.

    ![Selecione e desleve o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na lâmina **Desativada,** reveja o impacto de desligar o volume e selecione a caixa de verificação correspondente. Certifique-se de que o volume correspondente no hospedeiro está offline primeiro. Para obter informações sobre como desativar um volume offline no seu servidor de anfitriões ligado ao StorSimple, consulte as instruções específicas do sistema operativo. Clique **Em desligar.**

    ![Rever o impacto da desativação do volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Depois de o volume estar offline (como mostra o estado do volume), selecione o volume e clique à direita para invocar o menu de contexto. Selecione **Modificar o volume**.

    ![Selecione modificar volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Na lâmina de **volume Modificar,** pode efazer as seguintes alterações:
   
   1. O **nome** do volume não pode ser editado.
   2. Converter o **Tipo** de fixado localmente para tiered ou de tiered para localmente fixado (ver [Alterar o tipo](#change-the-volume-type) de volume para obter mais informações).
   3. Aumentar a **capacidade provisionada.** A **capacidade provisionada** só pode ser aumentada. Não se pode encolher um volume depois de criado.
   4. Nos **anfitriões conectados,** pode modificar o ACR. Para modificar um ACR, o volume deve estar offline.

       ![Rever o impacto da desativação do volume](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Clique em **Guardar** para guardar as suas alterações. Quando lhe for pedida a confirmação, clique em **Sim**. O portal Azure apresentará uma mensagem de volume atualizada. Apresentará uma mensagem de sucesso quando o volume tiver sido atualizado com sucesso.

    ![Rever o impacto da desativação do volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se estiver a expandir um volume, complete os seguintes passos no seu computador anfitrião windows:
   
   1. Ir para **Gestão de Computadores** ->**Gestão de Discos.**
   2. Clique na **gestão** do disco e selecione **Rescan Disks**.
   3. Na lista de discos, selecione o volume que atualizou, clique à direita e, em seguida, **selecione Extend Volume**. O assistente de volume extensão começa. Clique em **Seguinte**.
   4. Complete o assistente, aceitando os valores predefinidos. Depois de terminado o assistente, o volume deve mostrar o tamanho aumentado.
      
      > [!NOTE]
      > Se expandir um volume localmente fixado e, em seguida, expandir outro volume localmente fixado imediatamente depois, os trabalhos de expansão do volume funcionam sequencialmente. O primeiro trabalho de expansão de volume deve terminar antes que o próximo trabalho de expansão do volume possa começar.
      

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Pode alterar o tipo de volume de camadas para fixações locais ou de fixação local para camadas. No entanto, esta conversão não deve ser frequente.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Niversões para considerações locais de conversão de volume

Algumas razões para converter um volume de tiered para localmente fixado são:

* Garantias locais relativas à disponibilidade e desempenho dos dados
* Eliminação de latenciências em nuvem e problemas de conectividade em nuvem.

Normalmente, estes são pequenos volumes existentes que você quer aceder com frequência. Um volume fixado localmente é totalmente provisionado quando é criado. 

Se estiver a converter um volume hierárquico num volume fixado localmente, o StorSimple verifica que tem espaço suficiente no seu dispositivo antes de iniciar a conversão. Se tiver espaço insuficiente, receberá um erro e a operação será cancelada. 

> [!NOTE]
> Antes de iniciar uma conversão de camadas para fixações locais, certifique-se de que considera os requisitos de espaço das suas outras cargas de trabalho. 

A conversão de um volume nidigrado para um volume fixado localmente pode afetar negativamente o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo necessário para completar a conversão:

* Não há largura de banda suficiente.
* Não há apoio atual.

Para minimizar os efeitos que estes fatores podem ter:

* Reveja as suas políticas de estrangulamento da largura de banda e certifique-se de que está disponível uma largura de banda dedicada de 40 Mbps.
* Agende a conversão para horas fora do pico.
* Tire uma foto na nuvem antes de iniciar a conversão.

Se estiver a converter vários volumes (suportando diferentes cargas de trabalho), então deve priorizar a conversão de volume para que volumes prioritários mais elevados sejam convertidos primeiro. Por exemplo, deve converter volumes que acolhem máquinas virtuais (VMs) ou volumes com cargas de trabalho SQL antes de converter volumes com cargas de trabalho de partilha de ficheiros.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Considerações locais de conversão de volume tiered

Você pode querer mudar um volume fixado localmente para um volume hierárquico se você precisar de espaço adicional para fornecer outros volumes. Quando converte o volume fixado localmente para tiered, a capacidade disponível no dispositivo aumenta pelo tamanho da capacidade libertada. Se os problemas de conectividade impedirem a conversão de um volume do tipo local para o tipo hierárquico, o volume local apresentará propriedades de um volume hierárquico até que a conversão esteja concluída. Isto porque alguns dados podem ter derramado para a nuvem. Estes dados derramados continuam a ocupar o espaço local no dispositivo que não pode ser libertado até que a operação seja reiniciada e concluída.

> [!NOTE]
> Converter um volume pode demorar algum tempo e não pode cancelar uma conversão depois de começar. O volume permanece on-line durante a conversão, e você pode pegar backups, mas você não pode expandir ou restaurar o volume enquanto a conversão está acontecendo.


#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. A partir da listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **Definições e Volumes**.

    ![Ir para a lâmina Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A partir da listagem tabular de volumes, selecione o volume e clique à direita para invocar o menu de contexto. Selecione **Modificar**.

    ![Selecione modificar a partir do menu de contexto](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na lâmina de **volume Modificar,** altere o tipo de volume selecionando o novo tipo da lista de drop-down **do Tipo.**
   
   * Se estiver a mudar o tipo para **fixação local,** o StorSimple verificará se existe capacidade suficiente.
   * Se estiver a mudar o tipo para **Tiered** e este volume for utilizado para dados de arquivo, selecione o utilize este volume para uma caixa de verificação de dados de **arquivo com menos frequência.**
   * Se estiver a configurar um volume fixado localmente como hierárquico ou _vice-versa,_ aparece a seguinte mensagem.
   
     ![Alterar a mensagem do tipo de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Clique em **Guardar** para guardar as alterações. Quando solicitado para confirmação, clique **em Sim** para iniciar o processo de conversão. 

    ![Salvar e confirmar](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. O portal Azure apresenta uma notificação para a criação de emprego que atualizaria o volume. Clique na notificação para monitorizar o estado do trabalho de conversão de volume.

    ![Trabalho para conversão de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Pegue um volume offline

Pode ser necessário desligar um volume quando planeia modificar ou eliminar o volume. Quando um volume está offline, não está disponível para acesso de leitura- escrita. Deve desligar o volume do hospedeiro e do dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para tirar um volume offline

1. Certifique-se de que o volume em questão não está a ser utilizado antes de o desligar.
2. Desative o volume no hospedeiro primeiro. Isto elimina qualquer risco potencial de corrupção de dados no volume. Para obter passos específicos, consulte as instruções do seu sistema operativo anfitrião.
3. Depois de o hospedeiro estar offline, desative o volume do dispositivo através da realização dos seguintes passos:
   
    1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. A partir da listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **Definições e Volumes**.

        ![Ir para a lâmina Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. A partir da listagem tabular de volumes, selecione o volume e clique à direita para invocar o menu de contexto. Selecione **Desligar** para retirar o volume que irá modificar offline.

        ![Selecione e desleve o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na lâmina **Desativada,** reveja o impacto de desligar o volume e selecione a caixa de verificação correspondente. Clique **Em desligar.** 

    ![Rever o impacto da desativação do volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      É notificado quando o volume está offline. O estado do volume também atualiza para Offline.
      
4. Depois de um volume estar offline, se selecionar o volume e o clique direito, a opção **Bring Online** fica disponível no menu de contexto.

> [!NOTE]
> O comando **Take Offline** envia um pedido ao dispositivo para desligar o volume. Se os anfitriões ainda estiverem a utilizar o volume, isto resulta em ligações partidas, mas desligar o volume não falhará.

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Só é possível eliminar um volume se estiver offline.

Complete os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para apagar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. A partir da listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **Definições e Volumes**.

    ![Ir para a lâmina Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, desative-o primeiro. Siga os passos em [Tomar um volume offline](#take-a-volume-offline).
4. Depois de o volume estar offline, selecione o volume, clique à direita para invocar o menu de contexto e, em seguida, **selecione Delete**.

    ![Selecione excluir do menu de contexto](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Na lâmina **Eliminar,** reveja e selecione a caixa de verificação com o impacto de apagar um volume. Quando se elimina um volume, perdem-se todos os dados que residem no volume. 

    ![Guardar e confirmar alterações](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Após a eliminação do volume, a lista tabular de atualizações de volumes para indicar a eliminação.

    ![Lista de volumes atualizada](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se eliminar um volume fixado localmente, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O Serviço de Gestor de Dispositivos StorSimple atualiza periodicamente o espaço local disponível. Sugerimos que espere alguns minutos antes de tentar criar o novo volume.
   >
   > Além disso, se eliminar um volume fixado localmente e, em seguida, apagar outro volume fixado localmente imediatamente a seguir, os trabalhos de eliminação de volume são sequenciais. O primeiro trabalho de eliminação de volume deve terminar antes de começar o próximo trabalho de eliminação de volume.

## <a name="monitor-a-volume"></a>Monitorizar um volume

A monitorização do volume permite-lhe recolher estatísticas relacionadas com I/O para um volume. A monitorização está ativada por padrão para os primeiros 32 volumes que cria. A monitorização de volumes adicionais é desativada por defeito. 

> [!NOTE]
> A monitorização dos volumes clonados é desativada por defeito.


Execute os seguintes passos para ativar ou desativar a monitorização durante um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização do volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. A partir da listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **Definições e Volumes**.
2. A partir da listagem tabular de volumes, selecione o volume e clique à direita para invocar o menu de contexto. Selecione **Modificar**.
3. Na lâmina de **volume Modificar,** para **monitorizar** selecione **ativar** ou **desativar** para ativar ou desativar a monitorização.

    ![Desativar a monitorização](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Clique em **Guardar** e quando solicitado para confirmação, clique **em Sim**. O portal Azure apresenta uma notificação para atualizar o volume e, em seguida, uma mensagem de sucesso, depois de o volume ser atualizado com sucesso.

## <a name="next-steps"></a>Passos Seguintes

* Aprenda a [clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

