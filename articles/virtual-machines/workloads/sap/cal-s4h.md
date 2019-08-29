---
title: Implantar SAP S/4HANA ou BW/4HANA em uma VM do Azure | Microsoft Docs
description: Implantar o SAP S/4HANA ou BW/4HANA em uma VM do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 2fa68d9dc3052263b5354086ee802cc31fa35ace
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101447"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implantar o SAP S/4HANA ou BW/4HANA no Azure
Este artigo descreve como implantar S/4HANA no Azure usando o SAP CAL (SAP Cloud Appliance Library) 3,0. Para implantar outras soluções baseadas em SAP HANA, como BW/4HANA, siga as mesmas etapas.

> [!NOTE]
> Para obter mais informações sobre o SAP CAL, vá para o site do [SAP Cloud Appliance library](https://cal.sap.com/) . O SAP também tem um blog sobre a [SAP Cloud Appliance Library 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> A partir de 29 de maio de 2017, você pode usar o modelo de implantação Azure Resource Manager além do modelo de implantação clássico menos preferido para implantar o SAP CAL. Recomendamos que você use o novo modelo de implantação do Gerenciador de recursos e Desconsidere o modelo de implantação clássico.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implantar a solução

A sequência de capturas de tela a seguir mostra como implantar o S/4HANA no Azure usando o SAP CAL. O processo funciona da mesma maneira para outras soluções, como BW/4HANA.

A página **soluções** mostra algumas das soluções baseadas no SAP Cal Hana disponíveis no Azure. O **aplicativo SAP S/4HANA 1610 fps01 Users, totalmente ativado,** está na linha intermediária:

![Soluções da SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para entrar no SAP CAL pela primeira vez, use o SAP S-User ou outro usuário registrado com o SAP. Em seguida, defina uma conta da SAP CAL que é usada pela SAP CAL para implantar dispositivos no Azure. Na definição de conta, você precisa:

    a. Selecione o modelo de implantação no Azure (Resource Manager ou clássico).

    b. Insira sua assinatura do Azure. Uma conta da SAP CAL pode ser atribuída somente a uma assinatura. Se precisar de mais de uma assinatura, você precisará criar outra conta da SAP CAL.

    c. Conceda à permissão SAP CAL para implantar em sua assinatura do Azure.

   > [!NOTE]
   >  As próximas etapas mostram como criar uma conta da SAP CAL para implantações do Gerenciador de recursos. Se você já tiver uma conta da SAP CAL vinculada ao modelo de implantação clássico, você *precisará* seguir estas etapas para criar uma nova conta da SAP Cal. A nova conta da SAP CAL precisa ser implantada no modelo do Resource Manager.

1. Crie uma nova conta da SAP CAL. A página **contas** mostra três opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implantação clássico e não é mais preferencial.

    b. **Microsoft Azure** é o novo modelo de implantação do Gerenciador de recursos.

    c. O **Windows Azure operado pela 21vianet** é uma opção na China que usa o modelo de implantação clássico.

    Para implantar no modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Detalhes da conta da SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Insira a **ID de assinatura** do Azure que pode ser encontrada no portal do Azure.

   ![Contas da SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Para autorizar o SAP CAL a implantar na assinatura do Azure que você definiu, clique em **autorizar**. A página a seguir aparece na guia navegador:

   ![Entrada de serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Se mais de um usuário estiver listado, escolha o conta Microsoft que está vinculado a ser o coadministrador da assinatura do Azure que você selecionou. A página a seguir aparece na guia navegador:

   ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Clique em **aceitar**. Se a autorização for bem-sucedida, a definição de conta da SAP CAL será exibida novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi bem-sucedido.

1. Para atribuir a conta do SAP CAL recém-criada ao usuário, insira sua **ID de usuário** na caixa de texto à direita e clique em **Adicionar**.

   ![Conta para associação de usuário](./media/cal-s4h/s4h-pic8a.png)

1. Para associar sua conta ao usuário que você usa para entrar na SAP CAL, clique em **examinar**. 
 
1. Para criar a associação entre o usuário e a conta do SAP CAL recém-criada, clique em **criar**.

   ![Usuário para associação de conta do SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Você criou com êxito uma conta da SAP CAL que é capaz de:

- Use o modelo de implantação do Gerenciador de recursos.
- Implante sistemas SAP em sua assinatura do Azure.

Agora você pode começar a implantar S/4HANA em sua assinatura de usuário no Azure.

> [!NOTE]
> Antes de continuar, determine se você tem cotas do Azure vCPU para VMs da série H do Azure. No momento, a SAP CAL usa as VMs da série H do Azure para implantar algumas das soluções baseadas em SAP HANA. Sua assinatura do Azure pode não ter nenhuma cota de vCPU da série H para a série H. Nesse caso, talvez seja necessário entrar em contato com o suporte do Azure para obter uma cota de pelo menos 16 vCPUs da série H.
> 
> [!NOTE]
> Ao implantar uma solução no Azure na SAP CAL, você pode descobrir que pode escolher apenas uma região do Azure. Para implantar em regiões do Azure diferentes da sugerida pela SAP CAL, você precisa comprar uma assinatura do CAL da SAP. Você também pode precisar abrir uma mensagem com o SAP para que sua conta CAL seja habilitada para entrega em regiões do Azure que não sejam as inicialmente sugeridas.

### <a name="deploy-a-solution"></a>Implantar uma solução

Vamos implantar uma solução na página **soluções** da SAP Cal. A SAP CAL tem duas sequências para implantar:

- Uma sequência básica que usa uma página para definir o sistema a ser implantado
- Uma sequência avançada que fornece certas opções de tamanhos de VM 

Demonstramos o caminho básico para a implantação aqui.

1. Na página de **detalhes da conta** , você precisa:

    a. Selecione uma conta da SAP CAL. (Use uma conta que esteja associada à implantação com o modelo de implantação do Resource Manager.)

    b. Insira um **nome**de instância.

    c. Selecione uma **região**do Azure. A SAP CAL sugere uma região. Se você precisar de outra região do Azure e não tiver uma assinatura da SAP CAL, será necessário solicitar uma assinatura do CAL com o SAP.

    d. Insira uma **senha** mestra para a solução de oito ou nove caracteres. A senha é usada para os administradores dos diferentes componentes.

   ![Modo básico da SAP CAL: Criar Instância](./media/cal-s4h/s4h-pic10a.png)

1. Clique em **criar**e, na caixa de mensagem exibida, clique em **OK**.

   ![Tamanhos de VM compatíveis com SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. Na caixa de diálogo **chave privada** , clique em **armazenar** para armazenar a chave privada na SAP Cal. Para usar a proteção por senha para a chave privada, clique em **baixar**. 

   ![Chave privada SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Leia a mensagem de **aviso** do SAP Cal e clique em **OK**.

   ![Aviso do SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Agora, a implantação ocorre. Após algum tempo, dependendo do tamanho e da complexidade da solução (a SAP CAL fornece uma estimativa), o status é mostrado como ativo e pronto para uso.

1. Para localizar as máquinas virtuais coletadas com os outros recursos associados em um grupo de recursos, vá para o portal do Azure: 

   ![Objetos da SAP CAL implantados no novo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. No portal do SAP CAL, o status aparece como **ativo**. Para se conectar à solução, clique em **conectar**. Opções diferentes para se conectar a diferentes componentes são implantadas nessa solução.

   ![Instâncias do SAP CAL](./media/cal-s4h/active_solution.png)

1. Para poder usar uma das opções para se conectar aos sistemas implantados, clique **introdução guia**. 

   ![Conectar-se à instância](./media/cal-s4h/connect_to_solution.png)

    A documentação nomeia os usuários para cada um dos métodos de conectividade. As senhas para esses usuários são definidas com a senha mestra que você definiu no início do processo de implantação. Na documentação, outros usuários mais funcionais são listados com suas senhas, que você pode usar para entrar no sistema implantado. 

    Por exemplo, se você usar a GUI do SAP que é pré-instalado no computador Windows Área de Trabalho Remota, o sistema S/4 poderá ser assim:

   ![SM50 na GUI do SAP pré-instalado](./media/cal-s4h/gui_sm50.png)

    Ou, se você usar o DBACockpit, a instância poderá ser parecida com esta:

   ![SM50 na GUI SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

Em algumas horas, um dispositivo SAP S/4 íntegro é implantado no Azure.

Se você comprou uma assinatura da SAP CAL, o SAP dá suporte total a implantações por meio do SAP CAL no Azure. A fila de suporte é BC-VCM-CAL.







