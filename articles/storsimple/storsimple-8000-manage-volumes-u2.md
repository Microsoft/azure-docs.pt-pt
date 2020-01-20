---
title: Gerenciar volumes do StorSimple (atualização 3)
description: Explica como adicionar, modificar, monitorar e excluir volumes do StorSimple e como levá-los offline, se necessário.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278833"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Usar o serviço de Device Manager do StorSimple para gerenciar volumes (atualização 3 ou posterior)

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço de Device Manager do StorSimple para criar e gerenciar volumes nos dispositivos da série StorSimple 8000 que executam a atualização 3 e posterior.

O serviço StorSimple Device Manager é uma extensão no portal do Azure que permite que você gerencie sua solução StorSimple de uma única interface da Web. Use o portal do Azure para gerenciar volumes em todos os seus dispositivos. Você também pode criar e gerenciar serviços do StorSimple, gerenciar dispositivos, políticas de backup e catálogo de backup e exibir alertas.

## <a name="volume-types"></a>Tipos de volume

Os volumes do StorSimple podem ser:

* **Volumes fixados localmente**: os dados nesses volumes permanecem no dispositivo StorSimple local em todos os momentos.
* **Volumes em camadas**: os dados nesses volumes podem despejar para a nuvem.

Um volume de arquivamento é um tipo de volume em camadas. O tamanho de parte maior de eliminação de duplicação usado para volumes de arquivamento permite que o dispositivo transfira segmentos maiores de dados para a nuvem.

Se necessário, você pode alterar o tipo de volume de local para em camadas ou de em camadas para local. Para obter mais informações, vá para [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>volumes afixados localmente

Volumes fixados localmente são volumes totalmente provisionados que não fazem a camada de dados para a nuvem, garantindo, assim, garantias locais para dados primários, independentemente da conectividade de nuvem. Os dados em volumes fixados localmente não têm eliminação de duplicação e são compactados; no entanto, os instantâneos de volumes fixados localmente têm eliminação de duplicação. 

Volumes localmente afixados são totalmente provisionados; Portanto, você deve ter espaço suficiente em seu dispositivo ao criá-los. Você pode provisionar volumes localmente afixados até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. O StorSimple reserva o espaço local restante no dispositivo para instantâneos, metadados e processamento de dados. Você pode aumentar o tamanho de um volume fixado localmente para o espaço máximo disponível, mas não pode diminuir o tamanho de um volume depois de criado.

Quando você cria um volume fixado localmente, o espaço disponível para a criação de volumes em camadas é reduzido. O inverso também é verdadeiro: se você tiver volumes em camadas existentes, o espaço disponível para a criação de volumes fixados localmente será menor do que os limites máximos indicados acima. Para obter mais informações sobre volumes locais, consulte as [perguntas frequentes sobre volumes localmente afixados](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumes escalonados

Os volumes em camadas são volumes com provisionamento dinâmico nos quais os dados acessados com frequência permanecem locais no dispositivo e os dados usados com menos frequência são automaticamente enfileirados para a nuvem. O provisionamento dinâmico é uma tecnologia de virtualização na qual o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa provisionamento dinâmico para alocar espaço suficiente para atender aos requisitos atuais. A natureza elástica do armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas em constante mudança.

Se você estiver usando o volume em camadas para dados de arquivamento, marque a caixa de seleção **usar este volume para dados de arquivamento acessados com menos frequência** para alterar o tamanho da parte de eliminação de duplicação do seu volume para 512 KB. Se você não selecionar essa opção, o volume em camadas correspondente usará um tamanho de bloco de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.


### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte a tabela a seguir para obter a capacidade máxima provisionada para cada tipo de dispositivo e volume. (Observe que os volumes localmente afixados não estão disponíveis em um dispositivo virtual.)

|  | Tamanho máximo do volume em camadas | Tamanho máximo do volume fixado localmente |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuais** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>A folha volumes

A folha **volumes** permite que você gerencie os volumes de armazenamento que são provisionados no dispositivo Microsoft Azure StorSimple para seus iniciadores (servidores). Ele exibe a lista de volumes nos dispositivos StorSimple conectados ao seu serviço.

 ![Página volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Um volume consiste em uma série de atributos:

* **Nome do volume** – um nome descritivo que deve ser exclusivo e ajuda a identificar o volume. Esse nome também é usado em relatórios de monitoramento quando você filtra em um volume específico. Depois que o volume é criado, ele não pode ser renomeado.
* **Status** – pode ser online ou offline. Se um volume estiver offline, ele não será visível aos iniciadores (servidores) que têm permissão de acesso para usar o volume.
* **Capacidade** – especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Os volumes fixados localmente são totalmente provisionados e residem no dispositivo StorSimple. Os volumes em camadas são provisionados de thin e os dados têm eliminação de duplicação. Com volumes com provisionamento dinâmico, seu dispositivo não aloca a capacidade de armazenamento físico de fato internamente ou na nuvem de acordo com a capacidade de volume configurada. A capacidade do volume é alocada e consumida sob demanda.
* **Tipo** – indica se o volume é em **camadas** (o padrão) ou **fixado localmente**.

Use as instruções neste tutorial para executar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Excluir um volume 
* Colocar um volume offline 
* Monitorar um volume 

## <a name="add-a-volume"></a>Adicionar um volume

Você [criou um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implantação do seu dispositivo StorSimple da série 8000. A adição de um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione o seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. No painel **Adicionar um volume**:
   
    1. O campo **Selecionar dispositivo** é preenchido automaticamente com o seu dispositivo atual.

    2. Na lista pendente, selecione o contentor de volumes onde precisa de adicionar um volume.

    3.  Escreva um **Nome** para o volume. Depois que o volume for criado, você não poderá renomear o volume.

    4. Na lista pendente, selecione o **Tipo** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**.
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
       
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo.

    5. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, é reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não pode criar nenhum volume escalonado a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume escalonado. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.

    6. No campo **Anfitriões ligados**, clique na seta. Na folha **hosts conectados** , escolha um ACR existente ou adicione um novo ACR. Se você escolher um novo ACR, forneça um **nome** para o ACR, forneça o **nome qualificado do iSCSI** (iqn) do seu host do Windows. Se você não tiver o IQN, vá para obter o IQN de um host do Windows Server. Clique em **Criar**. É criado um volume com as definições especificadas.

        ![Clique em Criar](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Seu novo volume agora está pronto para uso.

> [!NOTE]
> Se você criar um volume fixado localmente e, em seguida, criar outro volume localmente afixado imediatamente, os trabalhos de criação de volume são executados em sequência. O primeiro trabalho de criação de volume deve ser concluído antes que o próximo trabalho de criação de volume possa começar.

## <a name="modify-a-volume"></a>Modificar um volume

Modifique um volume quando precisar expandi-lo ou altere os hosts que acessam o volume.

> [!IMPORTANT]
> * Se você modificar o tamanho do volume no dispositivo, o tamanho do volume também precisará ser alterado no host.
> * As etapas do lado do host descritas aqui são para o Windows Server 2012 (2012R2). Os procedimentos para Linux ou outros sistemas operacionais de host serão diferentes. Consulte as instruções do sistema operacional do host ao modificar o volume em um host que executa outro sistema operacional.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **configurações > volumes**.

    ![Ir para a folha volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **colocar offline** para pegar o volume que será modificado offline.

    ![Selecionar e colocar volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na folha **colocar offline** , examine o impacto de colocar o volume offline e marque a caixa de seleção correspondente. Verifique se o volume correspondente no host está offline primeiro. Para obter informações sobre como colocar um volume offline no servidor host conectado ao StorSimple, consulte as instruções específicas do sistema operacional. Clique em **colocar offline**.

    ![Examinar o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Depois que o volume estiver offline (conforme mostrado pelo status do volume), selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar volume**.

    ![Selecione Modificar volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Na folha **Modificar volume** , você pode fazer as seguintes alterações:
   
   1. O **nome** do volume não pode ser editado.
   2. Converter o **tipo** de fixado localmente em em camadas ou de em camadas para localmente afixado (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   3. Aumente a **capacidade provisionada**. A **capacidade provisionada** só pode ser aumentada. Não é possível reduzir um volume depois que ele é criado.
   4. Em **hosts conectados**, você pode modificar o ACR. Para modificar um ACR, o volume deve estar offline.

       ![Examinar o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Clique em **guardar** para guardar as alterações. Quando lhe for pedida a confirmação, clique em **Sim**. O portal do Azure exibirá uma mensagem de atualização de volume. Ele exibirá uma mensagem de êxito quando o volume tiver sido atualizado com êxito.

    ![Examinar o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se você estiver expandindo um volume, conclua as seguintes etapas no computador host do Windows:
   
   1. Acesse **Gerenciamento do computador** ->**Gerenciamento de disco**.
   2. Clique com o botão direito do mouse em **Gerenciamento de disco** e selecione **examinar discos novamente**.
   3. Na lista de discos, selecione o volume que você atualizou, clique com o botão direito do mouse e selecione **estender volume**. O assistente para estender volume é iniciado. Clique em **Seguinte**.
   4. Conclua o assistente, aceitando os valores padrão. Depois que o assistente for concluído, o volume deverá mostrar o tamanho aumentado.
      
      > [!NOTE]
      > Se você expandir um volume fixado localmente e, em seguida, expandir outro volume localmente afixado imediatamente, os trabalhos de expansão de volume são executados em sequência. O primeiro trabalho de expansão de volume deve ser concluído antes que o próximo trabalho de expansão de volume possa começar.
      

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Você pode alterar o tipo de volume de em camadas para fixado localmente ou de fixado localmente para em camadas. No entanto, essa conversão não deve ser uma ocorrência frequente.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Considerações de conversão de volume em camadas para local

Alguns motivos para converter um volume de em camadas para fixadas localmente são:

* Garantias locais relacionadas à disponibilidade e ao desempenho dos dados
* Eliminação de latências de nuvem e problemas de conectividade de nuvem.

Normalmente, esses são volumes pequenos existentes que você deseja acessar com frequência. Um volume fixado localmente é totalmente provisionado quando é criado. 

Se você estiver convertendo um volume em camadas para um volume fixado localmente, o StorSimple verificará se você tem espaço suficiente em seu dispositivo antes de iniciar a conversão. Se você não tiver espaço suficiente, receberá um erro e a operação será cancelada. 

> [!NOTE]
> Antes de começar uma conversão de em camadas para fixada localmente, certifique-se de considerar os requisitos de espaço de suas outras cargas de trabalho. 

A conversão de uma em camadas em um volume fixado localmente pode afetar negativamente o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo necessário para concluir a conversão:

* Não há largura de banda suficiente.
* Não há backup atual.

Para minimizar os efeitos que esses fatores podem ter:

* Examine as políticas de limitação de largura de banda e verifique se há uma largura de banda dedicada de 40 Mbps disponível.
* Agende a conversão para horários de pico.
* Faça um instantâneo de nuvem antes de iniciar a conversão.

Se você estiver convertendo vários volumes (com suporte para cargas de trabalho diferentes), deverá priorizar a conversão de volume para que os volumes de prioridade mais alta sejam convertidos primeiro. Por exemplo, você deve converter volumes que hospedam VMs (máquinas virtuais) ou volumes com cargas de trabalho do SQL antes de converter volumes com cargas de trabalho de compartilhamento de arquivos.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Considerações de conversão de volume local para em camadas

Talvez você queira alterar um volume fixado localmente para um volume em camadas se precisar de espaço adicional para provisionar outros volumes. Quando você converte o volume fixado localmente em camadas, a capacidade disponível no dispositivo aumenta pelo tamanho da capacidade liberada. Se os problemas de conectividade impedirem a conversão de um volume do tipo local para o tipo em camadas, o volume local exibirá as propriedades de um volume em camadas até que a conversão seja concluída. Isso ocorre porque alguns dados podem ter sido despejados para a nuvem. Esses dados despejados continuam ocupando o espaço local no dispositivo que não pode ser liberado até que a operação seja reiniciada e concluída.

> [!NOTE]
> A conversão de um volume pode levar algum tempo e não é possível cancelar uma conversão depois que ela é iniciada. O volume permanece online durante a conversão e você pode fazer backups, mas não pode expandir ou restaurar o volume enquanto a conversão está ocorrendo.


#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **configurações > volumes**.

    ![Ir para a folha volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar**.

    ![Selecione Modificar no menu de contexto](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na folha **Modificar volume** , altere o tipo de volume selecionando o novo tipo na lista suspensa **tipo** .
   
   * Se você estiver alterando o tipo para **fixado localmente**, o StorSimple verificará se há capacidade suficiente.
   * Se você estiver alterando o tipo para em **camadas** e esse volume for usado para dados de arquivamento, marque a caixa de seleção **usar este volume para dados de arquivamento acessados com menos frequência** .
   * Se você estiver configurando um volume fixado localmente como em camadas ou _vice-versa_, a mensagem a seguir será exibida.
   
     ![Alterar mensagem do tipo de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Clique em **Guardar** para guardar as alterações. Quando a confirmação for solicitada, clique em **Sim** para iniciar o processo de conversão. 

    ![Salvar e confirmar](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. O portal do Azure exibe uma notificação para a criação do trabalho que atualizaria o volume. Clique na notificação para monitorar o status do trabalho de conversão de volume.

    ![Trabalho para conversão de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Talvez seja necessário colocar um volume offline quando você estiver planejando modificar ou excluir o volume. Quando um volume está offline, ele não está disponível para acesso de leitura/gravação. Você deve colocar o volume offline no host e no dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não esteja em uso antes de colocá-lo offline.
2. Coloque o volume offline no host primeiro. Isso elimina qualquer risco potencial de corrupção de dados no volume. Para obter etapas específicas, consulte as instruções para o sistema operacional do host.
3. Depois que o host estiver offline, coloque o volume no dispositivo offline executando as seguintes etapas:
   
    1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **configurações > volumes**.

        ![Ir para a folha volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **colocar offline** para pegar o volume que será modificado offline.

        ![Selecionar e colocar volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Na folha **colocar offline** , examine o impacto de colocar o volume offline e marque a caixa de seleção correspondente. Clique em **colocar offline**. 

    ![Examinar o impacto de colocar o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Você será notificado quando o volume estiver offline. O status do volume também é atualizado para offline.
      
4. Depois que um volume estiver offline, se você selecionar o volume e clicar com o botão direito do mouse, a opção **colocar online** ficará disponível no menu de contexto.

> [!NOTE]
> O comando **colocar offline** envia uma solicitação ao dispositivo para colocar o volume offline. Se os hosts ainda estiverem usando o volume, isso resultará em conexões interrompidas, mas colocar o volume offline não falhará.

## <a name="delete-a-volume"></a>Excluir um volume

> [!IMPORTANT]
> Você só poderá excluir um volume se ele estiver offline.

Conclua as etapas a seguir para excluir um volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **configurações > volumes**.

    ![Ir para a folha volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verifique o status do volume que você deseja excluir. Se o volume que você deseja excluir não estiver offline, coloque-o offline primeiro. Siga as etapas em [colocar um volume offline](#take-a-volume-offline).
4. Depois que o volume estiver offline, selecione o volume, clique com o botão direito do mouse para invocar o menu de contexto e, em seguida, selecione **excluir**.

    ![Selecionar Excluir no menu de contexto](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Na folha **excluir** , examine e marque a caixa de seleção em relação ao impacto da exclusão de um volume. Quando você exclui um volume, todos os dados que residem no volume são perdidos. 

    ![Salvar e confirmar as alterações](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Depois que o volume é excluído, a lista tabular de volumes é atualizada para indicar a exclusão.

    ![Lista de volumes atualizada](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se você excluir um volume fixado localmente, o espaço disponível para novos volumes poderá não ser atualizado imediatamente. O serviço StorSimple Device Manager atualiza o espaço local disponível periodicamente. Sugerimos que você aguarde alguns minutos antes de tentar criar o novo volume.
   >
   > Além disso, se você excluir um volume fixado localmente e, em seguida, excluir outro volume localmente afixado imediatamente, os trabalhos de exclusão de volume são executados em sequência. O primeiro trabalho de exclusão de volume deve ser concluído antes que o próximo trabalho de exclusão de volume possa começar.

## <a name="monitor-a-volume"></a>Monitorar um volume

O monitoramento de volume permite coletar estatísticas relacionadas a e/s de um volume. O monitoramento é habilitado por padrão para os primeiros 32 volumes que você cria. O monitoramento de volumes adicionais está desabilitado por padrão. 

> [!NOTE]
> O monitoramento de volumes clonados está desabilitado por padrão.


Execute as etapas a seguir para habilitar ou desabilitar o monitoramento de um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar ou desabilitar o monitoramento de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione o dispositivo que tem o volume que você pretende modificar. Clique em **configurações > volumes**.
2. Na listagem tabular dos volumes, selecione o volume e clique com o botão direito do mouse para invocar o menu de contexto. Selecione **Modificar**.
3. Na folha **Modificar volume** , para **monitoramento** , selecione **habilitar** ou **desabilitar** para habilitar ou desabilitar o monitoramento.

    ![Desativar a monitorização](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Clique em **salvar** e, quando a confirmação for solicitada, clique em **Sim**. O portal do Azure exibe uma notificação para atualizar o volume e, em seguida, uma mensagem de êxito, depois que o volume é atualizado com êxito.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [clonar um volume do StorSimple](storsimple-8000-clone-volume-u2.md).
* Saiba como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).

