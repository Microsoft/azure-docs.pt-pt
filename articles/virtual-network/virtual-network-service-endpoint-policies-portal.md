---
title: Criar e associar políticas de endpoint de serviço - portal Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, aprenda a configurar e a associar políticas de ponto final de serviço utilizando o portal Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651275"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Criar, alterar ou eliminar a política de ponto final de serviço utilizando o portal Azure

As políticas de ponto final do serviço permitem filtrar o tráfego de rede virtual para recursos específicos do Azure, sobre os pontos finais do serviço. Se não está familiarizado com as políticas de ponto final de serviço, consulte [as políticas de ponto final do serviço](virtual-network-service-endpoint-policies-overview.md) para saber mais.

 Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de ponto final de serviço
> * Criar uma definição de política de ponto final de serviço
> * Criar uma rede virtual com uma subnet
> * Associar uma política de ponto final de serviço a uma subnet

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto final de serviço

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. No painel de pesquisa, digite "política de ponto final de serviço" e selecione **a política de ponto final do serviço** e, em seguida, selecione **Criar**.

![Criar política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Insira, ou selecione, as seguintes informações no **Básico** 

   - Subscrição : Selecione a sua subscrição para a política
   - Grupo de recursos : **Selecione Criar novo** e insira o *myResourceGroup*
   - Nome : myEndpointPolicy
   - Localização : Centro dos EUA
 
   ![Criar fundamentos básicos de política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selecione **+ Adicione** em **Recursos** e introduza ou selecione as seguintes informações em Adicionar um painel de **recursos**

   - Serviço : Apenas **microsoft.Armazenamento** está disponível com políticas de endpoint de serviço
   - Âmbito : Selecione um fora de **Conta Única,** **Todas as contas por subscrição** e **todas as contas no grupo de recursos**
   - Subscrição : Selecione a sua subscrição para a conta de armazenamento. As contas de política e armazenamento podem estar em assinaturas diferentes.
   - Grupo de recursos : Selecione o seu grupo de recursos. Obrigatório, se o âmbito for definido como, "Todas as contas do grupo de recursos" ou "Conta Única".  
   - Recurso : Selecione o seu recurso de armazenamento Azure sob o grupo de subscrição ou recursos selecionado
   - Clique no botão **Adicionar** na parte inferior para terminar adicionando o recurso

   ![Definição de política de ponto final de serviço - recurso](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Adicione mais recursos repetindo os passos acima, conforme necessário

5. Opcional: Insira ou selecione, as seguintes informações nas **Etiquetas:**
   
   - Chave : Selecione a sua chave para a apólice. Ex: Dept     
   - Valor : Introduza o par de valor para a chave. Ex: Finanças

6. Selecione **Rever + Criar**. Valide a informação e Clique **em Criar**. Para fazer mais edições, clique **em Anterior**. 

   ![Criar validações finais de política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Ver políticas de ponto final 

1. Na caixa *de todos os serviços* no portal, comece a digitar *as políticas de ponto final do serviço.* Selecione **Políticas de ponto final de serviço**.
2. Em **Subscrições**, selecione o seu grupo de subscrição e recursos, como mostra a seguinte imagem

   ![Mostrar política](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecione a política e clique em **Definições** políticas para visualizar ou adicionar mais definições de política.

   ![Mostrar definições políticas](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecione **subredes associadas** para visualizar as subredes a política está associada. Se ainda não estiver associada nenhuma sub-rede, siga as instruções no próximo passo.

   ![Subredes associadas](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associar uma política a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acedidos a partir da subnet são adicionados à definição de política antes de associar a política à sub-rede dada. Uma vez associada a política, apenas o acesso aos recursos *listados permitir* será permitido em pontos finais de serviço. 
>
> Certifique-se também de que não existem serviços Azure geridos na subnet que está a ser associado à política de ponto final de serviço

- Antes de poder associar uma apólice a uma subrede, tem de criar uma rede virtual e uma sub-rede. Consulte o artigo [Criar uma Rede Virtual](./quick-create-portal.md) para obter ajuda com isto.

- Uma vez configurada a rede virtual e a sub-rede, é necessário configurar pontos finais do serviço de rede virtual para o Armazenamento Azure. Na lâmina da Rede Virtual, selecione **pontos finais de serviço**, e no painel seguinte selecione **Microsoft.Storage** e under **Subnets** selecione o VNet ou Subnet desejado

- Agora, pode optar por selecionar a Política de Ponto final de Serviço a partir da queda no painel acima se já tiver criado as políticas de Fim de Serviço antes de configurar o Ponto Final do Serviço para a Subnet, como mostrado abaixo

    ![Subnet associado ao criar ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OU se estiver a associar as políticas de ponto final do serviço depois de os pontos finais do serviço já estarem configurados, pode optar por associar a subnet a partir da lâmina de política de endpoint de serviço navegando para o painel de **subnets associado,** como mostrado abaixo

    ![Subnet associado via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>O acesso aos recursos de armazenamento Azure em todas as regiões será restringido de acordo com a Política de Ponto final de Serviço desta subrede.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma política de ponto final de serviço e associou-a a uma subnet. Para saber mais sobre as políticas de ponto final de serviço, consulte [as políticas de ponto final do serviço.](virtual-network-service-endpoint-policies-overview.md)
