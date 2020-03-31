---
title: 'Faça o D: unidade de um VM um disco de dados '
description: 'Descreve como alterar as letras de condução para um VM do Windows para que possa utilizar o D: conduzir como uma unidade de dados.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033660"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Use o D: dirija como uma unidade de dados num VM do Windows
Se a sua aplicação necessitar de utilizar a unidade D para armazenar dados, siga estas instruções para utilizar uma letra de unidade diferente para o disco temporário. Nunca utilize o disco temporário para armazenar os dados que precisa de guardar.

Se redimensionar ou **parar (Dealocar)** uma máquina virtual, isto pode desencadear a colocação da máquina virtual num novo hipervisor. Um evento de manutenção planeado ou não planeado também pode desencadear esta colocação. Neste cenário, o disco temporário será transferido para a primeira letra disponível. Se tiver uma aplicação que exija especificamente o D: unidade, precisa seguir estes passos para mover temporariamente o pagefile.sys, anexar um novo disco de dados e atribuí-lo a letra D e, em seguida, mover o ficheiro de página.sys de volta para a unidade temporária. Uma vez concluído, Azure não retomará o D: se o VM se mover para um hipervisor diferente.

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [Compreender a unidade temporária nas Máquinas Virtuais Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Anexar o disco de dados
Primeiro, terá de anexar o disco de dados à máquina virtual. Para isso utilizando o portal, consulte como anexar um disco de [dados gerido no portal Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente pagefile.sys para unidade C
1. Ligue à máquina virtual. 
2. Clique no menu **Iniciar** e selecione **Sistema**.
3. No menu à esquerda, selecione **definições avançadas do sistema**.
4. Na secção **Performance,** selecione **Definições**.
5. Selecione o separador **Avançadas**.
6. Na secção **de memória Virtual,** selecione **Mudar**.
7. Selecione a unidade **C** e, em seguida, clique no **tamanho gerido pelo Sistema** e, em seguida, clique em **Definir**.
8. Selecione a unidade **D** e, em seguida, clique **em Não veriver ficheiro** e, em seguida, clique em **Definir**.
9. Clique em Aplicar. Receberá um aviso de que o computador precisa de ser reiniciado para que as alterações afetem.
10. Reiniciar a máquina virtual.

## <a name="change-the-drive-letters"></a>Mude as letras de unidade
1. Assim que o VM recomeçar, volte a entrar no VM.
2. Clique no menu **Iniciar** e digite **diskmgmt.msc** e clique em Enter. A Gestão do Disco vai começar.
3. Clique à direita em **D,** na unidade de armazenamento temporário e selecione **Change Drive Letter and Paths**.
4. Sob a letra Drive, selecione uma nova unidade como **T** e, em seguida, clique em **OK**. 
5. Clique à direita no disco de dados e selecione **Change Drive Letter and Paths**.
6. Sob a letra Drive, selecione a unidade **D** e, em seguida, clique em **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Mova o pagefile.sys de volta para a unidade de armazenamento temporário
1. Clique no menu **Iniciar** e selecione **Sistema**
2. No menu à esquerda, selecione **definições avançadas do sistema**.
3. Na secção **Performance,** selecione **Definições**.
4. Selecione o separador **Avançadas**.
5. Na secção **de memória Virtual,** selecione **Mudar**.
6. Selecione a unidade OS **C** e clique **em Não ver o ficheiro de paging** e, em seguida, clique em **Definir**.
7. Selecione a unidade de armazenamento temporária **T** e, em seguida, clique no **tamanho gerido pelo Sistema** e, em seguida, clique em **Definir**.
8. Clique em **Aplicar**. Receberá um aviso de que o computador precisa de ser reiniciado para que as alterações afetem.
9. Reiniciar a máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Pode aumentar o armazenamento disponível na sua máquina [virtual, anexando um disco](attach-managed-disk-portal.md)de dados adicional .

