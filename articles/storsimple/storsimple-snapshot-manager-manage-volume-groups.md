---
title: StorSimple Snapshot Manager grupos de volume / Microsoft Docs
description: Descreve como utilizar o snap-in StorSimple Snapshot Manager MMC para criar e gerir grupos de volume.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209224"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Use storSimple Snapshot Manager para criar e gerir grupos de volume
## <a name="overview"></a>Descrição geral
Pode utilizar o nó **grupos de volume** no painel **Scope** para atribuir volumes a grupos de volume, ver informações sobre um grupo de volume, agendar cópias de segurança e editar grupos de volume.

Os grupos de volume são grupos de volumes relacionados utilizados para garantir que as cópias de segurança são consistentes com as aplicações. Para obter mais informações, consulte [volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Todos os volumes de um grupo de volume devem ser provenientes de um único fornecedor de serviços em nuvem.
> * Quando configurar grupos de volume, não misture volumes partilhados por cluster (CSVs) e não-CSVs no mesmo grupo de volume. O StorSimple Snapshot Manager não suporta uma mistura de CSVs e não-CSVs na mesma imagem.

![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó de grupos de volume de gestor snapshot simple** 

Este tutorial explica como pode utilizar o StorSimple Snapshot Manager para:

* Ver informações sobre os seus grupos de volume
* Criar um grupo de volume
* Apoiar um grupo de volume
* Editar um grupo de volume
* Excluir um grupo de volume

Todas estas ações estão também disponíveis no painel **de Ações.**

## <a name="view-volume-groups"></a>Ver grupos de volume
Se clicar no nó **Grupos de Volume,** o painel **de resultados** mostra as seguintes informações sobre cada grupo de volume, dependendo das seleções de colunas que escoda. (As colunas no painel **de resultados** são configuráveis. Clique com o botão direito no nó **volumes,** selecione **Ver** e, em seguida, selecione **Adicionar/Remover Colunas**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |A coluna **Nome** contém o nome do grupo de volume. |
| Aplicação |A coluna Aplicações mostra o número de **escritores** VSS atualmente instalados e em execução no anfitrião do Windows. |
| Selecionado |A coluna **Selecionada** mostra o número de volumes contidos no grupo de volume. Um zero (0) indica que nenhuma aplicação está associada aos volumes do grupo de volume. |
| Importado |A coluna **importada** mostra o número de volumes importados. Quando definida para **True**, esta coluna indica que um grupo de volume foi importado do portal Azure e não foi criado no StorSimple Snapshot Manager. |

> [!NOTE]
> Os grupos de volume StorSimple Snapshot Manager também são apresentados no separador **Políticas de Backup** no portal Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volume
Utilize o seguinte procedimento para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Criar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita **em Grupos de Volume** e, em seguida, clique em Criar Grupo de **Volume**.
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Aparece a caixa de diálogo **do grupo De volume Create.**
   
    ![Criar um diálogo de grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Introduza as seguintes informações:
   
   1. Na caixa **Nome,** escreva um nome único para o novo grupo de volume.
   2. Na caixa **Aplicações,** selecione as aplicações associadas aos volumes que irá adicionar ao grupo de volume.
      
       A caixa applications lista **apenas** as aplicações que utilizam volumes StorSimple e têm escritores VSS habilitados para eles. Um escritor vss só é habilitado se todos os volumes que o escritor está ciente são volumes StorSimple. Se a caixa aplicações estiver vazia, não serão instaladas aplicações que utilizem volumes Azure StorSimple e tenham suportado escritores VSS. (Atualmente, a Azure StorSimple suporta o Microsoft Exchange e o SQL Server.) Para obter mais informações sobre os escritores vss, consulte [a Integração com o Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se selecionar uma aplicação, todos os volumes associados a ela são automaticamente selecionados. Inversamente, se selecionar volumes associados a uma aplicação específica, a aplicação é automaticamente selecionada na caixa **de Aplicações.** 
   3. Na caixa **Volumes,** selecione volumes StorSimple para adicionar ao grupo de volume. 
      
      * Pode incluir volumes com divisórias simples ou múltiplas. (Múltiplos volumes de partição podem ser discos dinâmicos ou discos básicos com múltiplas divisórias.) Um volume que contém múltiplas divisórias é tratado como uma única unidade. Consequentemente, se adicionar apenas uma das divisórias a um grupo de volume, todas as outras divisórias são automaticamente adicionadas a esse grupo de volume ao mesmo tempo. Depois de adicionar um volume de partição múltipla a um grupo de volume, o volume de partição múltipla continua a ser tratado como uma única unidade.
      * Pode criar grupos de volume vazios não atribuindo-lhes volumes. 
      * Não misture volumes partilhados por cluster (CSVs) e não-CSVs no mesmo grupo de volume. O StorSimple Snapshot Manager não suporta uma mistura de volumes de CSV e volumes não CSV no mesmo instantâneo.
4. Clique **em OK** para salvar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Apoiar um grupo de volume
Utilize o seguinte procedimento para fazer o reforço de um grupo de volume.

#### <a name="to-back-up-a-volume-group"></a>Para apoiar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó grupos de **volume,** clique com o botão direito num nome de grupo de volume e, em seguida, clique em **Take Backup**.
   
    ![Apoiar grupo de volume imediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Na caixa de diálogo **De backup,** selecione **Snapshot Local** ou **Cloud Snapshot** e, em seguida, clique em **Criar**.
   
    ![Faça o diálogo de backup](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que a cópia de segurança está em execução, expanda o nó **Jobs** e, em seguida, clique em **Executar**. O reforço deve estar na lista.
5. Para visualizar o instantâneo completo, expanda o nó **do Catálogo de Cópias de Segurança,** expanda o nome do grupo de volume e, em seguida, clique em **Snapshot Local** ou **Cloud Snapshot**. A cópia de segurança será listada se terminar com sucesso.

## <a name="edit-a-volume-group"></a>Editar um grupo de volume
Utilize o seguinte procedimento para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó **grupos de volume,** clique com o botão direito num nome de grupo de volume e, em seguida, clique em **Editar**.
3. Aparece a caixa de diálogo **do grupo De volume Create.** Pode alterar as entradas **Nome,** **Aplicações** e **Volumes.**
4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Excluir um grupo de volume
Utilize o seguinte procedimento para eliminar um grupo de volume. 

> [!WARNING]
> Isto também elimina todas as cópias de segurança associadas ao grupo de volume.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para eliminar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó grupos de **volume,** clique com o botão direito num nome de grupo de volume e, em seguida, clique em **Eliminar**.
3. Aparece a caixa de diálogo **do Grupo Delete Volume.** Escreva **Confirmar** na caixa de texto e, em seguida, clique **em OK**.
   
    O grupo de volume eliminado desaparece da lista no painel **de resultados** e todas as cópias de segurança associadas a esse grupo de volume são eliminadas do catálogo de cópias de segurança.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para criar e gerir políticas de backup.](storsimple-snapshot-manager-manage-backup-policies.md)

