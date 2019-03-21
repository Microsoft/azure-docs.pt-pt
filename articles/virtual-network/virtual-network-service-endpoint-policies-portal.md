---
title: Criar e associar políticas de ponto final de serviço - portal do Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, saiba como configurar e políticas de ponto final de serviço associado com o portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: anithaa
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: aaa07759ed8b10578d024d5838ac1d2658778695
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999884"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Criar, alterar ou eliminar a política de ponto final de serviço com o portal do Azure

Políticas de ponto final de serviço permitem-lhe filtrar o tráfego de rede virtual para recursos específicos do Azure, através de pontos finais de serviço. Se não estiver familiarizado com as políticas de ponto final de serviço, consulte [descrição geral das políticas de ponto final de serviço](virtual-network-service-endpoint-policies-overview.md) para saber mais.

 Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de ponto final de serviço
> * Criar uma definição de política de ponto final de serviço
> * Criar uma rede virtual com uma sub-rede
> * Associe uma política de ponto final de serviço a uma sub-rede

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto final de serviço

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. No painel de pesquisa, escreva "política de ponto final de serviço" e selecione **(pré-visualização) de política de ponto final de serviço** e, em seguida, selecione **criar**.
3. Introduza ou selecione as seguintes informações na **Noções básicas** 

   - Subscrição: Selecione a sua subscrição para a política.    
   - Grupo de recursos: Selecione **Criar novo** e introduza *myResourceGroup*.     
   - Name           : myEndpointPolicy
   - Localização: EUA Centro-Oeste     
 
   ![Criar ponto final de serviço Noções básicas de política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Introduza ou selecione as seguintes informações na **definições de política**

   - Clique em **+ adicionar um recurso**, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e clique em **Add**.  
   - Âmbito: Selecione **única conta** ou **todas as contas na subscrição** ou **todas as contas do grupo de recursos**.    
   - Subscrição: Selecione a sua subscrição para a conta de armazenamento. Contas de armazenamento e de política podem ser em subscrições diferentes.   
   - Grupo de recursos: Selecione o grupo de recursos. Necessário se o âmbito será definido como "Todas as contas no grupo de recursos" ou "Conta única".  
   - Recurso: mystorageaccountportal    
   - Clique em **+ adicionar um recurso** para continuar a adicionar mais recursos.
   
   ![Criar definições de políticas de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Opcional: Introduza ou selecione as seguintes informações na **etiquetas**:
   
   - Chave: Selecione a sua chave para a política. Ex.: Departamento     
   - Valor: Introduza o par de valor da chave. Ex.: Finanças

6. Selecione **rever + criar**. Validar as informações e clique em **criar**. Para tornar ainda mais as edições, clique em **Previous**. 

   ![Criar ponto final de serviço validações de final de política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Ver políticas de ponto final 

1. Na *todos os serviços* caixa no portal, comece a escrever *políticas de ponto final de serviço*. Selecione **Policies(Preview) de ponto final de serviço**.
2. Sob **subscrições**, selecione a sua subscrição e grupo de recursos, conforme mostrado na imagem seguinte

   ![Mostrar a política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Selecione a política e clique em **definições de política** para ver ou adicionar mais definições de política.

   ![Mostrar definições de política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Selecione **associado à sub-redes** para ver as sub-redes está associada a política. Para associar a política a uma sub-rede, clique em "Navegar para a rede virtual na mesma região".

   ![Mostrar sub-redes associadas](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Associe uma política a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acedidos a partir da sub-rede, para o serviço selecionado, são adicionados à política antes de o associar a política. Assim que a política estiver associada, acessar apenas para os recursos listados na política vai ser permitida, para regiões de ponto final para o serviço. 

Antes de poder associar uma política a uma sub-rede, tem de criar uma rede virtual e uma sub-rede, em seguida, pode associar a política para a sub-rede:

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Em **Criar rede virtual**, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **Criar**:
   - Name           : myVirtualNetwork      
   - Espaço de endereços: 10.0.0.0/16      
   - Subscrição: Selecione a sua subscrição. Política deve ser na mesma subscrição que a VNet     
   - Grupo de recursos: Selecione **utilizar existente** e, em seguida, selecione *myResourceGroup*     
   - Localização: EUA Centro-Oeste     
   - Nome da sub-rede: privada     
   - Intervalo de endereços: 10.0.0.0/24
     
4. Na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal, comece a escrever *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
5. Sob **configurações**, selecione **sub-redes** e, em seguida, selecione **privada**.
6. Conforme mostrado na imagem seguinte, selecione **pontos finais de serviço**, selecione **Microsoft. Storage**, selecione **políticas de ponto final de serviço**, selecione  **myEndpointPolicy**e, em seguida, selecione **guardar**:

   ![Associar política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Acesso aos recursos de serviço noutras regiões terão permissão desta sub-rede, com base em grupos de segurança de rede (NSGs). Para restringir o acesso a apenas as regiões de ponto final, o limite NSGs para tráfego de serviço em regiões de ponto final. Para obter mais informações sobre como criar NSGs com etiquetas de serviço por região, consulte [etiquetas de serviço do Azure de NSG.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

No exemplo abaixo, o NSG está restringido para acessar apenas armazenamento do Azure recursos nos EUA e WestUS2, com uma regra de "Negar todos os" como uma regra de prioridade mais baixa.

![Negar todos os NSG](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou uma política de ponto final de serviço e associou-o a uma sub-rede. Para saber mais sobre as políticas de ponto final de serviço, veja [descrição geral das políticas de ponto final de serviço.](virtual-network-service-endpoint-policies-overview.md)

