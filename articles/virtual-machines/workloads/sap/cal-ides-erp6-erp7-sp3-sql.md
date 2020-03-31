---
title: Implantação SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure Microsoft Docs
description: Implemente SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
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
ms.openlocfilehash: 3efd92226b7c69590f3960458ffec49b63b8364f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616694"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implemente SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure
Este artigo descreve como implementar um sistema SAP IDES em funcionamento com o SQL Server e o sistema operativo Windows no Azure através da Biblioteca de Eletrodomésticos SAP Cloud (SAP CAL) 3.0. As imagens mostram o processo passo a passo. Para implementar uma solução diferente, siga os mesmos passos.

Para começar com o SAP CAL, vá ao site da Biblioteca de Aparelhos De [Nuvem SAP.](https://cal.sap.com/) A SAP também tem um blog sobre a nova [Biblioteca de Eletrodomésticos SAP Cloud 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> A partir de 29 de maio de 2017, pode utilizar o modelo de implementação do Gestor de Recursos Azure, além do modelo de implantação clássico menos preferido para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Gestor de Recursos e ignore o modelo de implementação clássico.

Se já criou uma conta SAP CAL que utiliza o modelo clássico, *precisa de criar outra conta SAP CAL*. Esta conta precisa de ser implantada exclusivamente no Azure utilizando o modelo Degestor de Recursos.

Depois de iniciar sessão no SAP CAL, a primeira página normalmente leva-o à página **Soluções.** As soluções oferecidas no SAP CAL estão a aumentar constantemente, por isso poderá saquear um pouco para encontrar a solução que deseja. A solução SAP IDES baseada no Windows que está disponível exclusivamente no Azure demonstra o processo de implementação:

![Soluções SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta no SAP CAL
1. Para iniciar sessão no SAP CAL pela primeira vez, utilize o seu S-User SAP ou outro utilizador registado no SAP. Em seguida, defina uma conta SAP CAL que é utilizada pelo SAP CAL para implantar aparelhos no Azure. Na definição de conta, é necessário:

    a. Selecione o modelo de implantação no Azure (Gestor de Recursos ou clássico).

    b. Insira a sua assinatura Azure. Uma conta SAP CAL só pode ser atribuída a uma subscrição. Se precisa de mais do que uma subscrição, precisa de criar outra conta SAP CAL.
    
    c. Dê permissão ao SAP CAL para se instalar na sua subscrição Azure.

   > [!NOTE]
   >  Os próximos passos mostram como criar uma conta SAP CAL para implementações do Gestor de Recursos. Se já tem uma conta SAP CAL ligada ao modelo de implementação clássico, *precisa seguir* estes passos para criar uma nova conta SAP CAL. A nova conta SAP CAL precisa de ser implementada no modelo de Gestor de Recursos.

1. Para criar uma nova conta SAP CAL, a página **Contas** mostra duas opções para o Azure: 

    a. O **Microsoft Azure (clássico)** é o modelo clássico de implementação e já não é preferido.

    b. O **Microsoft Azure** é o novo modelo de implementação do Gestor de Recursos.

    ![Contas SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implementar no modelo Degestor de Recursos, selecione **Microsoft Azure**.

    ![Contas SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Introduza o ID de **subscrição** Azure que pode ser encontrado no portal Azure. 

    ![ID de subscrição SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Para autorizar o SAP CAL a ser implantado na subscrição Azure que definiu, clique **em Autorizar**. A página seguinte aparece no separador do navegador:

    ![Sessão de serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Se estiver listado mais de um utilizador, escolha a conta Microsoft que está ligada a ser o coadministrador da subscrição Azure selecionada. A página seguinte aparece no separador do navegador:

    ![Confirmação de serviços na nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Clique em **Aceitar**. Se a autorização for bem sucedida, a definição de conta SAP CAL volta a ser demonstrada. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi bem sucedido.

1. Para atribuir a conta SAP CAL recém-criada ao seu utilizador, introduza o ID do **utilizador** na caixa de texto à direita e clique em **Adicionar**. 

    ![Conta à associação de utilizadores](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Para associar a sua conta ao utilizador que utiliza para iniciar sessão no SAP CAL, clique em **Rever**. 

1. Para criar a associação entre o utilizador e a recém-criada conta SAP CAL, clique em **Criar**.

    ![Associação de utilizadores para conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Criou com sucesso uma conta SAP CAL capaz de:

- Utilize o modelo de implementação Resource Manager.
- Implemente sistemas SAP na sua subscrição Azure.

> [!NOTE]
> Antes de poder implementar a solução SAP IDES com base no Windows e no SQL Server, poderá ter de se inscrever para uma subscrição SAP CAL. Caso contrário, a solução pode aparecer como **Bloqueada** na página geral.

### <a name="deploy-a-solution"></a>Implementar uma solução
1. Depois de configurar uma conta SAP CAL, selecione **a solução SAP IDES na solução Windows e SQL Server.** Clique em **Criar Instância**e confirmar as condições de utilização e termos. 

1. No **Modo Básico: Criar** página de instância, é necessário:

    a. Introduza uma instância **Nome**.

    b. Selecione uma **região**azure . Você pode precisar de uma subscrição SAP CAL para obter várias regiões Azure oferecidas.

    c.  Introduza a **palavra-passe** principal para a solução, como mostrado:

    ![Modo Básico SAP CAL: Criar instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Clique em **Criar**. Após algum tempo, dependendo da dimensão e complexidade da solução (o SAP CAL fornece uma estimativa), o estado é mostrado como ativo e pronto a ser utilizado: 

    ![Instâncias SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Para encontrar o grupo de recursos e todos os seus objetos que foram criados pelo SAP CAL, vá ao portal Azure. A máquina virtual pode ser encontrada a partir do mesmo nome de instância que foi dado no SAP CAL.

    ![Objetos de grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. No portal SAP CAL, vá às instâncias implementadas e clique em **Connect**. Aparece a seguinte janela pop-up: 

    ![Ligar à Instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Antes de poder utilizar uma das opções para se ligar aos sistemas implantados, clique no **Guia iniciar**a partida . A documentação dá nomes aos utilizadores para cada um dos métodos de conectividade. As palavras-passe para esses utilizadores são definidas para a palavra-passe principal que definiu no início do processo de implementação. Na documentação, outros utilizadores mais funcionais estão listados com as suas palavras-passe, que pode utilizar para iniciar sessão no sistema implementado.

    ![Documentação de boas-vindas sAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Dentro de algumas horas, um sistema Saudável SAP IDES é implantado em Azure.

Se comprou uma subscrição SAP CAL, o SAP suporta totalmente as implementações através do SAP CAL em Azure. A fila de apoio é BC-VCM-CAL.

