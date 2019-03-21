---
title: Gerir volumes do StorSimple (atualização 3) | Documentos da Microsoft
description: Explica como adicionar, modificar, monitorizar e eliminar os volumes do StorSimple e como colocar offline se necessário.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f7bfe41b4cdc9989c6b949011bc240275886b6f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093335"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Utilizar o serviço StorSimple Device Manager para gerir volumes (atualização 3 ou posterior)

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir volumes nos dispositivos de série 8000 do StorSimple que executam a atualização 3 e posterior.

O serviço StorSimple Device Manager é uma extensão no portal do Azure que lhe permite gerir a solução StorSimple a partir de uma interface web único. Utilize o portal do Azure para gerir volumes em todos os seus dispositivos. Também pode criar e gerir serviços do StorSimple, gerir dispositivos, as políticas de cópia de segurança e o catálogo de cópia de segurança e ver os alertas.

## <a name="volume-types"></a>Tipos de volume

Volumes do StorSimple podem ser:

* **Volumes afixados localmente**: Dados nestes volumes permanecem no dispositivo StorSimple local em todos os momentos.
* **Volumes em camadas**: Dados nestes volumes podem transbordam para a nuvem.

Um volume de arquivo é um tipo de volume em camadas. O tamanho de segmento de eliminação de duplicados maior utilizado para volumes de arquivos permite que o dispositivo de transferência de grandes segmentos de dados para a cloud.

Se necessário, pode alterar o volume do tipo de local para camadas ou de camadas para o local. Para obter mais informações, aceda a [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes afixados localmente

Os volumes afixados localmente são totalmente aprovisionados volumes que não os dados de escalão para a cloud de fazer, assegurando local garante para dados primários, independentes de conectividade de cloud. Dados em volumes localmente afixados não é eliminação de duplicados e comprimidos; No entanto, os instantâneos de volumes localmente afixados tenham os duplicados eliminados. 

Os volumes afixados localmente são totalmente aprovisionados; Por conseguinte, tem de ter espaço suficiente no seu dispositivo quando criá-los. Pode aprovisionar volumes localmente afixados até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. StorSimple se reserva o espaço local restante no dispositivo para instantâneos, metadados e processamento de dados. Pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível reduzir o tamanho de um volume depois de criada.

Quando cria um volume localmente afixado, o espaço disponível para a criação de volumes em camadas é reduzido. O inverso também é verdade: Se tiver de volumes em camadas existentes, o espaço disponível para criar localmente volumes afixados será inferior aos limites máximos indicados acima. Para obter mais informações sobre volumes locais, consulte a [perguntas mais frequentes sobre os volumes afixados localmente](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumes em camadas

Volumes em camadas são volumes de aprovisionamento dinâmico no qual os dados acedidos com frequência permanecem locais no dispositivo e menos dados utilizados com frequência é automaticamente em camadas para a cloud. Aprovisionamento dinâmico é uma tecnologia de Virtualização na qual armazenamento disponível parece exceder os recursos físicos. Em vez de reservar capacidade de armazenamento suficiente com antecedência, StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza flexível do armazenamento na cloud facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento na cloud para atender às necessidades em mudança.

Se estiver a utilizar o volume em camadas para dados de arquivo, selecione o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação para alterar o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se não selecionar esta opção, o volume em camadas correspondente irá utilizar um tamanho de segmentos de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.


### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte a tabela seguinte para máxima capacidade aprovisionada para cada tipo de dispositivo e o volume. (Tenha em atenção que os volumes afixados localmente não estão disponíveis num dispositivo virtual.)

|  | Tamanho máximo de volume em camadas | Máximo localmente afixado de tamanho do volume |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuais** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>O painel de volumes

O **Volumes** painel permite-lhe gerir os volumes de armazenamento que estão aprovisionados no dispositivo do Microsoft Azure StorSimple para os iniciadores (servidores). Apresenta a lista de volumes nos dispositivos StorSimple ligados ao seu serviço.

 ![Página de volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Um volume consiste numa série de atributos:

* **Nome do volume** – um nome descritivo, que tem de ser exclusivo e ajuda a identificar o volume. Este nome também é utilizado em relatórios de monitorização ao filtrar num volume específico. Assim que o volume for criado, não podem ser renomeado.
* **Estado** – pode estar online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm acesso ao utilizar o volume.
* **Capacidade** – Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes afixados localmente são totalmente aprovisionados e residem no dispositivo StorSimple. Volumes em camadas são com aprovisionamento dinâmico e os dados tem eliminação de duplicados. Com volumes de aprovisionamento dinâmico, o dispositivo previamente não atribuir capacidade de armazenamento físico internamente ou na cloud, de acordo com a capacidade de volume configurado. A capacidade do volume é alocada e consumida sob demanda.
* **Tipo** – indica se o volume está **em camadas** (predefinição) ou **localmente afixado**.

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Eliminar um volume 
* Colocar um volume offline 
* Monitorizar um número 

## <a name="add-a-volume"></a>Adicionar um volume

[Criado um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implementação do seu dispositivo da série StorSimple 8000. A adição de um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione o seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. No painel **Adicionar um volume**:
   
    1. O campo **Selecionar dispositivo** é preenchido automaticamente com o seu dispositivo atual.

    2. Na lista pendente, selecione o contentor de volumes onde precisa de adicionar um volume.

    3.  Escreva um **Nome** para o volume. Assim que o volume for criado, não é possível mudar o nome do volume.

    4. Na lista pendente, selecione o **Tipo** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**.
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
       
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo.

    5. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, é reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não pode criar nenhum volume escalonado a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume escalonado. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.

    6. No campo **Anfitriões ligados**, clique na seta. Na **anfitriões ligados** painel, escolha um ACR existente ou adicione um novo ACR. Se optar por um novo ACR, em seguida, forneça uma **nome** para o ACR, forneça o **nome do iSCSI Qualified** (IQN) de anfitrião do Windows. Se não tiver o IQN, vá para obter o IQN de um anfitrião do Windows Server. Clique em **Criar**. É criado um volume com as definições especificadas.

        ![Clique em Criar](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Está agora pronto para utilizar o seu novo volume.

> [!NOTE]
> Se criar um volume afixado localmente e, em seguida, criar outro volume afixado localmente imediatamente depois disso, a criação do volume trabalhos são executados sequencialmente. A primeira tarefa de criação do volume tem de concluir antes de poderem começar a próxima tarefa de criação do volume.

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando precisa para expandi-lo ou alterar os anfitriões que aceder ao volume.

> [!IMPORTANT]
> * Se modificar o tamanho do volume no dispositivo, o tamanho do volume tem de ser alterada no anfitrião também.
> * As etapas de lado do host descritas aqui são para o Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas de operativos de anfitrião será diferentes. Consulte as instruções de sistema operativo de anfitrião ao modificar o volume num anfitrião com outro sistema operacional.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Na lista tabular de volumes, selecione o volume e com o botão direito para invocar o menu de contexto. Selecione **colocar offline** para colocar o volume irá modificar offline.

    ![Selecione e coloque o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na **colocar offline** painel, reveja o impacto de colocar offline o volume e selecione a caixa de seleção correspondente. Certifique-se de que o volume correspondente no anfitrião está offline pela primeira vez. Para obter informações sobre como aproveitar um volume offline no seu servidor de anfitrião ligado ao StorSimple, referem-se para obter instruções específicas do sistema operativo. Clique em **colocar offline**.

    ![Reveja o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Depois do volume estiver offline (conforme exibido o status de volume), selecione o volume e com o botão direito para invocar o menu de contexto. Selecione **modificar o volume**.

    ![Selecione modificar o volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Na **modificar o volume** painel, pode efetuar as seguintes alterações:
   
   1. O volume **nome** não pode ser editado.
   2. Converter os **tipo** partir afixado localmente para camadas ou de camadas para localmente afixado (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   3. Aumentar a **aprovisionado capacidade**. O **capacidade aprovisionada** só pode ser aumentado. Não é possível encolher um volume depois de criado.
   4. Sob **anfitriões ligados**, pode modificar o ACR. Para modificar um ACR, o volume tem de ser offline.

       ![Reveja o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Clique em **guardar** para guardar as alterações. Quando lhe for pedida a confirmação, clique em **Sim**. O portal do Azure irá apresentar uma mensagem a atualização do volume. Esta será apresentada uma mensagem de êxito quando o volume tiver sido atualizado com êxito.

    ![Reveja o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se está a expandir um volume, conclua os seguintes passos no seu computador de anfitrião do Windows:
   
   1. Aceda a **gestão de computadores** ->**gestão de discos**.
   2. Com o botão direito **gestão de discos** e selecione **reanalisar discos**.
   3. Na lista de discos, selecione o volume que atualizar, botão direito do mouse e selecione **Expandir Volume**. O Assistente de expandir o Volume é iniciado. Clique em **Seguinte**.
   4. Conclua o assistente, aceitando os valores predefinidos. Depois de concluído o assistente, o volume deve mostrar o tamanho aumentado.
      
      > [!NOTE]
      > Se expandir um volume afixado localmente e, em seguida, expanda outro localmente afixado volume imediatamente depois disso, as tarefas de expansão do volume são executados sequencialmente. A primeira tarefa de expansão do volume tem de concluir antes de poderem começar a próxima tarefa de expansão do volume.
      

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Pode alterar o tipo de volume de camadas para localmente afixado ou de localmente afixado a em camadas. No entanto, esta conversão não deve ser uma ocorrência frequente.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Em camadas para considerações sobre conversão do local volume

Algumas razões para a conversão de um volume de camadas para localmente afixado são:

* Garantias locais em relação à disponibilidade de dados e o desempenho
* Eliminação de latências de nuvem e problemas de conectividade de cloud.

Normalmente, estes são pequenos volumes existentes que deseja acessar com frequência. Um volume localmente afixado esteja totalmente aprovisionado quando for criado. 

Se estiver a converter um volume em camadas para um volume localmente afixado, o StorSimple verifica que tem espaço suficiente no seu dispositivo antes de iniciar a conversão. Se tiver espaço suficiente, receberá um erro e a operação será cancelada. 

> [!NOTE]
> Antes de iniciar uma conversão de camadas para localmente afixado, certifique-se de que considere os requisitos de espaço das outras cargas de trabalho. 

Conversão de um em camadas para um volume afixado localmente pode afetar negativamente o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo que demora a concluir a conversão:

* Não existe largura de banda suficiente.
* Não existe nenhuma cópia de segurança atual.

Para minimizar os efeitos que podem ter esses fatores:

* Reveja as suas políticas de limitação de largura de banda e certifique-se de que um 40 Mbps largura de banda está disponível.
* Agende a conversão para horas de ponta.
* Tire um instantâneo de cloud antes de iniciar a conversão.

Se estiver a converter vários volumes (suportam diferentes cargas de trabalho), devem priorizar a conversão do volume para que os volumes de prioridade superior são convertidos em primeiro lugar. Por exemplo, deve converter volumes que alojam máquinas virtuais (VMs) ou volumes com cargas de trabalho do SQL antes de converter os volumes com cargas de trabalho de partilha de ficheiros.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Local para considerações sobre conversão do volume em camadas

Pode querer alterar um volume localmente afixado a um volume em camadas se precisar de espaço adicional para aprovisionar outros volumes. Ao converter o volume afixado localmente para camadas, a capacidade disponível nos aumentos de dispositivo pelo tamanho da capacidade do lançamento. Se a problemas de conectividade de impedir que a conversão de um volume do tipo local para o tipo em camadas, o volume local exibirão propriedades de um volume em camadas até a conversão está concluída. Isto acontece porque alguns dados poderão ter vazam para a cloud. Estes dados spilled continuam a ocupar espaço local no dispositivo que não pode ser liberado até que a operação é reiniciada e concluída.

> [!NOTE]
> Converter um volume pode demorar algum tempo e não é possível cancelar uma conversão depois de iniciar. O volume permanece online durante a conversão e pode fazer cópias de segurança, mas não é possível expandir ou restaurar o volume enquanto a conversão está ocorrendo.


#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Na lista tabular de volumes, selecione o volume e com o botão direito para invocar o menu de contexto. Selecione **modificar**.

    ![Selecione modificar no menu de contexto](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Sobre o **modificar o volume** painel, alterar o tipo de volume, selecionando o novo tipo da **tipo** na lista pendente.
   
   * Se estiver a alterar o tipo para **localmente afixado**, StorSimple verificará se há capacidade suficiente.
   * Se estiver a alterar o tipo para **em camadas** e este volume será utilizado para dados de arquivo, selecionados a **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação.
   * Se estiver a configurar um volume afixado localmente como em camadas ou _vice versa_, é apresentada a mensagem seguinte.
   
     ![Mensagem de tipo de volume de alterações](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Clique em **Guardar** para guardar as alterações. Quando lhe for pedida confirmação, clique em **Sim** para iniciar o processo de conversão. 

    ![Guardar e confirmar](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. O portal do Azure apresenta uma notificação para a criação de tarefa atualizará o volume. Clique na notificação para monitorizar o estado da tarefa de conversão do volume.

    ![Tarefas de conversão do volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Terá de colocar um volume offline quando estiver a planear modificar ou eliminar o volume. Quando um volume estiver offline, não está disponível para o acesso de leitura / escrita. Tem de executar o volume offline no anfitrião e o dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em utilização antes de colocar offline.
2. Coloque o volume offline no anfitrião primeiro. Isso elimina qualquer potencial risco de Corrupção de dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema de operativo anfitrião.
3. Depois do anfitrião estiver offline, coloque o volume no dispositivo offline, efetuando os seguintes passos:
   
    1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

        ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Na lista tabular de volumes, selecione o volume e com o botão direito para invocar o menu de contexto. Selecione **colocar offline** para colocar o volume irá modificar offline.

        ![Selecione e coloque o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na **colocar offline** painel, reveja o impacto de colocar offline o volume e selecione a caixa de seleção correspondente. Clique em **colocar offline**. 

    ![Reveja o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Será notificado quando o volume está offline. Também atualiza o estado do volume para Offline.
      
4. Depois de um volume offline, se selecionar o volume e o botão direito do mouse, **colocar Online** opção fica disponível no menu de contexto.

> [!NOTE]
> O **tirar Offline** comando envia um pedido para o dispositivo para colocar o volume offline. Se os anfitriões ainda estiver a utilizar o volume, isso resulta em conexões interrompidas, mas que o volume offline não irá falhar.

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Pode eliminar um volume apenas se está offline.

Conclua os passos seguintes para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verificar o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, colocá-lo offline pela primeira vez. Siga os passos em [colocar um volume offline](#take-a-volume-offline).
4. Depois do volume estiver offline, selecione o volume, clique com o botão direito para invocar o menu de contexto e, em seguida, selecione **eliminar**.

    ![Selecione eliminar no menu de contexto](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Na **eliminar** painel, reveja e selecione a caixa de verificação contra o impacto da eliminação de um volume. Quando elimina um volume, todos os dados que residem no volume são perdidos. 

    ![Guardar e confirme as alterações](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Depois de eliminar o volume, atualiza a lista tabular de volumes para indicar a eliminação.

    ![Lista atualizada de volume](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Device Manager atualiza periodicamente o espaço local disponível. Sugerimos que aguarde alguns minutos antes de tentar criar o novo volume.
   >
   > Além disso, se eliminar um volume afixado localmente e, em seguida, eliminar outro localmente afixado volume imediatamente depois disso, a eliminação do volume trabalhos são executados sequencialmente. A primeira tarefa de eliminação do volume tem de concluir antes de poderem começar a próxima tarefa de eliminação do volume.

## <a name="monitor-a-volume"></a>Monitorizar um número

Monitorização do volume permite-lhe recolher estatísticas de eu s relacionado/para um volume. A monitorização está ativada por predefinição para os primeiros 32 volumes que cria. Monitorização de volumes adicionais está desativada por predefinição. 

> [!NOTE]
> Monitorização de volumes clonados está desativada por predefinição.


Execute os seguintes passos para ativar ou desativar a monitorização para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização do volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.
2. Na lista tabular de volumes, selecione o volume e com o botão direito para invocar o menu de contexto. Selecione **modificar**.
3. Na **modificar o volume** painel, para **monitorização** selecionar **ativar** ou **desativar** para ativar ou desativar a monitorização.

    ![Desativar a monitorização](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Clique em **salvar** e quando lhe for pedida confirmação, clique em **Sim**. O portal do Azure apresenta uma notificação para atualizar o volume e, em seguida, uma mensagem de êxito, depois do volume é atualizado com êxito.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

