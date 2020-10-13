---
title: Implementar SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure / Microsoft Docs
description: Implementar SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure
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
ms.openlocfilehash: 56f5072c90a699270a92af317976c8f56bd3e2cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536533"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implementar SAP IDES EHP7 SP3 para SAP ERP 6.0 em Azure
Este artigo descreve como implementar um sistema SAP IDES em funcionamento com o SQL Server e o sistema operativo Windows em Azure através da Sap Cloud Appliance Library (SAP CAL) 3.0. As imagens mostram o processo passo a passo. Para implementar uma solução diferente, siga os mesmos passos.

Para começar com o SAP CAL, aceda ao site da [Biblioteca de Eletrodomésticos SAP Cloud.](https://cal.sap.com/) A SAP também tem um blog sobre a nova [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> A partir de 29 de maio de 2017, pode utilizar o modelo de implementação do Azure Resource Manager para além do modelo de implementação clássico menos preferido para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Gestor de Recursos e ignore o modelo clássico de implementação.

Se já criou uma conta SAP CAL que utiliza o modelo clássico, *precisa de criar outra conta SAP CAL.* Esta conta precisa de ser exclusivamente implantada no Azure utilizando o modelo de Gestor de Recursos.

Depois de iniciar solução para o SAP CAL, a primeira página normalmente leva-o à página **Soluções.** As soluções oferecidas na SAP CAL estão a aumentar constantemente, por isso poderá ser necessário deslocar-se um pouco para encontrar a solução que pretende. A solução SAP IDES baseada no Windows que está disponível exclusivamente no Azure demonstra o processo de implementação:

![Soluções SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para iniciar sôm no SAP CAL pela primeira vez, utilize o seu SAP S-User ou outro utilizador registado no SAP. Em seguida, defina uma conta SAP CAL que é utilizada pela SAP CAL para implantar aparelhos em Azure. Na definição de conta, é necessário:

    a. Selecione o modelo de implementação no Azure (Gestor de Recursos ou clássico).

    b. Insira a sua assinatura Azure. Uma conta SAP CAL pode ser atribuída apenas a uma subscrição. Se precisar de mais do que uma subscrição, tem de criar outra conta SAP CAL.
    
    c. Dê a permissão da SAP CAL para implantar na sua subscrição Azure.

   > [!NOTE]
   >  Os próximos passos mostram como criar uma conta SAP CAL para implementações do Gestor de Recursos. Se já tem uma conta SAP CAL que está ligada ao modelo de implementação clássico, *precisa seguir* estes passos para criar uma nova conta SAP CAL. A nova conta SAP CAL tem de ser implantada no modelo de Gestor de Recursos.

1. Para criar uma nova conta SAP CAL, a página Contas mostra **duas** opções para a Azure: 

    a. **O Microsoft Azure (clássico)** é o modelo clássico de implementação e já não é preferido.

    b. **O Microsoft Azure** é o novo modelo de implementação do Gestor de Recursos.

    ![O Screenshot mostra as contas S A P CAL com o Microsoft Azure chamado.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implementar no modelo gestor de recursos, selecione **Microsoft Azure**.

    ![A screenshot mostra contas S A P CAL.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Introduza o **ID de subscrição Azure** que pode ser encontrado no portal Azure. 

    ![ID de assinatura SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Para autorizar a SAP CAL a implantar na subscrição Azure que definiu, clique em **Autorizor**. A página seguinte aparece no separador do navegador:

    ![Serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Se mais de um utilizador estiver listado, escolha a conta Microsoft que está ligada para ser o coadministrator da subscrição Azure que selecionou. A página seguinte aparece no separador do navegador:

    ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Clique em **Aceitar**. Se a autorização for bem sucedida, a definição de conta SAP CAL volta a ser apresentada. Após pouco tempo, uma mensagem confirma que o processo de autorização foi bem sucedido.

1. Para atribuir a conta SAP CAL recém-criada ao seu utilizador, insira o seu **ID do Utilizador** na caixa de texto à direita e clique em **Adicionar**. 

    ![Conta para associação de utilizadores](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Para associar a sua conta ao utilizador que utiliza para iniciar sinscê-lo no SAP CAL, clique em **'Rever'.** 

1. Para criar a associação entre o seu utilizador e a conta SAP CAL recém-criada, clique em **Criar**.

    ![Associação de utilizadores a conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Criou com sucesso uma conta SAP CAL que é capaz de:

- Utilize o modelo de implementação Resource Manager.
- Coloque os sistemas SAP na sua subscrição Azure.

> [!NOTE]
> Antes de poder implementar a solução SAP IDES com base no Windows e SQL Server, poderá ter de se inscrever para uma subscrição SAP CAL. Caso contrário, a solução pode aparecer como **bloqueado** na página geral.

### <a name="deploy-a-solution"></a>Implementar uma solução
1. Depois de configurar uma conta SAP CAL, selecione **a solução SAP IDES na** solução Windows e SQL Server. Clique **em Criar Instância**e confirme as condições de utilização e termos. 

1. No **modo básico: Criar página de exemplo,** precisa:

    a. Insira um nome **de exemplo**.

    b. Selecione uma **Região**Azure . Você pode precisar de uma assinatura SAP CAL para obter várias regiões Azure oferecidas.

    c.  Introduza a **palavra-passe** principal para a solução, como mostrado:

    ![Modo Básico SAP CAL: Criar instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Clique em **Criar**. Após algum tempo, dependendo do tamanho e complexidade da solução (a SAP CAL fornece uma estimativa), o estado é mostrado como ativo e pronto a ser utilizado: 

    ![Casos SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Para encontrar o grupo de recursos e todos os seus objetos que foram criados pela SAP CAL, vá ao portal Azure. A máquina virtual pode ser encontrada a partir do mesmo nome que foi dado na SAP CAL.

    ![Objetos de grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. No portal SAP CAL, vá às instâncias implementadas e clique em **Connect**. Aparece a seguinte janela pop-up: 

    ![Ligar-se à Instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Antes de utilizar uma das opções para ligar aos sistemas implantados, clique em **Guia de Início.** A documentação designa os utilizadores por cada um dos métodos de conectividade. As palavras-passe para esses utilizadores estão definidas na palavra-passe principal que definiu no início do processo de implementação. Na documentação, outros utilizadores mais funcionais estão listados com as suas palavras-passe, que pode utilizar para iniciar sação no sistema implantado.

    ![Documentação de boas-vindas SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Dentro de poucas horas, um sistema SAP IDES saudável é implantado em Azure.

Se comprou uma assinatura SAP CAL, a SAP suporta totalmente as implementações através da SAP CAL on Azure. A fila de apoio é BC-VCM-CAL.

