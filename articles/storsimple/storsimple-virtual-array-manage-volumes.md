---
title: Gerir volumes na matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve o StorSimple Device Manager e explica como usá-lo a gerenciar volumes em sua matriz Virtual StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125802"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Serviço de utilização StorSimple Device Manager para gerir volumes na matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir volumes em sua matriz Virtual StorSimple.

O serviço StorSimple Device Manager é uma extensão no portal do Azure que lhe permite gerir a solução StorSimple a partir de uma interface web único. Além da gestão de compartilhamentos e volumes, pode utilizar o serviço StorSimple Device Manager para ver e gerir dispositivos, ver alertas e ver e gerir políticas de cópia de segurança e o catálogo de cópia de segurança.

## <a name="volume-types"></a>Tipos de volume

Volumes do StorSimple podem ser:

* **Afixado localmente**: Os dados nestes volumes permanecem na matriz durante todo o tempo e não transbordam para a cloud.
* **Camadas**: Dados nestes volumes podem transbordam para a nuvem. Quando cria um volume em camadas, aproximadamente 10% do espaço é aprovisionado no escalão local e 90% do espaço é aprovisionado na cloud. Por exemplo, se tiver aprovisionado um volume de 1 TB, 100 GB seria residem no espaço de local e 900 GB seria usado na cloud quando as camadas de dados. Isso por sua vez, implica que se executar fora de todo o espaço local no dispositivo, não pode aprovisionar um volume em camadas (porque a 10% necessário no escalão local não estará disponível).

### <a name="provisioned-capacity"></a>Capacidade aprovisionada
Consulte a tabela seguinte para máxima capacidade aprovisionada para cada tipo de volume.

| **Identificador de limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Tamanho mínimo de um volume em camadas                            | 500 GB        |
| Tamanho máximo de um volume em camadas                            | 5 TB          |
| Tamanho mínimo de um volume afixado localmente                    | 50 GB         |
| Tamanho máximo de um volume afixado localmente                    | 500 GB        |

## <a name="the-volumes-blade"></a>O painel de Volumes
O **Volumes** menu no seu painel de resumo do serviço StorSimple apresenta a lista de volumes de armazenamento numa determinada matriz do StorSimple e permite-lhe geri-los.

![Painel de volumes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Um volume consiste numa série de atributos:

* **Nome do volume** – um nome descritivo, que tem de ser exclusivo e ajuda a identificar o volume.
* **Estado** – pode estar online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm acesso ao utilizar o volume.
* **Tipo** – indica se o volume está **em camadas** (predefinição) ou **localmente afixado**.
* **Capacidade** – Especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados pelo iniciador (servidor).
* **Cópia de segurança** – no caso da matriz Virtual StorSimple, todos os volumes são ativados automaticamente para cópia de segurança.
* **Anfitriões ligados** – Especifica os iniciadores (servidores) que têm permissão para aceder a este volume.

![Detalhes de volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar um volume
* Modificar um volume
* Colocar um volume offline
* Eliminar um volume

## <a name="add-a-volume"></a>Adicionar um volume

1. No painel Resumo do serviço do StorSimple, clique em **+ adicionar volume** na barra de comandos. Esta ação abre o **adicionar volume** painel.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Na **adicionar volume** painel, faça o seguinte:
   
   * Na **nome do Volume** , insira um nome exclusivo para o volume. O nome tem de ser uma cadeia que contém 3 e 127 carateres.
   * Na **tipo** lista pendente, especifique se pretende criar um **em camadas** ou **localmente afixado** volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e um desempenho mais elevado, selecione **localmente afixado volume**. Para todos os outros dados, selecione **em camadas** volume.
   * Na **capacidade** campo, especifique o tamanho do volume. Tem de ser um volume em camadas entre 500 GB e 5 TB e um volume localmente afixado tem de estar entre 50 GB e 500 GB.
   * * Clique em **anfitriões ligados**, selecione um registo de controlo de acesso (ACR) correspondente para o iniciador de iSCSI que pretende ligar a este volume e, em seguida, clique em **selecione**.
3. Para adicionar um novo anfitrião ligado, clique em **adicionar novo**, introduza um nome para o anfitrião e o iSCSI nome qualificado (IQN) e clique em **Add**.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Quando tiver terminado de configurar o volume, clique em **criar**. Será criado um volume com as definições especificadas e verá uma notificação sobre a criação bem-sucedida dos mesmos. Por predefinição, cópia de segurança irão estar ativada para o volume.
5. Para confirmar que o volume foi criado com êxito, vá para o **Volumes** painel. Deverá ver o volume listado.
   
    ![Volume criar com êxito](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando precisar de alterar os anfitriões que aceder ao volume. Não não possível modificar os outros atributos de um volume quando o volume tiver sido criado.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Partir do **Volumes** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o volume que pretende modificar.
2. **Selecione** do volume e clique em **anfitriões ligados** para ver o anfitrião atualmente ligado e modificá-lo para um servidor diferente.
   
    ![Editar o volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Guardar as alterações ao clicar o **guardar** barra de comandos. As definições especificadas serão aplicadas e verá uma notificação.

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Terá de colocar um volume offline quando estiver a planear para modificá-lo ou eliminá-lo. Quando um volume estiver offline, não está disponível para o acesso de leitura / escrita. Precisará tirar o volume offline no anfitrião, bem como no dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em utilização antes de colocar offline.
2. Coloque o volume offline no anfitrião primeiro. Isso elimina qualquer potencial risco de Corrupção de dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema de operativo anfitrião.
3. Depois do volume do anfitrião estiver offline, coloque o volume na matriz offline, efetuando os seguintes passos:
   
   * Partir do **Volumes** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o volume que pretende colocar offline.
   * **Selecione** do volume e clique em **...**  (em alternativa com o botão direito na linha) e no menu de contexto, selecione **colocar offline**.
     
        ![Offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Reveja as informações a **colocar offline** painel e confirme a sua aceitação da operação. Clique em **colocar offline** para colocar o volume offline. Verá uma notificação da operação em curso.
   * Para confirmar que o volume com êxito foi colocado offline, vá para o **Volumes** painel. Deverá ver o estado do volume como offline.
     
       ![Confirmação de offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Pode eliminar um volume apenas se está offline.
> 
> 

Conclua os passos seguintes para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Partir do **Volumes** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o volume que pretende eliminar.
2. **Selecione** do volume e clique em **...**  (em alternativa com o botão direito na linha) e no menu de contexto, selecione **eliminar**.
   
    ![Eliminação do volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verificar o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, colocá-lo offline em primeiro lugar, siga os passos descritos em [colocar um volume offline](#take-a-volume-offline).
4. Quando lhe for pedida confirmação no **elimine** painel, aceite a confirmação e clique em **eliminar**. O volume agora será eliminado e o **Volumes** painel mostrará a lista atualizada de volumes dentro da matriz virtual.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [clonar um volume StorSimple](storsimple-virtual-array-clone.md).

