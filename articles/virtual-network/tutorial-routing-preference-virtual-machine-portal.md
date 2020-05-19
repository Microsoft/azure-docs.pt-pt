---
title: Configure a preferência de encaminhamento para um portal VM - Azure
description: Aprenda a criar um VM com um endereço IP público com escolha de preferência de encaminhamento utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 2a501280b817db691fb3b93097669d473443a607
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597990"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Configure a preferência de encaminhamento para um VM utilizando o portal Azure

Este artigo mostra-lhe como configurar a preferência de encaminhamento para uma máquina virtual. O tráfego ligado à Internet a partir do VM será encaminhado através da rede ISP quando escolher a **Internet** como a sua opção de preferência de encaminhamento . O encaminhamento padrão é através da rede global da Microsoft.

Este artigo mostra-lhe como criar uma máquina virtual com um IP público que está definido para direcionar o tráfego através da internet pública através do portal Azure.

> [!IMPORTANT]
> A preferência por encaminhamento está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade para a sua subscrição
A função De Preferência de Encaminhamento está atualmente em pré-visualização. Deve registar a funcionalidade para a sua subscrição utilizando o Azure PowerShell da seguinte forma:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://preview.portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. Selecione **Compute**, e, em seguida, selecione **Windows Server 2016 VM**, ou outro sistema operativo à sua escolha.
3. Introduza, ou selecione, as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK:**

    |Definição|Valor|
    |---|---|
    |Nome|myVM|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. No separador **Networking,** clique em **Criar novo** para **endereço IP público**.
6. Introduza *o myPublicIpAddress*, selecione sku como **Standard**, e, em seguida, selecione a preferência de encaminhamento **internet** e, em seguida, acerte **ok**, como mostrado na seguinte imagem:

   ![Selecione estática](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Selecione uma porta ou nenhuma porta sob **portas de entrada pública Selecione**. O Portal 3389 é selecionado, para permitir o acesso remoto à máquina virtual do Windows Server a partir da internet. A abertura da porta 3389 da internet não é recomendada para cargas horárias de produção.

   ![Selecione uma porta](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Aceite as definições predefinidas restantes e selecione **OK**.
8. Na página **Resumo**, selecione **Criar**. A máquina virtual leva alguns minutos para ser implantada.
9. Uma vez que a máquina virtual é implantada, introduza o *myPublicIpAddress* na caixa de pesquisa na parte superior do portal. Quando o **myPublicIpAddress** aparecer nos resultados da pesquisa, selecione-o.
10. Pode ver o endereço IP público que está atribuído e que o endereço é atribuído à máquina virtual **myVM,** como mostra a seguinte imagem:

    ![Ver endereço IP público](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Selecione **Networking,** em seguida, clique em nic **mynic** e, em seguida, selecione o endereço ip público para confirmar que a preferência de encaminhamento é atribuída como **Internet**.
    ![Ver endereço IP público](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [IP público com preferência de encaminhamento](routing-preference-overview.md).
- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as definições de [endereçoip públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
