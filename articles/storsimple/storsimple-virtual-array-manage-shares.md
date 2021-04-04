---
title: Gerir ações da StorSimple Virtual Array | Microsoft Docs
description: Descreve o StorSimple Device Manager e explica como usá-lo para gerir ações no seu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994949"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de Dispositivos do StorSimple para gerir partilhas no StorSimple Virtual Array

## <a name="overview"></a>Descrição Geral

Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir ações no seu StorSimple Virtual Array.

O serviço StorSimple Device Manager é uma extensão no portal Azure que permite gerir a sua solução StorSimple a partir de uma única interface web. Além de gerir ações e volumes, pode utilizar o serviço StorSimple Device Manager para visualizar e gerir dispositivos, ver alertas, gerir políticas de backup e gerir o catálogo de backup.

## <a name="share-types"></a>Tipos de partilha

As ações StorSimple podem ser:

* **Localmente fixados**: Os dados nestas ações permanecem sempre na matriz e não derramam para a nuvem.
* **Tiered**: Os dados nestas ações podem derramar para a nuvem. Quando se cria uma quota hierárquica, cerca de 10 % do espaço é a provisionado no nível local e 90 % do espaço é a provisionado na nuvem. Por exemplo, se fornecesse uma quota de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que, se ficar sem todo o espaço local do dispositivo, não poderá disponibilizar uma parte diferenciada (porque os 10 % exigidos no nível local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade a provisionada

Consulte o quadro seguinte para obter a capacidade máxima a provisionada para cada tipo de ação.

| **Identificador de limite** | **Limite** |
| --- | --- |
| Tamanho mínimo de uma parte hierárquico |500 GB |
| Tamanho máximo de uma parte hierárquico |20 TB |
| Tamanho mínimo de uma parte fixa local |50 GB |
| Tamanho máximo de uma parte fixa local |2 TB |

## <a name="the-shares-blade"></a>A lâmina de partilha

O menu **Partilhas** na sua lâmina de resumo de serviço StorSimple exibe a lista de partilhas de armazenamento numa determinada matriz StorSimple e permite-lhe geri-las.

![Lâmina de partilha](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Uma ação é constituída por uma série de atributos:

* **Partilhar Nome** – Um nome descritivo que deve ser único e ajuda a identificar a partilha.
* **Estado** – Pode estar online ou offline. Se uma parte estiver offline, os utilizadores da parte não poderão aceder à sua parte.
* **Tipo** – Indica se a ação é **Tiered** (o padrão) ou **fixado localmente**.
* **Capacidade** – especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados na parte.
* **Descrição** – Uma definição opcional que ajuda a descrever a partilha.
* **Permissões** - As permissões NTFS para a partilha que podem ser geridas através do Windows Explorer.
* **Backup** – No caso do StorSimple Virtual Array, todas as ações são automaticamente ativadas para cópia de segurança.

![Ações detalhes](./media/storsimple-virtual-array-manage-shares/share-details.png)

Utilize as instruções deste tutorial para executar as seguintes tarefas:

* Adicionar uma partilha
* Modificar uma parte
* Tome uma parte offline
* Eliminar uma partilha

## <a name="add-a-share"></a>Adicionar uma partilha

1. A partir da lâmina de resumo do serviço StorSimple, clique **+ Adicione a partilha** da barra de comando. Isto abre a lâmina **de partilha Add.**

    ![Adicionar ação](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Na lâmina **de partilha Add,** faça o seguinte:
   
   1. No campo **de nomes Share,** insira um nome único para a sua parte. O nome deve ser uma corda que contenha 3 a 127 caracteres.

   2. Uma **descrição** opcional para a parte. A descrição ajudará a identificar os proprietários de ações.

   3. Na lista de dropdown **do Tipo,** especifique se deve criar uma quota **tiered** ou **localmente fixada.** Para cargas de trabalho que requerem garantias locais, baixas latências e maior desempenho, selecione **ações fixas locais.** Para todos os outros dados, selecione **Tiered** share.

   4. No campo **Capacidade,** especifique o tamanho da parte. Uma quota hierárquica deve estar entre 500 GB e 20 TB e uma quota fixa local deve estar entre 50 GB e 2 TB.

   5. Nas **permissões completas do Conjunto para** o campo, atribua as permissões ao utilizador ou ao grupo que está a aceder a esta partilha. Especifique o nome do utilizador ou do grupo de utilizador em _john@contoso.com_ formato. Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios de administração acedam a estas partilhas. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
3. Quando terminar de configurar a sua parte, clique em **Criar**. Será criada uma partilha com as definições especificadas e verá uma notificação. Por predefinição, a cópia de segurança será ativada para a partilha.
4. Para confirmar que a ação foi criada com sucesso, vá para a lâmina **shares.** Devia ver a cotação.
   
    ![Partilhar criar sucesso](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificar uma parte

Modifique uma ação quando precisar de alterar a descrição da parte. Nenhuma outra propriedade de ações pode ser modificada uma vez que a ação é criada.

#### <a name="to-modify-a-share"></a>Para modificar uma parte

1. A partir da definição **de Ações** na lâmina de resumo do serviço StorSimple, selecione o conjunto virtual em que a partilha que deseja modificar reside.
2. **Selecione** a partilha para ver a descrição atual e modificá-la.
3. Guarde as suas alterações clicando na barra de comando **Save.** As suas definições especificadas serão aplicadas e verá uma notificação.
   
    ![ Editar partilha](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Tome uma parte offline

Poderá ter de retirar uma parte offline quando planeia modificá-la ou eliminá-la. Quando uma partilha está offline, não está disponível para acesso de leitura-escrita. Terá de desligar a partilha do anfitrião, bem como do dispositivo.

#### <a name="to-take-a-share-offline"></a>Para tirar uma parte offline

1. Certifique-se de que a parte em questão não está a ser utilizada antes de a desligar.
2. Tome a parte da matriz offline executando os seguintes passos:
   
    1. A partir da definição **de Partilhas** na lâmina de resumo do serviço StorSimple, selecione o conjunto virtual em que reside a partilha em que deseja tirar offline.

    2. **Selecione** a partilha e clique **...** (alternadamente clique à direita nesta linha) e a partir do menu de contexto, selecione **'Tirar offline'.**
     
        ![Parte offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Reveja as informações na lâmina **offline Take** e confirme a sua aceitação da operação. Clique **em Desligar** offline para tirar a partilha offline. Verá uma notificação da operação em curso.

    4. Para confirmar que a ação foi tirada com sucesso offline, vá para a lâmina **shares.** Devia ver o estado da parte como offline.

## <a name="delete-a-share"></a>Eliminar uma partilha

> [!IMPORTANT]
> Só pode excluir uma ação se estiver offline.


Preencha os seguintes passos para eliminar uma partilha.

#### <a name="to-delete-a-share"></a>Para apagar uma parte

1. A partir da definição **de Partilhas** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside a partilha em que a partilha deseja eliminar.
2. **Selecione** a partilha e clique **...** (alternadamente clique à direita nesta linha) e a partir do menu de contexto, selecione **Delete**.
   
    ![Apagar ações](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verifique o estado da parte que pretende eliminar. Se a parte que pretende eliminar não estiver offline, desative-a primeiro. Siga os passos em [Take a share offline](#take-a-share-offline).
4. Quando solicitado para confirmação na lâmina **eliminar,** aceite a confirmação e clique em **Eliminar**. A partilha será agora eliminada e a lâmina **de partilha** mostra a lista atualizada de ações dentro da matriz virtual.

## <a name="next-steps"></a>Passos seguintes
Saiba como [clonar uma partilha StorSimple](storsimple-virtual-array-clone.md).

