---
title: Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure | Documentos da Microsoft
description: Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 413e449f005d288f66da0257f5b800733e58eb94
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011271"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
Este artigo descreve como implementar um sistema SAP IDES em execução com o SQL Server e o sistema operativo do Windows no Azure através de SAP Cloud Appliance Library (SAP CAL) 3.0. As capturas de ecrã mostram o processo passo a passo. Para implementar uma solução diferente, siga os mesmos passos.

Para começar com o SAP CAL, vá para o [SAP Cloud Appliance Library](https://cal.sap.com/) Web site. SAP também tem um blog sobre o novo [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> A partir de 29 de Maio de 2017, pode utilizar o modelo de implementação Azure Resource Manager, além do modelo de implementação clássica menos preferível para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Resource Manager e ignorar o modelo de implementação clássica.

Se já criou uma conta de SAP CAL que utiliza o modelo clássico, *tem de criar outra conta de SAP CAL*. Esta conta tem de implementar exclusivamente para o Azure, utilizando o modelo do Resource Manager.

Depois de iniciar sessão para o SAP CAL, a primeira página geralmente leva para o **soluções** página. As soluções oferecidas no SAP CAL firmemente estiverem aumentando, portanto, poderá ter de deslocar-se bastante para encontrar a solução que pretende. O realçado solução SAP IDES baseados em Windows que está disponível exclusivamente no Azure demonstra o processo de implantação:

![Soluções do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta no SAP CAL
1. Para iniciar sessão para o SAP CAL pela primeira vez, utilize o seu utilizador de S SAP ou outro utilizador registado com o SAP. Em seguida, defina uma conta de SAP CAL, que é utilizada pelo SAP CAL para implementar aplicações no Azure. Na definição da conta, terá de:

    a. Selecione o modelo de implementação no Azure (Resource Manager ou clássico).

    b. Introduza a sua subscrição do Azure. Uma conta de SAP CAL pode ser atribuída a apenas uma subscrição. Se precisar de mais de uma subscrição, terá de criar outra conta de SAP CAL.
    
    c. Conceder a permissão de SAP CAL para implementar na sua subscrição do Azure.

   > [!NOTE]
   >  Os passos seguintes mostram como criar uma conta de SAP CAL para implementações do Resource Manager. Se já tiver uma conta de SAP CAL que está ligada ao modelo de implementação clássica, *precisa* a seguir estes passos para criar uma nova conta de SAP CAL. A nova conta de SAP CAL tem de implementar no modelo do Resource Manager.

1. Para criar uma nova conta de SAP CAL, o **contas** página mostra duas opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implementação clássica e já não é preferencial.

    b. **Microsoft Azure** é o novo modelo de implementação do Resource Manager.

    ![Contas do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implementar o modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Contas do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Introduza o Azure **ID de subscrição** que pode ser encontrada no portal do Azure. 

    ![ID de subscrição do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Para autorizar o SAP CAL para implementar para a subscrição do Azure que definiu, clique em **autorizar**. A página seguinte é apresentada no separador do browser:

    ![Serviços em nuvem do Internet Explorer no início de sessão](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Se mais do que um utilizador estiver listado, selecione a conta da Microsoft que está ligada a ser o coadministrador da subscrição do Azure que selecionou. A página seguinte é apresentada no separador do browser:

    ![Confirmação de serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Clique em **aceitar**. Se a autorização for bem sucedida, a definição de conta do SAP CAL apresenta novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi concluída com êxito.

1. Para atribuir a conta de SAP CAL recentemente criada para o utilizador, introduza o seu **ID de utilizador** na caixa de texto à direita e clique em **Add**. 

    ![A associação do utilizador da conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Para associar a sua conta com o utilizador que utiliza para iniciar sessão para o SAP CAL, clique em **revisão**. 

1. Para criar a associação entre o utilizador e a conta de SAP CAL recentemente criada, clique em **criar**.

    ![Utilizador para a associação de conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Criado com êxito uma conta de SAP CAL é capaz de:

- Utilize o modelo de implementação do Resource Manager.
- Implemente sistemas SAP na sua subscrição do Azure.

> [!NOTE]
> Antes de poder implementar a solução SAP IDES com base no Windows e do SQL Server, poderá ter de inscrever-se para uma subscrição de SAP CAL. Caso contrário, a solução pode aparecer como **bloqueado** na página de descrição geral.

### <a name="deploy-a-solution"></a>Implementar uma solução
1. Depois de configurar uma conta de SAP CAL, selecione **solução o SAP IDES em Windows e do SQL Server** solução. Clique em **criar instância**e confirme as condições de utilização e os termos. 

1. Sobre o **modo básico: Criar instância** página, terá de:

    a. Introduza uma instância **nome**.

    b. Selecione do Azure **região**. Poderá ter uma assinatura de SAP CAL para obter várias regiões do Azure, oferecidos.

    c.  Introduza o mestre **palavra-passe** para a solução, conforme mostrado:

    ![Modo do SAP CAL Basic: Criar Instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Clique em **Criar**. Após algum tempo, dependendo do tamanho e complexidade da solução (o SAP CAL fornece uma estimativa), o estado é apresentado como ativo e pronto a utilizar: 

    ![Instâncias do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Para localizar o grupo de recursos e todos os seus objetos que foram criados pela SAP CAL, aceda ao portal do Azure. Pode localizar a máquina virtual, começando com o mesmo nome de instância que foi atribuído no SAP CAL.

    ![Objetos de grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. No portal do SAP CAL, vá para as instâncias implantadas e clique em **Connect**. É apresentada a janela de pop-up seguinte: 

    ![Ligar à instância do](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Antes de poder utilizar uma das opções para ligar para os sistemas implantados, clique em **guia de introdução**. A documentação nomes os utilizadores para cada um dos métodos de conectividade. As palavras-passe para os utilizadores estão definidas para a palavra-passe principal que definiu no início do processo de implantação. Na documentação, os outros utilizadores mais funcionais são listados com as palavras-passe, que pode utilizar para iniciar sessão para o sistema implementado.

    ![Documentação de boas-vindas do SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Em algumas horas, um sistema SAP IDES em bom estado de funcionamento é implementado no Azure.

Se comprasse uma subscrição de SAP CAL, o SAP suporta totalmente o implementações através do SAP CAL no Azure. A fila de suporte é BC-VCM-CAL.

