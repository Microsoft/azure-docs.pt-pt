---
title: Implantar SAP IDES EHP7 SP3 para SAP ERP 6,0 no Azure | Microsoft Docs
description: Implantar SAP IDES EHP7 SP3 para SAP ERP 6,0 no Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 66921280403027d1723b27f104b42d2c83271213
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100074"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implantar SAP IDES EHP7 SP3 para SAP ERP 6,0 no Azure
Este artigo descreve como implantar um sistema SAP IDES executado com SQL Server e o sistema operacional Windows no Azure por meio da SAP CAL (SAP Cloud Appliance Library) 3,0. As capturas de tela mostram o processo passo a passo. Para implantar uma solução diferente, siga as mesmas etapas.

Para começar com o SAP CAL, vá para o site do [SAP Cloud Appliance library](https://cal.sap.com/) . O SAP também tem um blog sobre a nova [SAP Cloud Appliance Library 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> A partir de 29 de maio de 2017, você pode usar o modelo de implantação Azure Resource Manager além do modelo de implantação clássico menos preferido para implantar o SAP CAL. Recomendamos que você use o novo modelo de implantação do Gerenciador de recursos e Desconsidere o modelo de implantação clássico.

Se você já criou uma conta da SAP CAL que usa o modelo clássico, *precisará criar outra conta da SAP Cal*. Essa conta precisa ser implantada exclusivamente no Azure usando o modelo do Resource Manager.

Depois de entrar no SAP CAL, a primeira página geralmente leva você para a página de **soluções** . As soluções oferecidas na SAP CAL estão aumentando constantemente, portanto, talvez seja necessário rolar um pouco para encontrar a solução desejada. A solução SAP IDES destacada em Windows, que está disponível exclusivamente no Azure, demonstra o processo de implantação:

![Soluções da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para entrar no SAP CAL pela primeira vez, use o SAP S-User ou outro usuário registrado com o SAP. Em seguida, defina uma conta da SAP CAL que é usada pela SAP CAL para implantar dispositivos no Azure. Na definição de conta, você precisa:

    a. Selecione o modelo de implantação no Azure (Resource Manager ou clássico).

    b. Insira sua assinatura do Azure. Uma conta da SAP CAL pode ser atribuída somente a uma assinatura. Se precisar de mais de uma assinatura, você precisará criar outra conta da SAP CAL.
    
    c. Conceda à permissão SAP CAL para implantar em sua assinatura do Azure.

   > [!NOTE]
   >  As próximas etapas mostram como criar uma conta da SAP CAL para implantações do Gerenciador de recursos. Se você já tiver uma conta da SAP CAL vinculada ao modelo de implantação clássico, você *precisará* seguir estas etapas para criar uma nova conta da SAP Cal. A nova conta da SAP CAL precisa ser implantada no modelo do Resource Manager.

1. Para criar uma nova conta da SAP CAL, a página **contas** mostra duas opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implantação clássico e não é mais preferencial.

    b. **Microsoft Azure** é o novo modelo de implantação do Gerenciador de recursos.

    ![Contas da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implantar no modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Contas da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Insira a **ID de assinatura** do Azure que pode ser encontrada no portal do Azure. 

    ![ID da assinatura do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Para autorizar o SAP CAL a implantar na assinatura do Azure que você definiu, clique em **autorizar**. A página a seguir aparece na guia navegador:

    ![Entrada de serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Se mais de um usuário estiver listado, escolha o conta Microsoft que está vinculado a ser o coadministrador da assinatura do Azure que você selecionou. A página a seguir aparece na guia navegador:

    ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Clique em **aceitar**. Se a autorização for bem-sucedida, a definição de conta da SAP CAL será exibida novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi bem-sucedido.

1. Para atribuir a conta do SAP CAL recém-criada ao usuário, insira sua **ID de usuário** na caixa de texto à direita e clique em **Adicionar**. 

    ![Conta para associação de usuário](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Para associar sua conta ao usuário que você usa para entrar na SAP CAL, clique em **examinar**. 

1. Para criar a associação entre o usuário e a conta do SAP CAL recém-criada, clique em **criar**.

    ![Usuário para associação de conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Você criou com êxito uma conta da SAP CAL que é capaz de:

- Use o modelo de implantação do Gerenciador de recursos.
- Implante sistemas SAP em sua assinatura do Azure.

> [!NOTE]
> Antes de implantar a solução SAP IDES com base no Windows e SQL Server, talvez seja necessário inscrever-se para uma assinatura da SAP CAL. Caso contrário, a solução pode aparecer como **bloqueada** na página Visão geral.

### <a name="deploy-a-solution"></a>Implantar uma solução
1. Depois de configurar uma conta da SAP CAL, selecione **a solução SAP IDES no Windows e SQL Server** solução. Clique em **criar instância**e confirme as condições de uso e termos. 

1. No modo **básico: Criar a** página de instância, você precisa:

    a. Insira um **nome**de instância.

    b. Selecione uma **região**do Azure. Talvez você precise de uma assinatura da SAP CAL para obter várias regiões do Azure oferecidas.

    c.  Insira a **senha** mestra da solução, conforme mostrado:

    ![Modo básico da SAP CAL: Criar Instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Clique em **Criar**. Após algum tempo, dependendo do tamanho e da complexidade da solução (a SAP CAL fornece uma estimativa), o status é mostrado como ativo e pronto para uso: 

    ![Instâncias do SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Para localizar o grupo de recursos e todos os seus objetos que foram criados pela SAP CAL, vá para a portal do Azure. A máquina virtual pode ser encontrada começando com o mesmo nome de instância que foi fornecido no SAP CAL.

    ![Objetos do grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. No portal do SAP CAL, vá para as instâncias implantadas e clique em **conectar**. A seguinte janela pop-up é exibida: 

    ![Conectar-se à instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Para poder usar uma das opções para se conectar aos sistemas implantados, clique **introdução guia**. A documentação nomeia os usuários para cada um dos métodos de conectividade. As senhas para esses usuários são definidas com a senha mestra que você definiu no início do processo de implantação. Na documentação, outros usuários mais funcionais são listados com suas senhas, que você pode usar para entrar no sistema implantado.

    ![Documentação de boas-vindas do SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Em algumas horas, um sistema SAP IDES íntegro é implantado no Azure.

Se você comprou uma assinatura da SAP CAL, o SAP dá suporte total a implantações por meio do SAP CAL no Azure. A fila de suporte é BC-VCM-CAL.

