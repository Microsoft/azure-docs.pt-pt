---
title: Gerir as ações da StorSimple Virtual Array [ StorSimple Virtual Array] Microsoft Docs
description: Descreve o StorSimple Device Manager e explica como usá-lo para gerir ações no seu StorSimple Virtual Array.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "62116871"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de Dispositivos do StorSimple para gerir partilhas no StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este tutorial explica como usar o serviço StorSimple Device Manager para criar e gerir ações no seu StorSimple Virtual Array.

O serviço StorSimple Device Manager é uma extensão no portal Azure que permite gerir a sua solução StorSimple a partir de uma única interface web. Além de gerir ações e volumes, pode utilizar o serviço StorSimple Device Manager para visualizar e gerir dispositivos, visualizar alertas, gerir políticas de backup e gerir o catálogo de backup.

## <a name="share-types"></a>Tipos de partilha

As ações StorSimple podem ser:

* **Localmente fixado**: Os dados destas ações permanecem sempre na matriz e não derramam para a nuvem.
* **Tiered**: Os dados destas ações podem derramar para a nuvem. Quando se cria uma quota hierárquica, aproximadamente 10 % do espaço é aprovisionado no nível local e 90 % do espaço é aprovisionado na nuvem. Por exemplo, se você forprovisionado uma quota de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os níveis de dados. Isto, por sua vez, implica que, se ficar sem todo o espaço local do dispositivo, não pode fornecer uma parte diferenciada (porque os 10 % exigidos no nível local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte o quadro seguinte para obter a máxima capacidade prevista para cada tipo de ação.

| **Identificador de limite** | **Limite** |
| --- | --- |
| Tamanho mínimo de uma parte hierárquico |500 GB |
| Tamanho máximo de uma parte hierárquico |20 TB |
| Tamanho mínimo de uma parte fixada localmente |50 GB |
| Tamanho máximo de uma parte fixada localmente |2 TB |

## <a name="the-shares-blade"></a>A lâmina das ações

O menu **Partilhas** na sua lâmina de resumo do serviço StorSimple exibe a lista de partilhas de armazenamento numa dada matriz StorSimple e permite-lhe geri-las.

![Partilha lâmina](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Uma parte consiste numa série de atributos:

* **Nome partilha** – Um nome descritivo que deve ser único e ajuda a identificar a partilha.
* **Estado** – Pode estar online ou offline. Se uma parte estiver offline, os utilizadores da parte não poderão aceder à sua participação.
* **Tipo** – Indica se a parte está **nivida** (a predefinição) ou **fixada localmente**.
* **Capacidade** – especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados na parte.
* **Descrição** – Uma definição opcional que ajuda a descrever a partilha.
* **Permissões** - As permissões NTFS para a parte que podem ser geridas através do Windows Explorer.
* **Backup** – No caso do StorSimple Virtual Array, todas as ações estão automaticamente ativadas para cópia de segurança.

![Detalhes das partilhas](./media/storsimple-virtual-array-manage-shares/share-details.png)

Utilize as instruções deste tutorial para executar as seguintes tarefas:

* Adicionar uma partilha
* Modificar uma parte
* Tome uma parte offline
* Eliminar uma partilha

## <a name="add-a-share"></a>Adicionar uma partilha

1. A partir da lâmina de resumo do serviço StorSimple, clique **+ adicione a partir** da barra de comando. Isto abre a lâmina **de partilha Add.**

    ![Adicionar partilha](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Na lâmina **de partilha Add,** faça o seguinte:
   
   1. No campo **de nome partilhar,** insira um nome único para a sua parte. O nome deve ser uma corda que contenha 3 a 127 caracteres.

   2. Uma **descrição** opcional para a parte. A descrição ajudará a identificar os proprietários de ações.

   3. Na lista de abandono do **Tipo,** especifique se criar uma parte **hierárquico** ou **fixada localmente.** Para cargas de trabalho que requerem garantias locais, baixas llátências e maior desempenho, selecione **partilha fixada localmente.** Para todos os outros dados, selecione a parte **Tiered.**

   4. No campo **capacidade,** especifique o tamanho da parte. Uma parte hierárquica deve ser entre 500 GB e 20 TB e uma parte fixada localmente deve ser entre 50 GB e 2 TB.

   5. No **Conjunto predefinido permissões completas para** o campo, atribua as permissões ao utilizador ou ao grupo que está a aceder a esta partilha. Especifique o nome do _john@contoso.com_ utilizador ou do grupo utilizador em formato. Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios administrativos acedam a estas ações. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
3. Quando terminar de configurar a sua parte, clique em **Criar**. Será criada uma parte com as definições especificadas e verá uma notificação. Por defeito, a cópia de segurança será ativada para a partilha.
4. Para confirmar que a ação foi criada com sucesso, vá à lâmina **das Partilhas.** Devia ver a parte listada.
   
    ![Partilhar criar sucesso](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificar uma parte

Modifique uma parte quando precisar de alterar a descrição da parte. Nenhuma outra propriedade de ações pode ser modificada uma vez que a parte é criada.

#### <a name="to-modify-a-share"></a>Modificar uma parte

1. A partir da definição de **Partilhas** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que a partilha que pretende modificar reside.
2. **Selecione** a parte para ver a descrição atual e modificá-la.
3. Guarde as suas alterações clicando na barra de comando **Guardar.** As definições especificadas serão aplicadas e verá uma notificação.
   
    ![ Editar parte](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Tome uma parte offline

Pode ter de retirar uma parte offline quando planeia modificá-la ou eliminá-la. Quando uma ação está offline, não está disponível para acesso de leitura- escrita. Terá de desligar a parte do anfitrião, bem como no dispositivo.

#### <a name="to-take-a-share-offline"></a>Para tirar uma parte offline

1. Certifique-se de que a parte em questão não está a ser utilizada antes de a desligar.
2. Tome a parte da matriz offline executando os seguintes passos:
   
    1. A partir da definição de **Partilhas** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que a partilha que deseja desconhecer reside.

    2. **Selecione** a parte e clique **...** (clique alternadamente à direita nesta linha) e a partir do menu de contexto, selecione **Desligar**.
     
        ![Ação offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Reveja as informações na lâmina **Desativada** e confirme a sua aceitação da operação. Clique **em Desligar** para tirar a parte offline. Verá uma notificação da operação em curso.

    4. Para confirmar que a ação foi retirada com sucesso, vá à lâmina **das Ações.** Deviaver o estado da parte como offline.

## <a name="delete-a-share"></a>Eliminar uma partilha

> [!IMPORTANT]
> Só pode eliminar uma ação se estiver offline.


Complete os seguintes passos para eliminar uma parte.

#### <a name="to-delete-a-share"></a>Para apagar uma parte

1. A partir da definição **de Partilhas** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que a parte que pretende eliminar reside.
2. **Selecione** a parte e clique **...** (clique alternadamente à direita nesta linha) e a partir do menu de contexto, selecione **Delete**.
   
    ![Eliminar a partilha](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verifique o estado da partilha que pretende eliminar. Se a parte que pretende eliminar não estiver offline, desative-a primeiro. Siga os passos em [Tomar uma parte offline](#take-a-share-offline).
4. Quando solicitado para confirmação na lâmina **Apagar,** aceite a confirmação e clique em **Apagar**. A ação será agora eliminada e a lâmina **das Ações** mostra a lista atualizada de ações dentro da matriz virtual.

## <a name="next-steps"></a>Passos seguintes
Aprenda a [clonar uma partilha StorSimple](storsimple-virtual-array-clone.md).

