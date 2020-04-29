---
title: Gerir volumes em StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve o StorSimple Device Manager e explica como usá-lo para gerir volumes no seu StorSimple Virtual Array.
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
ms.openlocfilehash: 2dbbe6bcd4957a108cc3eae4d41816b130cf8f07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633872"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de Dispositivos do StorSimple para gerir volumes no StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este tutorial explica como usar o serviço StorSimple Device Manager para criar e gerir volumes no seu StorSimple Virtual Array.

O serviço StorSimple Device Manager é uma extensão no portal Azure que permite gerir a sua solução StorSimple a partir de uma única interface web. Além de gerir ações e volumes, pode utilizar o serviço StorSimple Device Manager para visualizar e gerir dispositivos, visualizar alertas e visualizar e gerir políticas de backup e o catálogo de backup.

## <a name="volume-types"></a>Tipos de volume

Os volumes StorSimple podem ser:

* **Localmente fixado:** Os dados nestes volumes permanecem sempre na matriz e não derramam para a nuvem.
* **Tiered**: Os dados nestes volumes podem derramar para a nuvem. Quando se cria um volume hierárquico, aproximadamente 10 % do espaço é aprovisionado no nível local e 90 % do espaço é aprovisionado na nuvem. Por exemplo, se for provisionado um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que, se ficar sem todo o espaço local do dispositivo, não pode fornecer um volume hierárquico (porque os 10 % exigidos no nível local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade aprovisionada
Consulte o quadro seguinte para obter a máxima capacidade prevista para cada tipo de volume.

| **Identificador de limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Tamanho mínimo de um volume hierárquico                            | 500 GB        |
| Tamanho máximo de um volume hierárquico                            | 5 TB          |
| Tamanho mínimo de um volume fixado localmente                    | 50 GB         |
| Tamanho máximo de um volume fixado localmente                    | 200 GB        |

## <a name="the-volumes-blade"></a>A lâmina Volumes
O menu **Volumes** da sua lâmina de resumo do serviço StorSimple apresenta a lista de volumes de armazenamento numa dada matriz StorSimple e permite-lhe geri-los.

![Lâmina de volumes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Um volume consiste numa série de atributos:

* **Nome do Volume** – Um nome descritivo que deve ser único e ajuda a identificar o volume.
* **Estado** – Pode estar online ou offline. Se um volume estiver offline, não é visível para iniciadores (servidores) que são autorizados a aceder à utilização do volume.
* **Tipo** – Indica se o volume está **nivida (o** padrão) ou **fixado localmente**.
* **Capacidade** – especifica a quantidade de dados utilizados em comparação com a quantidade total de dados que podem ser armazenados pelo iniciador (servidor).
* **Backup** – No caso do StorSimple Virtual Array, todos os volumes estão automaticamente ativados para cópia de segurança.
* **Anfitriões conectados** – Especifica os iniciadores (servidores) que têm acesso a este volume.

![Detalhes de volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Utilize as instruções deste tutorial para executar as seguintes tarefas:

* Adicione um volume
* Modificar um volume
* Pegue um volume offline
* Eliminar um volume

## <a name="add-a-volume"></a>Adicione um volume

1. A partir da lâmina de resumo do serviço StorSimple, clique **+ adicione volume** da barra de comando. Isto abre a lâmina de **volume Adicionar.**
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Na lâmina de **volume Adicionar,** faça o seguinte:
   
   * No campo **de nome volume,** introduza um nome único para o seu volume. O nome deve ser uma corda que contenha 3 a 127 caracteres.
   * Na lista de abandono do **Tipo,** especifique se criar á criação de um volume **hierárquico** ou **fixado localmente.** Para cargas de trabalho que requerem garantias locais, baixas llátências e maior desempenho, selecione **volume fixado localmente.** Para todos os outros dados, selecione volume **tiered.**
   * No campo **capacidade,** especifique o tamanho do volume. Um volume hierárquico deve ser entre 500 GB e 5 TB e um volume fixado localmente deve ser entre 50 GB e 500 GB.
   * * Clique em **anfitriões Conectados,** selecione um registo de controlo de acesso (ACR) correspondente ao iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **Selecionar**.
3. Para adicionar um novo anfitrião conectado, clique **em Adicionar novo,** insira um nome para o anfitrião e o seu nome iSCSI Qualificado (IQN), e, em seguida, clique em **Adicionar**.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Quando terminar de configurar o seu volume, clique em **Criar**. Será criado um volume com as definições especificadas e verá uma notificação sobre a criação bem sucedida do mesmo. Por predefinição, a cópia de segurança será ativada para o volume.
5. Para confirmar que o volume foi criado com sucesso, vá à lâmina **Volumes.** Devia ver o volume listado.
   
    ![Volume criar sucesso](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar um volume

Modifique um volume quando precisa de alterar os anfitriões que acedem ao volume. Os outros atributos de um volume não podem ser modificados uma vez que o volume tenha sido criado.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. A partir da definição de **Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume que pretende modificar.
2. **Selecione** o volume e clique em **anfitriões conectados** para visualizar o anfitrião atualmente conectado e modifique-o para um servidor diferente.
   
    ![Editar volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Guarde as suas alterações clicando na barra de comando **Guardar.** As definições especificadas serão aplicadas e verá uma notificação.

## <a name="take-a-volume-offline"></a>Pegue um volume offline

Pode ser necessário desativar um volume quando planeia modificá-lo ou eliminá-lo. Quando um volume está offline, não está disponível para acesso de leitura- escrita. Terá de desligar o volume do hospedeiro, bem como do dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para tirar um volume offline

1. Certifique-se de que o volume em questão não está a ser utilizado antes de o desligar.
2. Desative o volume no hospedeiro primeiro. Isto elimina qualquer risco potencial de corrupção de dados no volume. Para obter passos específicos, consulte as instruções do seu sistema operativo anfitrião.
3. Depois de o volume no hospedeiro estar offline, desembote o volume da matriz offline, executando os seguintes passos:
   
   * A partir da definição de **Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume que pretende retirar offline.
   * **Selecione** o volume e clique **...** (clique alternadamente à direita nesta linha) e a partir do menu de contexto, selecione **Desligar**.
     
        ![Volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Reveja as informações na lâmina **Desativada** e confirme a sua aceitação da operação. Clique **em Desligar** para desligar o volume. Verá uma notificação da operação em curso.
   * Para confirmar que o volume foi desligado com sucesso, vá para a lâmina **Volumes.** Devia ver o estado do volume offline.
     
       ![Confirmação de volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Só é possível eliminar um volume se estiver offline.
> 
> 

Complete os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para apagar um volume

1. A partir da definição **de Volumes** na lâmina de resumo do serviço StorSimple, selecione a matriz virtual em que reside o volume que pretende eliminar.
2. **Selecione** o volume e clique **...** (clique alternadamente à direita nesta linha) e a partir do menu de contexto, selecione **Delete**.
   
    ![Eliminar volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, desative-o primeiro, seguindo os passos em [Take a volume offline](#take-a-volume-offline).
4. Quando solicitado para confirmação na lâmina **Apagar,** aceite a confirmação e clique em **Apagar**. O volume será agora eliminado e a lâmina **Volumes** mostrará a lista atualizada de volumes dentro da matriz virtual.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [clonar um volume StorSimple](storsimple-virtual-array-clone.md).

