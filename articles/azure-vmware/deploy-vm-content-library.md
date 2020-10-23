---
title: Criar uma biblioteca de conteúdos para implantar VMs na Solução VMware Azure
description: Crie uma biblioteca de conteúdos para implantar um VM numa nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 57db8ac4da8eef826d85feb11680c9c87823ee36
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461690"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Criar uma biblioteca de conteúdos para implantar VMs na Solução VMware Azure

Uma biblioteca de conteúdos armazena e gere conteúdos sob a forma de itens de biblioteca. Um único item de biblioteca consiste num ou mais ficheiros que utiliza para implantar máquinas virtuais (VMs). 

Neste artigo, vamos percorrer o procedimento para a criação de uma biblioteca de conteúdos.  Depois, vamos percorrer a implementação de um VM usando uma imagem ISO da biblioteca de conteúdos.

## <a name="prerequisites"></a>Pré-requisitos

É necessário um segmento de comutação lógica NSX-T e um serviço DHCP gerido para completar este tutorial.  Para mais informações, consulte o Artigo Como gerir o DHCP no artigo [Azure VMware Solution.](manage-dhcp.md)

## <a name="create-a-content-library"></a>Criar uma biblioteca de conteúdos

1. A partir do local vSphere Client, selecione **Menu > Bibliotecas de Conteúdos**.

   ![Selecione Bibliotecas de Conteúdos > Menu](./media/content-library/vsphere-menu-content-libraries.png)

1. Selecione o botão **Adicionar** para criar uma nova biblioteca de conteúdos.

   ![Selecione o botão Adicionar para criar uma nova biblioteca de conteúdos.](./media/content-library/create-new-content-library.png)

1. Especifique um nome e confirme o endereço IP do servidor vCenter e selecione **Next**.

   ![Especifique um nome e notas da sua escolha e, em seguida, selecione Seguinte.](./media/content-library/new-content-library-step1.png)

1. Selecione a **biblioteca de conteúdos Local** e selecione **Seguinte**.

   ![Para este exemplo, vamos criar uma biblioteca de conteúdos local, selecione Next.](./media/content-library/new-content-library-step2.png)

1. Selecione a loja de dados que armazenará a sua biblioteca de conteúdos e, em seguida, selecione **Seguinte**.

   ![Selecione a loja de dados que pretende hospedar a sua biblioteca de conteúdos, selecione em seguida.](./media/content-library/new-content-library-step3.png)

1. Reveja e verifique as definições da biblioteca de conteúdos e, em seguida, **selecione Terminar**.

   ![Verifique as definições, selecione Acabamento.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Faça upload de uma imagem ISO para a biblioteca de conteúdos

Agora que a biblioteca de conteúdos foi criada, pode adicionar uma imagem ISO para implantar um VM num cluster de nuvem privada. 

1. A partir do cliente vSphere, selecione **Menu > Bibliotecas de Conteúdos**.

1. Clique com o botão direito na biblioteca de conteúdos que pretende utilizar para o novo ISO e selecione **Import Item**.

1. Importe um item de biblioteca para a Fonte fazendo um dos seguintes e, em seguida, selecione **Import**:
   1. Selecione URL e forneça um URL para descarregar um ISO.

   1. Selecione **O Arquivo Local** para fazer o upload a partir do seu sistema local.

   > [!TIP]
   > Opcional, pode definir um nome de item personalizado e notas para o Destino.

1. Abra a biblioteca e selecione o separador **Outros Tipos** para verificar se o seu ISO foi carregado com sucesso.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Implementar um VM num aglomerado de nuvens privadas

1. A partir do cliente vSphere, selecione **Menu > Anfitriões e Clusters**.

1. No painel esquerdo, expanda a árvore e selecione um aglomerado.

1. Selecione **Ações > Nova Máquina Virtual**.

1. Passe pelo assistente e modifique as definições desejada.

1. Selecione **nova unidade de CD/DVD > dispositivo de cliente > ficheiro ISO da Biblioteca de Conteúdos**.

1. Selecione o ISO carregado na secção anterior e, em seguida, selecione **OK**.

1. Selecione a caixa de verificação **'Ligar'** para que o ISO seja montado a tempo de alimentação.

1. Selecione **New Network > Selecione dropdown > Navegar**.

1. Selecione o **interruptor lógico (segmento)** e selecione **OK**.

1. Modifique quaisquer outras definições de hardware e selecione **Next**.

1. Verifique as definições e **selecione Acabamento**.


## <a name="next-steps"></a>Passos seguintes

Se planeia utilizar o VMware HCX para migrar cargas de trabalho VM para a sua nuvem privada, utilize o procedimento [VMware HCX de implementação e configuração.](tutorial-deploy-vmware-hcx.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
