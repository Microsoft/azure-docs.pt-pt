---
title: 'Faça o D: unidade de um VM um disco de dados '
description: 'Descreve como alterar as letras de unidade para um VM do Windows para que possa utilizar o D: conduzir como unidade de dados.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 511c3aa65bf0a10e42d7a54c98662cc388a5d711
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028229"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilize o D: conduza como unidade de dados num VM do Windows
Se a sua aplicação precisar de utilizar a unidade D para armazenar dados, siga estas instruções para utilizar uma letra de unidade diferente para o disco temporário. Nunca utilize o disco temporário para armazenar dados que precisa de guardar.

Se redimensionar ou **parar (Deallocate)** uma máquina virtual, isto pode desencadear a colocação da máquina virtual para um novo hipervisor. Um evento de manutenção planeado ou não planeado também pode desencadear esta colocação. Neste cenário, o disco temporário será reatribuído à primeira carta de condução disponível. Se tiver uma aplicação que exija especificamente o D: unidade, tem de seguir estes passos para mover temporariamente o pagefile.sys, anexar um novo disco de dados e atribuí-lo à letra D e, em seguida, mover a pagefile.sys de volta para a unidade temporária. Uma vez concluído, O Azure não retira o D: se o VM se deslocar para um hipervisor diferente.

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [compreender a unidade temporária nas Máquinas Virtuais do Microsoft Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>Anexar o disco de dados
Primeiro, terá de anexar o disco de dados à máquina virtual. Para o fazer utilizando o portal, consulte [Como anexar um disco de dados gerido no portal Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente pagefile.sys para unidade C
1. Ligue à máquina virtual. 
2. Clique com o botão direito no menu **Iniciar** e selecione **Sistema.**
3. No menu da esquerda, selecione **Definições avançadas do sistema**.
4. Na secção **Desempenho,** selecione **Definições**.
5. Selecione o separador **Avançadas**.
6. Na secção **memória Virtual,** selecione **Alterar**.
7. Selecione a unidade **C** e, em seguida, clique no **tamanho gerido do Sistema** e, em seguida, clique em **Conjunto**.
8. Selecione a unidade **D** e, em seguida, clique em **No paging file** e, em seguida, clique em **Conjunto**.
9. Clique em Aplicar. Receberá um aviso de que o computador precisa de ser reiniciado para que as alterações sejam afetadas.
10. Reinicie a máquina virtual.

## <a name="change-the-drive-letters"></a>Alterar as letras de unidade
1. Assim que o VM recomeçar, volte a ligar-se ao VM.
2. Clique no menu **Iniciar** e escreva **diskmgmt.msc** e clique em Enter. A Gestão do Disco vai começar.
3. Clique à direita em **D,** a unidade de armazenamento temporário e selecione **Change Drive Letter and Paths**.
4. Na letra Drive, selecione uma nova unidade como **T** e, em seguida, clique **em OK**. 
5. Clique com o botão direito no disco de dados e selecione **Change Drive Letter and Paths**.
6. Na letra Drive, selecione drive **D** e, em seguida, clique **em OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Movapagefile.sys de volta para a unidade de armazenamento temporário
1. Clique com o botão direito no menu **Iniciar** e selecione **Sistema**
2. No menu da esquerda, selecione **Definições avançadas do sistema**.
3. Na secção **Desempenho,** selecione **Definições**.
4. Selecione o separador **Avançadas**.
5. Na secção **memória Virtual,** selecione **Alterar**.
6. Selecione a unidade de SO **C** e clique em **No paging file** e, em seguida, clique em **Conjunto**.
7. Selecione a unidade de armazenamento temporária **T** e, em seguida, clique no **tamanho gerido do Sistema** e, em seguida, clique em **Conjunto**.
8. Clique em **Aplicar**. Receberá um aviso de que o computador precisa de ser reiniciado para que as alterações sejam afetadas.
9. Reinicie a máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Pode aumentar o armazenamento disponível para a sua máquina virtual [anexando um disco de dados adicional.](attach-managed-disk-portal.md)
