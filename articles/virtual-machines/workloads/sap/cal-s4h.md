---
title: Implante SAP S/4HANA ou BW/4HANA num Azure VM [ Microsoft Docs
description: Implementar SAP S/4HANA ou BW/4HANA num VM Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
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
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616208"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implementar SAP S/4HANA ou BW/4HANA em Azure
Este artigo descreve como implantar S/4HANA em Azure utilizando a Biblioteca de Eletrodomésticos SAP Cloud (SAP CAL) 3.0. Para implementar outras soluções baseadas em SAP HANA, como a BW/4HANA, siga os mesmos passos.

> [!NOTE]
> Para mais informações sobre o SAP CAL, vá ao site da Biblioteca de Aparelhos De [Nuvem SAP.](https://cal.sap.com/) A SAP também tem um blog sobre a [Biblioteca de Eletrodomésticos SAP Cloud 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> A partir de 29 de maio de 2017, pode utilizar o modelo de implementação do Gestor de Recursos Azure, além do modelo de implantação clássico menos preferido para implementar o SAP CAL. Recomendamos que utilize o novo modelo de implementação do Gestor de Recursos e ignore o modelo de implementação clássico.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implementar a solução

A seguinte sequência de imagens mostra-lhe como implantar S/4HANA em Azure utilizando o SAP CAL. O processo funciona da mesma forma para outras soluções, como a BW/4HANA.

A página Solutions mostra **algumas** das soluções baseadas em SAP CAL HANA disponíveis no Azure. **SAP S/4HANA 1610 FPS01, Aparelho totalmente ativado** está na linha média:

![Soluções SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta no SAP CAL
1. Para iniciar sessão no SAP CAL pela primeira vez, utilize o seu S-User SAP ou outro utilizador registado no SAP. Em seguida, defina uma conta SAP CAL que é utilizada pelo SAP CAL para implantar aparelhos no Azure. Na definição de conta, é necessário:

    a. Selecione o modelo de implantação no Azure (Gestor de Recursos ou clássico).

    b. Insira a sua assinatura Azure. Uma conta SAP CAL só pode ser atribuída a uma subscrição. Se precisa de mais do que uma subscrição, precisa de criar outra conta SAP CAL.

    c. Dê permissão ao SAP CAL para se instalar na sua subscrição Azure.

   > [!NOTE]
   >  Os próximos passos mostram como criar uma conta SAP CAL para implementações do Gestor de Recursos. Se já tem uma conta SAP CAL ligada ao modelo de implementação clássico, *precisa seguir* estes passos para criar uma nova conta SAP CAL. A nova conta SAP CAL precisa de ser implementada no modelo de Gestor de Recursos.

1. Crie uma nova conta SAP CAL. A página **Contas** mostra três opções para Azure: 

    a. O **Microsoft Azure (clássico)** é o modelo clássico de implementação e já não é preferido.

    b. O **Microsoft Azure** é o novo modelo de implementação do Gestor de Recursos.

    c. O **Windows Azure operado pela 21Vianet** é uma opção na China que utiliza o modelo de implementação clássico.

    Para implementar no modelo Degestor de Recursos, selecione **Microsoft Azure**.

    ![Detalhes da conta SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Introduza o ID de **subscrição** Azure que pode ser encontrado no portal Azure.

   ![Contas SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Para autorizar o SAP CAL a ser implantado na subscrição Azure que definiu, clique **em Autorizar**. A página seguinte aparece no separador do navegador:

   ![Sessão de serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Se estiver listado mais de um utilizador, escolha a conta Microsoft que está ligada a ser o coadministrador da subscrição Azure selecionada. A página seguinte aparece no separador do navegador:

   ![Confirmação de serviços na nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Clique em **Aceitar**. Se a autorização for bem sucedida, a definição de conta SAP CAL volta a ser demonstrada. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi bem sucedido.

1. Para atribuir a conta SAP CAL recém-criada ao seu utilizador, introduza o ID do **utilizador** na caixa de texto à direita e clique em **Adicionar**.

   ![Conta à associação de utilizadores](./media/cal-s4h/s4h-pic8a.png)

1. Para associar a sua conta ao utilizador que utiliza para iniciar sessão no SAP CAL, clique em **Rever**. 
 
1. Para criar a associação entre o utilizador e a recém-criada conta SAP CAL, clique em **Criar**.

   ![Utilizador da associação de conta SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Criou com sucesso uma conta SAP CAL capaz de:

- Utilize o modelo de implementação Resource Manager.
- Implemente sistemas SAP na sua subscrição Azure.

Agora pode começar a implementar S/4HANA na subscrição do seu utilizador no Azure.

> [!NOTE]
> Antes de continuar, determine se tem quotas Azure vCPU para VMs da série H. Neste momento, o SAP CAL utiliza VMs de Série H do Azure para implementar algumas das soluções baseadas em SAP HANA. A sua subscrição Azure pode não ter quaisquer quotas vCPU da Série H para série H. Em caso afirmativo, poderá ter de contactar o suporte do Azure para obter uma quota de, pelo menos, 16 VCPUs da Série H.
> 
> [!NOTE]
> Quando implementar uma solução em Azure no SAP CAL, poderá descobrir que só pode escolher uma região azure. Para se implantar em regiões de Azure que não a sugerida pelo SAP CAL, precisa de adquirir uma subscrição CAL da SAP. Também poderá ter de abrir uma mensagem com o SAP para ter a sua conta CAL habilitada a entregar em regiões Azure que não as inicialmente sugeridas.

### <a name="deploy-a-solution"></a>Implementar uma solução

Vamos implementar uma solução a partir da página **Soluções** do SAP CAL. O SAP CAL tem duas sequências para implementar:

- Uma sequência básica que usa uma página para definir o sistema a ser implantado
- Uma sequência avançada que lhe dá certas escolhas em tamanhos VM 

Demonstramos o caminho básico para a implantação aqui.

1. Na página dados da **Conta,** é necessário:

    a. Selecione uma conta SAP CAL. (Utilize uma conta associada a implantar com o modelo de implementação do Gestor de Recursos.)

    b. Introduza uma instância **Nome**.

    c. Selecione uma **região**azure . O SAP CAL sugere uma região. Se precisa de outra região do Azure e não tem uma subscrição SAP CAL, precisa encomendar uma subscrição CAL com SAP.

    d. Introduza uma **palavra-passe** principal para a solução de oito ou nove caracteres. A palavra-passe é utilizada para os administradores dos diferentes componentes.

   ![Modo Básico SAP CAL: Criar instância](./media/cal-s4h/s4h-pic10a.png)

1. Clique em **Criar**, e na caixa de mensagens que aparece, clique **OK**.

   ![Tamanhos VM suportados SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. Na caixa de diálogo **Private Key,** clique em **Armazenar** para armazenar a chave privada no SAP CAL. Para utilizar a proteção de palavra-passe para a chave privada, clique em **Baixar**. 

   ![Chave Privada SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Leia a mensagem de **aviso** SAP CAL e clique **ok**.

   ![Aviso SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Agora o destacamento acontece. Após algum tempo, dependendo da dimensão e complexidade da solução (o SAP CAL fornece uma estimativa), o estado é mostrado como ativo e pronto para ser utilizado.

1. Para encontrar as máquinas virtuais recolhidas com os outros recursos associados num grupo de recursos, vá ao portal Azure: 

   ![Objetos SAP CAL implantados no novo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. No portal SAP CAL, o estado aparece como **Ativo**. Para se ligar à solução, clique em **Ligar**. Diferentes opções para se conectar aos diferentes componentes são implementadas dentro desta solução.

   ![Instâncias SAP CAL](./media/cal-s4h/active_solution.png)

1. Antes de poder utilizar uma das opções para se ligar aos sistemas implantados, clique no **Guia iniciar**a partida . 

   ![Ligar à Instância](./media/cal-s4h/connect_to_solution.png)

    A documentação dá nomes aos utilizadores para cada um dos métodos de conectividade. As palavras-passe para esses utilizadores são definidas para a palavra-passe principal que definiu no início do processo de implementação. Na documentação, outros utilizadores mais funcionais estão listados com as suas palavras-passe, que pode utilizar para iniciar sessão no sistema implementado. 

    Por exemplo, se utilizar o SAP GUI pré-instalado na máquina de desktop remoto do Windows, o sistema S/4 pode parecer o seguinte:

   ![SM50 no SAP GUI pré-instalado](./media/cal-s4h/gui_sm50.png)

    Ou se usar o DBACockpit, a instância pode parecer assim:

   ![SM50 no DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Dentro de algumas horas, um aparelho SAP S/4 saudável é implantado em Azure.

Se comprou uma subscrição SAP CAL, o SAP suporta totalmente as implementações através do SAP CAL em Azure. A fila de apoio é BC-VCM-CAL.







