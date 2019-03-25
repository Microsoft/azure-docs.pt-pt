---
title: Como aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, irá aceder a máquinas virtuais num laboratório de sala de aula configurado por um professor.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 8e20f612bc54433091036377d51a7e59e3abec51
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402155"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como aceder a um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como aceder a um laboratório de sala de aula, ligar à VM no laboratório e parar a VM. 

## <a name="register-to-a-lab"></a>Registre-se para um laboratório
1. Navegue para o **URL de registo** que recebeu do professor/educador. 
2. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 
3. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 
2. Aguarde até que a máquina virtual esteja pronta e, depois, **inicie-a**. Este processo demora algum tempo.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Ver todos os laboratórios de sala de aula
Depois de registar aos laboratórios, pode ver todos os laboratórios de sala de aula, efetuando os seguintes passos: 

1. Navegue para [ https://labs.azure.com ](https://labs.azure.com). 
2. Iniciar sessão serviço utilizando a conta de utilizador que utilizou para se registrar para o laboratório. 
3. Confirme que vê todos os laboratórios que tem acesso. 

    ![Ver todos os laboratórios](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Ligar à máquina virtual num laboratório de sala de aula

1. Iniciar a VM se esta ainda não tiver iniciado, selecione **iniciar** no mosaico. 
2. Selecione **Ligar** no mosaico que representa a máquina virtual do laboratório a que pretende aceder. 
3. Efetue um dos seguintes passos: 
   1. Para **Windows** máquinas virtuais, guarde o **RDP** ficheiro para o disco rígido. Abra o ficheiro RDP para ligar à máquina virtual. Utilize o **nome de utilizador** e **palavra-passe** a partir do seu professor/professora para iniciar sessão máquina. 
   3. Para **Linux** máquinas virtuais, copie e guarde a cadeia de ligação de SSH no **ligar a sua máquina virtual** caixa de diálogo. Utilize esta cadeia de ligação de um terminal SSH (como [Putty](https://www.putty.org/)) para ligar à máquina virtual.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Parar a máquina virtual num laboratório de sala de aula

Para parar a VM, selecione **parar** no mosaico. Quando a VM está parada, o botão **Iniciar** no mosaico é ativado. 

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar a utilização de um laboratório](how-to-configure-student-usage.md)
 