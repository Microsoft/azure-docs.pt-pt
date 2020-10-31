---
title: Gerir volumes em StorSimple Virtual Array Microsoft Docs
description: Descreve o Gestor de Dispositivos StorSimple e explica como usá-lo para gerir volumes no seu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129954"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de Dispositivos do StorSimple para gerir volumes no StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir volumes no seu StorSimple Virtual Array.

O serviço StorSimple Device Manager é uma extensão no portal Azure que permite gerir a sua solução StorSimple a partir de uma única interface web. Além de gerir ações e volumes, pode utilizar o serviço StorSimple Device Manager para visualizar e gerir dispositivos, ver alertas e ver e gerir políticas de backup e o catálogo de backup.

## <a name="volume-types"></a>Tipos de Volume

Os volumes StorSimple podem ser:

* **Localmente fixado** : Os dados nestes volumes permanecem sempre na matriz e não derramam para a nuvem.
* **Tiered** : Os dados nestes volumes podem derramar para a nuvem. Quando se cria um volume hierárquico, cerca de 10 % do espaço é a provisionado no nível local e 90 % do espaço é a provisionado na nuvem. Por exemplo, se fornecesse um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que, se ficar sem todo o espaço local do dispositivo, não poderá prever um volume hierárquico (porque os 10 % exigidos no nível local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade a provisionada
Consulte o quadro seguinte para obter a capacidade máxima a provisionada para cada tipo de volume.

| **Identificador de limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Tamanho mínimo de um volume hierárquico                            | 500 GB        |
| Tamanho máximo de um volume hierárquico                            | 5 TB          |
| Tamanho mínimo de um volume fixado localmente                    | 50 GB         |
| Tamanho máximo de um volume fixado localmente                    | 200 GB        |

## <a name="the-volumes-blade"></a>A lâmina volumes
O menu **Volumes** da sua lâmina de resumo de serviço StorSimple apresenta a lista de volumes de armazenamento numa determinada matriz StorSimple e permite-lhe geri-los.

![Lâmina de volume](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Um volume consiste numa série de atributos:

* **Nome do volume** – Um nome descritivo que deve ser único e ajuda a identificar o volume.
* **Estado** – Pode estar online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm acesso à utilização do volume.
* **Tipo** – Indica se o volume é **Tiered** (o padrão) ou **fixado localmente** .
* **Capacidade** – especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados pelo iniciador (servidor).
* **Backup** – No caso do Conjunto Virtual StorSimple, todos os volumes são automaticamente ativados para cópia de segurança.
* **Anfitriões conectados** – Especifica os iniciadores (servidores) que têm acesso a este volume.

![Detalhes de volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Utilize as instruções deste tutorial para executar as seguintes tarefas:

* Adicione um volume
* Modificar um volume
* Desative o volume
* Apagar um volume

## <a name="add-a-volume"></a>Adicione um volume

1. A partir da lâmina de resumo do serviço StorSimple, clique **+ Adicione volume** da barra de comando. Isto abre a lâmina **de volume adicionar.**
   
    ![A screenshot mostra o botão de volume Adicionar e o painel de volume adicionar.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Na lâmina **de volume adicionar,** faça o seguinte:
   
   * No campo **de nomes Volume,** insira um nome único para o seu volume. O nome deve ser uma corda que contenha 3 a 127 caracteres.
   * Na lista de dropdown **do tipo,** especifique se deve criar um volume **tiered** ou **localmente fixado.** Para cargas de trabalho que exijam garantias locais, baixas latências e maior desempenho, selecione **volume fixo local.** Para todos os outros dados, **selecione Volume Tiered.**
   * No campo **Capacidade,** especifique o tamanho do volume. Um volume hierárquico deve estar entre 500 GB e 5 TB e um volume fixado localmente deve estar entre 50 GB e 500 GB.
   * * Clique **em Anfitriões Conectados** , selecione um registo de controlo de acesso (ACR) correspondente ao iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **Select** .
3. Para adicionar um novo anfitrião conectado, clique em **Adicionar novo,** insira um nome para o anfitrião e o seu nome qualificado iSCSI (IQN) e, em seguida, clique em **Adicionar** .
   
    ![A screenshot mostra o painel de anfitriões conectado onde pode adicionar novos.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Quando terminar de configurar o seu volume, clique em **Criar** . Será criado um volume com as definições especificadas e verá uma notificação sobre a criação bem sucedida do mesmo. Por predefinição, a cópia de segurança será ativada para o volume.
5. Para confirmar que o volume foi criado com sucesso, vá para a lâmina **Volumes.** Devia ver o volume listado.
   
    ![Volume criar sucesso](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar um volume

Modifique um volume quando necessitar de alterar os anfitriões que acedam ao volume. Os outros atributos de um volume não podem ser modificados uma vez que o volume tenha sido criado.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. A partir da definição **de Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume em que o volume deseja modificar.
2. **Selecione** o volume e clique **em anfitriões conectados** para ver o anfitrião atualmente ligado e modificá-lo para um servidor diferente.
   
    ![Editar volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Guarde as suas alterações clicando na barra de comando **Save.** As suas definições especificadas serão aplicadas e verá uma notificação.

## <a name="take-a-volume-offline"></a>Desative o volume

Pode ter de desligar um volume quando estiver a planear modificá-lo ou eliminá-lo. Quando um volume está offline, não está disponível para acesso de leitura-escrita. Terá de desligar o volume do hospedeiro, bem como do dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para tirar um volume offline

1. Certifique-se de que o volume em questão não está a ser utilizado antes de o desligar.
2. Desative o volume do hospedeiro primeiro. Isto elimina qualquer risco potencial de corrupção de dados no volume. Para obter etapas específicas, consulte as instruções do seu sistema operativo hospedeiro.
3. Depois de o volume do hospedeiro estar offline, desative o volume da matriz, executando os seguintes passos:
   
   * A partir da definição **de Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume que deseja retirar offline.
   * **Selecione** o volume e clique **...** (alternadamente clique à direita nesta linha) e a partir do menu de contexto, selecione **'Tirar offline'.**
     
        ![Volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Reveja as informações na lâmina **offline Take** e confirme a sua aceitação da operação. Clique **em Desligar** offline para tirar o volume offline. Verá uma notificação da operação em curso.
   * Para confirmar que o volume foi retirado com sucesso offline, vá para a lâmina **Volumes.** Deve ver o estado do volume como offline.
     
       ![Confirmação de volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Apagar um volume

> [!IMPORTANT]
> Só pode apagar um volume se estiver offline.
> 
> 

Preencha os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para apagar um volume

1. A partir da definição **de Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume em que deseja eliminar.
2. **Selecione** o volume e clique **...** (alternadamente clique à direita nesta linha) e a partir do menu de contexto, selecione **Delete** .
   
    ![Eliminar volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, desative-o primeiro, seguindo os passos em [Retirar um volume offline](#take-a-volume-offline).
4. Quando solicitado para confirmação na lâmina **eliminar,** aceite a confirmação e clique em **Eliminar** . O volume será agora eliminado e a lâmina **volumes** mostrará a lista atualizada de volumes dentro da matriz virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba como [clonar um volume StorSimple](storsimple-virtual-array-clone.md).

