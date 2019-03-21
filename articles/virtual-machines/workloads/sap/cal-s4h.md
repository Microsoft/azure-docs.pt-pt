---
title: Implementar o SAP S/4HANA ou BW/4HANA numa VM do Azure | Documentos da Microsoft
description: Implementar o SAP S/4HANA ou BW/4HANA numa VM do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c59fcf43cb4767f1d95d769dfce4d5c8755e45ee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990485"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implementar o SAP S/4HANA ou BW/4HANA no Azure
Este artigo descreve como implementar o S/4HANA no Azure ao utilizar o SAP Cloud Appliance Library (SAP CAL) 3.0. Para implementar outras soluções baseadas no SAP HANA, como BW/4HANA, siga os mesmos passos.

> [!NOTE]
> Para obter mais informações sobre o SAP CAL, vá para o [SAP Cloud Appliance Library](https://cal.sap.com/) Web site. SAP também tem um blog sobre o [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> A partir de 29 de Maio de 2017, pode utilizar o modelo de implementação Azure Resource Manager, além do modelo de implementação clássica menos preferível para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Resource Manager e ignorar o modelo de implementação clássica.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implantar a solução

A seguinte sequência de capturas de ecrã mostra-lhe como implementar o S/4HANA no Azure ao utilizar o SAP CAL. O processo funciona da mesma forma para outras soluções, como BW/4HANA.

O **soluções** página mostra algumas das soluções com base em SAP CAL HANA disponíveis no Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated Appliance** é do meio:

![Soluções do SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta no SAP CAL
1. Para iniciar sessão para o SAP CAL pela primeira vez, utilize o seu utilizador de S SAP ou outro utilizador registado com o SAP. Em seguida, defina uma conta de SAP CAL, que é utilizada pelo SAP CAL para implementar aplicações no Azure. Na definição da conta, terá de:

    a. Selecione o modelo de implementação no Azure (Resource Manager ou clássico).

    b. Introduza a sua subscrição do Azure. Uma conta de SAP CAL pode ser atribuída a apenas uma subscrição. Se precisar de mais de uma subscrição, terá de criar outra conta de SAP CAL.

    c. Conceder a permissão de SAP CAL para implementar na sua subscrição do Azure.

   > [!NOTE]
   >  Os passos seguintes mostram como criar uma conta de SAP CAL para implementações do Resource Manager. Se já tiver uma conta de SAP CAL que está ligada ao modelo de implementação clássica, *precisa* a seguir estes passos para criar uma nova conta de SAP CAL. A nova conta de SAP CAL tem de implementar no modelo do Resource Manager.

1. Crie uma nova conta de SAP CAL. O **contas** página mostra três opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implementação clássica e já não é preferencial.

    b. **Microsoft Azure** é o novo modelo de implementação do Resource Manager.

    c. **Windows Azure operado pela 21Vianet** é uma opção na China que utiliza o modelo de implementação clássica.

    Para implementar o modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Detalhes da conta do CAL de SAP](./media/cal-s4h/s4h-pic-2a.png)

1. Introduza o Azure **ID de subscrição** que pode ser encontrada no portal do Azure.

   ![Contas do SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Para autorizar o SAP CAL para implementar para a subscrição do Azure que definiu, clique em **autorizar**. A página seguinte é apresentada no separador do browser:

   ![Serviços em nuvem do Internet Explorer no início de sessão](./media/cal-s4h/s4h-pic4c.png)

1. Se mais do que um utilizador estiver listado, selecione a conta da Microsoft que está ligada a ser o coadministrador da subscrição do Azure que selecionou. A página seguinte é apresentada no separador do browser:

   ![Confirmação de serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Clique em **aceitar**. Se a autorização for bem sucedida, a definição de conta do SAP CAL apresenta novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi concluída com êxito.

1. Para atribuir a conta de SAP CAL recentemente criada para o utilizador, introduza o seu **ID de utilizador** na caixa de texto à direita e clique em **Add**.

   ![A associação do utilizador da conta](./media/cal-s4h/s4h-pic8a.png)

1. Para associar a sua conta com o utilizador que utiliza para iniciar sessão para o SAP CAL, clique em **revisão**. 
 
1. Para criar a associação entre o utilizador e a conta de SAP CAL recentemente criada, clique em **criar**.

   ![Utilizador para a associação de conta do SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Criado com êxito uma conta de SAP CAL é capaz de:

- Utilize o modelo de implementação do Resource Manager.
- Implemente sistemas SAP na sua subscrição do Azure.

Agora pode começar a implementar o S/4HANA na sua subscrição de utilizador no Azure.

> [!NOTE]
> Antes de continuar, determine se existem quotas de vCPU do Azure para VMs de série H do Azure. No momento, o SAP CAL utiliza VMs de série H do Azure para implementar algumas das soluções com base em SAP HANA. A subscrição do Azure poderá não ter quaisquer quotas de vCPU de série H para a série H. Nesse caso, poderá ter de contactar o suporte do Azure para obter uma quota de, pelo menos, 16 vCPUs de série H.
> 
> [!NOTE]
> Quando implementa uma solução no Azure no SAP CAL, pode achar que pode escolher apenas uma região do Azure. Para implementar as regiões do Azure que não seja o sugerido pelo SAP CAL, terá de comprar uma subscrição de CAL do SAP. Também poderá ter de abrir uma mensagem com o SAP a sua conta do CAL ativada para entregar em regiões do Azure diferentes dos inicialmente sugeridos.

### <a name="deploy-a-solution"></a>Implementar uma solução

Vamos implementar uma solução a partir da **soluções** página do SAP CAL. O SAP CAL possui duas seqüências para implementar:

- Uma sequência básica que utiliza uma página para definir o implementação do sistema
- Uma sequência de avançada que fornece-lhe determinadas opções sobre tamanhos de VM 

Vamos demonstrar o caminho básico para implementação aqui.

1. Sobre o **detalhes da conta** página, terá de:

    a. Selecione uma conta de SAP CAL. (Utilizar uma conta que está associada a implementar com o modelo de implementação do Resource Manager).

    b. Introduza uma instância **nome**.

    c. Selecione do Azure **região**. O SAP CAL sugere uma região. Se precisar de outra região do Azure e não tiver uma subscrição de SAP CAL, terá de encomendar uma subscrição de CAL SAP.

    d. Introduza um mestre **palavra-passe** para a solução de caracteres de oito ou nove. A palavra-passe é utilizada para os administradores dos diferentes componentes.

   ![Modo do SAP CAL Basic: Criar Instância](./media/cal-s4h/s4h-pic10a.png)

1. Clique em **Create**e na caixa de mensagem que aparece, clique em **OK**.

   ![SAP CAL suportado tamanhos de VM](./media/cal-s4h/s4h-pic10b.png)

1. Na **chave privada** caixa de diálogo, clique em **Store** para armazenar a chave privada no SAP CAL. Para utilizar a proteção de palavra-passe para a chave privada, clique em **transferir**. 

   ![Chave privada do SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Leia o SAP CAL **aviso** da mensagem e clique em **OK**.

   ![Aviso do SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Agora, após a implantação. Após algum tempo, dependendo do tamanho e complexidade da solução (o SAP CAL fornece uma estimativa), o estado é apresentado como ativo e pronto a utilizar.

1. Para localizar as máquinas de virtuais recolhidas com os outros recursos associados num grupo de recursos, aceda ao portal do Azure: 

   ![Objetos de SAP CAL implementados no novo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. No portal do SAP CAL, o estado é apresentado como **Active Directory**. Para ligar à solução, clique em **Connect**. As diferentes opções para ligar para os diferentes componentes são implantadas dentro desta solução.

   ![Instâncias do SAP CAL](./media/cal-s4h/active_solution.png)

1. Antes de poder utilizar uma das opções para ligar para os sistemas implantados, clique em **guia de introdução**. 

   ![Ligar à instância do](./media/cal-s4h/connect_to_solution.png)

    A documentação nomes os utilizadores para cada um dos métodos de conectividade. As palavras-passe para os utilizadores estão definidas para a palavra-passe principal que definiu no início do processo de implantação. Na documentação, os outros utilizadores mais funcionais são listados com as palavras-passe, que pode utilizar para iniciar sessão para o sistema implementado. 

    Por exemplo, se usar a GUI de SAP está pré-instalado na máquina do ambiente de trabalho remoto do Windows, o sistema de S/4 pode ser assim:

   ![SM50 na GUI SAP pré-instalados](./media/cal-s4h/gui_sm50.png)

    Ou, se usar o DBACockpit, a instância pode ser assim:

   ![SM50 na DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Em algumas horas, uma aplicação SAP S/4 bom estado de funcionamento é implementada no Azure.

Se comprasse uma subscrição de SAP CAL, o SAP suporta totalmente o implementações através do SAP CAL no Azure. A fila de suporte é BC-VCM-CAL.







