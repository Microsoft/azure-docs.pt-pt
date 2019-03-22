---
title: Gerir partilhas de matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve o StorSimple Device Manager e explica como usá-lo para gerir partilhas em sua matriz Virtual StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092434"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Utilizar o serviço StorSimple Device Manager para gerir partilhas na matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir partilhas em sua matriz Virtual StorSimple.

O serviço StorSimple Device Manager é uma extensão no portal do Azure que lhe permite gerir a solução StorSimple a partir de uma interface web único. Além da gestão de compartilhamentos e volumes, pode utilizar o serviço StorSimple Device Manager para ver e gerir dispositivos, ver alertas, gerir políticas de cópia de segurança e gerir o catálogo de cópia de segurança.

## <a name="share-types"></a>Tipos de partilha

Partilhas de StorSimple podem ser:

* **Afixado localmente**: Os dados nestas partilhas permanecem na matriz durante todo o tempo e não transbordam para a cloud.
* **Camadas**: Dados nestas partilhas podem transbordam para a nuvem. Quando cria uma partilha em camadas, aproximadamente 10% do espaço é aprovisionado no escalão local e 90% do espaço é aprovisionado na cloud. Por exemplo, se tiver aprovisionado um compartilhamento de 1 TB, 100 GB seria residem no espaço de local e 900 GB seria usado na cloud quando as camadas de dados. Isso por sua vez, implica que se executar fora de todo o espaço local no dispositivo, não pode aprovisionar uma partilha em camadas (porque a 10% necessário no escalão local não estará disponível).

### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte a tabela seguinte para máxima capacidade aprovisionada para cada tipo de partilha.

| **Identificador de limite** | **Limite** |
| --- | --- |
| Tamanho mínimo de uma partilha em camadas |500 GB |
| Tamanho máximo de uma partilha em camadas |20 TB |
| Tamanho mínimo de uma partilha de afixado localmente |50 GB |
| Tamanho máximo de uma partilha de afixado localmente |2 TB |

## <a name="the-shares-blade"></a>O painel de partilhas

O **partilhas** menu no seu painel de resumo do serviço StorSimple é apresentada a lista de partilhas de armazenamento de uma determinada matriz do StorSimple e permite-lhe geri-los.

![Painel de partilhas](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Uma partilha consiste numa série de atributos:

* **Nome da partilha** – um nome descritivo, que tem de ser exclusivo e ajuda a identificar a partilha.
* **Estado** – pode estar online ou offline. Se uma partilha estiver offline, utilizadores da partilha não será capazes de aceder ao mesmo.
* **Tipo** – indica se a partilha está **em camadas** (predefinição) ou **localmente afixado**.
* **Capacidade** – Especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados na partilha.
* **Descrição** – uma definição opcional que o ajuda a descrever a partilha.
* **Permissões** -permissões de NTFS o para a partilha que podem ser geridas através do Explorador do Windows.
* **Cópia de segurança** – no caso da matriz Virtual StorSimple, todas as partilhas são ativadas automaticamente para cópia de segurança.

![Detalhes de partilhas](./media/storsimple-virtual-array-manage-shares/share-details.png)

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar uma partilha
* Modificar uma partilha
* Colocar uma partilha offline
* Eliminar uma partilha

## <a name="add-a-share"></a>Adicionar uma partilha

1. No painel Resumo do serviço do StorSimple, clique em **+ Adicionar partilha** na barra de comandos. Esta ação abre o **Adicionar partilha** painel.

    ![Adicionar partilha](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Na **Adicionar partilha** painel, faça o seguinte:
   
   1. Na **nome da partilha** , insira um nome exclusivo para a sua partilha. O nome tem de ser uma cadeia que contém 3 e 127 carateres.

   2. Opcional **Descrição** para a partilha. A descrição ajudará a identificar os proprietários de partilha.

   3. Na **tipo** lista pendente, especifique se pretende criar um **em camadas** ou **localmente afixado** partilhar. Para cargas de trabalho que necessitem de garantias locais, latências baixas e um desempenho mais elevado, selecione **localmente afixado partilha**. Para todos os outros dados, selecione **em camadas** partilhar.

   4. Na **capacidade** campo, especifique o tamanho da partilha. Tem de ser uma partilha em camadas entre 500 GB e 20 TB e um compartilhamento de localmente afixado tem de estar entre 50 GB e 2 TB.

   5. Na **predefinir todas as permissões** campo, atribua as permissões para o utilizador ou grupo que está a aceder a esta partilha. Especifique o nome do utilizador ou o grupo de utilizadores no _john@contoso.com_ formato. Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios de administrador para aceder a esses compartilhamentos. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
3. Quando tiver terminado de configurar a partilha, clique em **criar**. Será criado um compartilhamento com as definições especificadas e verá uma notificação. Por predefinição, será ativada para a partilha de cópia de segurança.
4. Para confirmar que a partilha foi criada com êxito, vá para o **partilhas** painel. Deverá ver a partilha listada.
   
    ![Partilha de criar com êxito](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificar uma partilha

Modificar uma partilha quando precisar de alterar a descrição da partilha. Não existem outras propriedades de partilha podem ser modificadas depois de criar a partilha.

#### <a name="to-modify-a-share"></a>Para modificar uma partilha

1. Partir do **partilhas** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside a partilha que pretende modificar.
2. **Selecione** a partilha para exibir a descrição do atual e modificá-lo.
3. Guardar as alterações ao clicar o **guardar** barra de comandos. As definições especificadas serão aplicadas e verá uma notificação.
   
    ![ Editar partilha](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Colocar uma partilha offline

Terá de colocar uma partilha offline quando estiver a planear para modificá-lo ou eliminá-lo. Quando uma partilha estiver offline, não está disponível para o acesso de leitura / escrita. Terá de levar a partilha offline no anfitrião, bem como no dispositivo.

#### <a name="to-take-a-share-offline"></a>Para colocar uma partilha offline

1. Certifique-se de que a partilha em questão não está a ser utilizada antes de colocar offline.
2. Colocar a partilha na matriz offline, efetuando os seguintes passos:
   
    1. Partir do **partilhas** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside a partilha que pretende colocar offline.

    2. **Selecione** de partilha e clique em **...**  (em alternativa com o botão direito na linha) e no menu de contexto, selecione **colocar offline**.
     
        ![Partilha offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Reveja as informações a **colocar offline** painel e confirme a sua aceitação da operação. Clique em **colocar offline** para colocar a partilha offline. Verá uma notificação da operação em curso.

    4. Para confirmar que a partilha com êxito foi colocada offline, vá para o **partilhas** painel. Deverá ver o estado da partilha de como offline.

## <a name="delete-a-share"></a>Eliminar uma partilha

> [!IMPORTANT]
> Pode eliminar uma partilha de apenas se está offline.


Conclua os passos seguintes para eliminar uma partilha.

#### <a name="to-delete-a-share"></a>Eliminar uma partilha

1. Do **partilhas** definição no painel de resumo do serviço StorSimple, selecione a matriz virtual no qual reside a partilha que quer eliminar.
2. **Selecione** de partilha e clique em **...**  (em alternativa com o botão direito na linha) e no menu de contexto, selecione **eliminar**.
   
    ![Eliminar partilha](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verificar o estado da partilha que pretende eliminar. Se a partilha que pretende eliminar não estiver offline, colocá-lo offline pela primeira vez. Siga os passos em [colocar uma partilha offline](#take-a-share-offline).
4. Quando lhe for pedida confirmação no **elimine** painel, aceite a confirmação e clique em **eliminar**. A partilha agora será eliminada e o **partilhas** painel mostra a lista atualizada de partilhas de dentro da matriz virtual.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [clonar um compartilhamento de StorSimple](storsimple-virtual-array-clone.md).

