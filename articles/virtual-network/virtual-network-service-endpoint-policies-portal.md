---
title: Criar e associar políticas de ponto final de serviço - Portal Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, aprenda a configurar e associar políticas de ponto final de serviço utilizando o portal Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004964"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Criar, alterar ou eliminar a política de ponto final de serviço utilizando o portal Azure

As políticas de ponto final de serviço permitem filtrar o tráfego de rede virtual para recursos específicos do Azure, sobre os pontos finais do serviço. Se não está familiarizado com as políticas de ponto final de serviço, consulte a [visão geral das políticas de ponto final do serviço](virtual-network-service-endpoint-policies-overview.md) para saber mais.

 Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de ponto final de serviço
> * Criar uma definição de política de ponto final de serviço
> * Criar uma rede virtual com uma sub-rede
> * Associar uma política de ponto final de serviço a uma sub-rede

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto final de serviço

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. No painel de pesquisa, escreva "política de ponto final de serviço" e selecione **a política de ponto final do Serviço** e, em seguida, selecione **Criar**.

![Criar política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Insira ou selecione as **seguintes** informações no Básico 

   - Subscrição : Selecione a sua subscrição para a política
   - Grupo de recursos : Selecione **Criar novo** e insira *o myResourceGroup*
   - Nome : myEndpointPolicy
   - Localização : Central US
 
   ![Criar básicos básicos da política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. **Selecione + Adicionar** em **Recursos** e insira ou selecione as seguintes informações em Adicionar um painel **de recursos**

   - Serviço : Apenas **o Microsoft.Storage** está disponível com políticas de endpoint de serviço
   - Âmbito : Selecione uma fora da **Conta Única,** **Todas as contas em subscrição** e **todas as contas no grupo de recursos**
   - Subscrição : Selecione a sua subscrição para conta de armazenamento. As contas de política e armazenamento podem estar em diferentes subscrições.
   - Grupo de recursos : Selecione o seu grupo de recursos. Necessário, se o âmbito for definido como "Todas as contas em grupo de recursos" ou "Conta Única".  
   - Recurso : Selecione o seu recurso de armazenamento Azure sob o grupo de subscrição ou recursos selecionados
   - Clique no botão **Adicionar** na parte inferior para terminar adicionando o recurso

   ![Definição de política de ponto final de serviço - recurso](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Adicione mais recursos repetindo os passos acima necessários

5. Opcional: Introduzir ou selecionar, as seguintes informações em **Tags**:
   
   - Tecla : Selecione a sua chave para a apólice. Ex: Dept     
   - Valor : Introduza o par de valor para a chave. Ex: Finanças

6. Selecione **Rever + Criar**. Validar a informação e clicar **criar.** Para edição adicional, clique em **Anterior**. 

   ![Criar validações finais da política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Ver políticas de ponto final 

1. Na caixa *de todos os serviços* no portal, comece a digitar *as políticas de ponto final do serviço.* Selecione **As Políticas de Ponto final de serviço**.
2. Em **Subscrições**, selecione o seu grupo de subscrição e recursos, como mostrado na imagem seguinte

   ![Mostrar política](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecione a política e clique em **Definições de Política** para visualizar ou adicionar mais definições de política.

   ![Mostrar definições de política](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecione **sub-redes associadas** para visualizar as sub-redes que a apólice está associada. Se ainda não houver sub-rede, siga as instruções no passo seguinte.

   ![Subesí redes associadas](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associar uma apólice a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acedidos a partir da sub-rede são adicionados à definição de política antes de associar a política à sub-rede dada. Uma vez que a política esteja associada, apenas o acesso aos recursos *listados de admissão* será permitido sobre os pontos finais de serviço. 
>
> Também certifique-se de que não existem serviços geridos da Azure na sub-rede que está a ser associada à política de ponto final de serviço

- Antes de poder associar uma apólice a uma sub-rede, tem de criar uma rede virtual e uma sub-rede. Consulte o artigo [Criar uma Rede Virtual](./quick-create-portal.md) para obter ajuda com isto.

- Uma vez que tenha a rede virtual e a sub-rede configuradas, precisa configurar pontos finais de serviço de rede virtual para armazenamento Azure. Na lâmina de rede virtual, selecione **os pontos finais** de serviço , e no painel seguinte selecione **Microsoft.Storage** e em **Subnets** selecione o VNet ou Subnet desejado

- Agora, pode optar por selecionar a Política de Ponto de Serviço da parte de down-down no painel acima referido se já criou as políticas de Ponto final de serviço antes de configurar o Ponto de Final de Serviço para a Sub-rede, como mostrado abaixo

    ![Sub-rede associada ao criar ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OU se estiver a associar as políticas de Endpoint de Serviço após o serviço Endpoints já estarem configurados, pode optar por associar a sub-rede a partir da lâmina de Política de Ponto final de serviço, navegando no painel **de subnetas associado,** como mostrado abaixo

    ![Sub-rede associada via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>O acesso aos recursos de armazenamento Azure em todas as regiões será restringido de acordo com a Política de Ponto de Serviço a partir desta sub-rede.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma política de ponto final de serviço e associou-a a uma sub-rede. Para saber mais sobre as políticas de ponto final de serviço, consulte a [visão geral das políticas de ponto final do serviço.](virtual-network-service-endpoint-policies-overview.md)
