---
title: Desdobre o SAP S/4HANA ou o BW/4HANA num | VM Azure Microsoft Docs
description: Implementar SAP S/4HANA ou BW/4HANA num Azure VM
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 4b739efcf2695a5385351f531efb6201bbea1cd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671935"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implementar SAP S/4HANA ou BW/4HANA em Azure
Este artigo descreve como implantar S/4HANA no Azure utilizando a Sap Cloud Appliance Library (SAP CAL) 3.0. Para implementar outras soluções baseadas em SAP HANA, como bW/4HANA, siga os mesmos passos.

> [!NOTE]
> Para mais informações sobre o SAP CAL, aceda ao site da [Biblioteca de Eletrodomésticos SAP Cloud.](https://cal.sap.com/) O SAP também tem um blog sobre a [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> A partir de 29 de maio de 2017, pode utilizar o modelo de implementação do Azure Resource Manager para além do modelo de implementação clássico menos preferido para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Gestor de Recursos e ignore o modelo clássico de implementação.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implementar a solução

A sequência seguinte de imagens mostra-lhe como implantar S/4HANA em Azure utilizando o SAP CAL. O processo funciona da mesma forma para outras soluções, como a BW/4HANA.

A página Solutions mostra **algumas** das soluções baseadas em SAP CAL HANA disponíveis no Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated Aparelho** está na linha do meio:

![Soluções SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para iniciar sôm no SAP CAL pela primeira vez, utilize o seu SAP S-User ou outro utilizador registado no SAP. Em seguida, defina uma conta SAP CAL que é utilizada pela SAP CAL para implantar aparelhos em Azure. Na definição de conta, é necessário:

    a. Selecione o modelo de implementação no Azure (Gestor de Recursos ou clássico).

    b. Insira a sua assinatura Azure. Uma conta SAP CAL pode ser atribuída apenas a uma subscrição. Se precisar de mais do que uma subscrição, tem de criar outra conta SAP CAL.

    c. Dê a permissão da SAP CAL para implantar na sua subscrição Azure.

   > [!NOTE]
   >  Os próximos passos mostram como criar uma conta SAP CAL para implementações do Gestor de Recursos. Se já tem uma conta SAP CAL que está ligada ao modelo de implementação clássico, *precisa seguir* estes passos para criar uma nova conta SAP CAL. A nova conta SAP CAL tem de ser implantada no modelo de Gestor de Recursos.

1. Criar uma nova conta SAP CAL. A página **Contas** mostra três escolhas para Azure: 

    a. **O Microsoft Azure (clássico)** é o modelo clássico de implementação e já não é preferido.

    b. **O Microsoft Azure** é o novo modelo de implementação do Gestor de Recursos.

    c. **O Windows Azure operado pela 21Vianet** é uma opção na China que utiliza o modelo de implementação clássico.

    Para implementar no modelo gestor de recursos, selecione **Microsoft Azure**.

    ![Detalhes da conta SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Introduza o **ID de subscrição Azure** que pode ser encontrado no portal Azure.

   ![Contas SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Para autorizar a SAP CAL a implantar na subscrição Azure que definiu, clique em **Autorizor**. A página seguinte aparece no separador do navegador:

   ![Serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Se mais de um utilizador estiver listado, escolha a conta Microsoft que está ligada para ser o coadministrator da subscrição Azure que selecionou. A página seguinte aparece no separador do navegador:

   ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Clique em **Aceitar**. Se a autorização for bem sucedida, a definição de conta SAP CAL volta a ser apresentada. Após pouco tempo, uma mensagem confirma que o processo de autorização foi bem sucedido.

1. Para atribuir a conta SAP CAL recém-criada ao seu utilizador, insira o seu **ID do Utilizador** na caixa de texto à direita e clique em **Adicionar**.

   ![Conta para associação de utilizadores](./media/cal-s4h/s4h-pic8a.png)

1. Para associar a sua conta ao utilizador que utiliza para iniciar sinscê-lo no SAP CAL, clique em **'Rever'.** 
 
1. Para criar a associação entre o seu utilizador e a conta SAP CAL recém-criada, clique em **Criar**.

   ![Associação de conta SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Criou com sucesso uma conta SAP CAL que é capaz de:

- Utilize o modelo de implementação Resource Manager.
- Coloque os sistemas SAP na sua subscrição Azure.

Agora pode começar a colocar S/4HANA na subscrição do utilizador em Azure.

> [!NOTE]
> Antes de continuar, determine se tem quotas Azure vCPU para VMs da série H do Azure. Neste momento, a SAP CAL utiliza VMs da Série H de Azure para implementar algumas das soluções baseadas em SAP HANA. A sua subscrição Azure pode não ter quotas VCPU da Série H para série H. Em caso afirmativo, poderá ter de contactar o suporte da Azure para obter uma quota de, pelo menos, 16 vCPUs da Série H.
> 
> [!NOTE]
> Quando implementar uma solução sobre o Azure na SAP CAL, poderá descobrir que pode escolher apenas uma região de Azure. Para se deslocar para regiões de Azure que não a sugerida pela SAP CAL, você precisa comprar uma assinatura CAL da SAP. Também poderá ter de abrir uma mensagem com a SAP para ter a sua conta CAL habilitada a entregar em regiões de Azure que não as inicialmente sugeridas.

### <a name="deploy-a-solution"></a>Implementar uma solução

Vamos implementar uma solução a partir da página **Soluções** da SAP CAL. A SAP CAL tem duas sequências para implementar:

- Uma sequência básica que usa uma página para definir o sistema a ser implementado
- Uma sequência avançada que lhe dá certas escolhas em tamanhos VM 

Demonstramos o caminho básico para a implantação aqui.

1. Na página Detalhes da **Conta,** tem de:

    a. Selecione uma conta SAP CAL. (Utilize uma conta associada a ser implantada com o modelo de implementação do Gestor de Recursos.)

    b. Insira um nome **de exemplo**.

    c. Selecione uma **Região** Azure . A SAP CAL sugere uma região. Se precisar de outra região de Azure e não tiver uma subscrição SAP CAL, precisa de encomendar uma assinatura CAL com SAP.

    d. Introduza uma **palavra-passe** principal para a solução de oito ou nove caracteres. A palavra-passe é utilizada para os administradores dos diferentes componentes.

   ![Modo Básico SAP CAL: Criar instância](./media/cal-s4h/s4h-pic10a.png)

1. Clique em **Criar**, e na caixa de mensagens que aparece, clique **em OK**.

   ![Tamanhos VM suportados pela SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. Na caixa de diálogo **Private Key,** clique em **Loja** para armazenar a chave privada na SAP CAL. Para utilizar a proteção de palavras-passe para a chave privada, clique em **Baixar**. 

   ![Chave Privada SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Leia a mensagem de **aviso** SAP CAL e clique **em OK**.

   ![Aviso SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Agora a implantação ocorre. Após algum tempo, dependendo do tamanho e complexidade da solução (a SAP CAL fornece uma estimativa), o estado é mostrado como ativo e pronto a ser utilizado.

1. Para encontrar as máquinas virtuais recolhidas com os outros recursos associados num grupo de recursos, vá ao portal Azure: 

   ![Objetos SAP CAL implantados no novo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. No portal SAP CAL, o estado aparece como **Ativo**. Para ligar à solução, clique em **Ligar**. São implementadas diferentes opções de ligação aos diferentes componentes dentro desta solução.

   ![Casos SAP CAL](./media/cal-s4h/active_solution.png)

1. Antes de utilizar uma das opções para ligar aos sistemas implantados, clique em **Guia de Início.** 

   ![Ligar-se à Instância](./media/cal-s4h/connect_to_solution.png)

    A documentação designa os utilizadores por cada um dos métodos de conectividade. As palavras-passe para esses utilizadores estão definidas na palavra-passe principal que definiu no início do processo de implementação. Na documentação, outros utilizadores mais funcionais estão listados com as suas palavras-passe, que pode utilizar para iniciar sação no sistema implantado. 

    Por exemplo, se utilizar o SAP GUI pré-instalado na máquina de desktop remoto do Windows, o sistema S/4 poderá ser assim:

   ![SM50 no SAP GUI pré-instalado](./media/cal-s4h/gui_sm50.png)

    Ou se usar o DBACockpit, o caso pode parecer assim:

   ![SM50 no DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Dentro de algumas horas, um aparelho SAP S/4 saudável é implantado em Azure.

Se comprou uma assinatura SAP CAL, a SAP suporta totalmente as implementações através da SAP CAL on Azure. A fila de apoio é BC-VCM-CAL.







