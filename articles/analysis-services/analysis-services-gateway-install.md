---
title: Instalar porta de dados no local para serviços de análise do Azure Microsoft Docs
description: Saiba como instalar e configurar uma porta de dados no local para ligar a fontes de dados no local a partir de um servidor de Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1d090070dd7b2afe5ea1ece9b5da8b8b5b7b0780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87438972"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local

É necessária uma porta de dados no local quando um ou mais servidores da Azure Analysis Services na mesma região se ligam a fontes de dados no local.  Embora o gateway que instala seja o mesmo que o utilizado por outros serviços como Power BI, Power Apps e Logic Apps, ao instalar para serviços de análise Azure, existem alguns passos adicionais que precisa de completar. Este artigo de instalação é específico dos **Serviços de Análise Azure.** 

Para saber mais sobre como funciona o Azure Analysis Services com o gateway, consulte [Conectar-se a fontes de dados no local.](analysis-services-gateway.md) Para saber mais sobre cenários avançados de instalação e a porta de entrada em geral, consulte a documentação de [gateways de dados no local.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos mínimos:**

* 4,5 de .NET framework
* Versão de 64 bits do Windows 8 / Windows Server 2012 R2 (ou posterior)

**Recomendado:**

* CPU de 8 Núcleos
* 8 GB de memória
* Versão de 64 bits do Windows 8 / Windows Server 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a configuração, ao registar o seu gateway com o Azure, é selecionada a região padrão para a sua subscrição. Pode escolher uma subscrição e uma região diferentes. Se tiver servidores em mais de uma região, deve instalar uma porta de entrada para cada região. 
* O gateway não pode ser instalado num controlador de domínio.
* Apenas um portal pode ser instalado num único computador.
* Instale o gateway num computador que permaneça ligado e não adormece.
* Não instale o gateway num computador com uma ligação sem fios apenas à sua rede. O desempenho pode ser diminuído.
* Ao instalar o gateway, a conta de utilizador com a qual está inscrito no seu computador deve ter iniciar sessão como privilégios de serviço. Quando a instalação estiver concluída, o serviço de gateway de dados no local utiliza a conta NT SERVICE\PBIEgwService para iniciar sessão como um serviço. Uma conta diferente pode ser especificada durante a configuração ou em Serviços após a configuração estar concluída. Certifique-se de que as definições de Política de Grupo permitem tanto a conta com que está a iniciar a sua instalação como a conta de serviço que escolhe ter registo como privilégios de serviço.
* Inscreva-se no Azure com uma conta em Azure AD para o mesmo [inquilino](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a subscrição em que está a registar o portal. As contas Azure B2B (hóspede) não são suportadas na instalação e registo de um gateway.
* Se as fontes de dados estiverem numa Rede Virtual Azure (VNet), tem de configurar a propriedade do servidor [AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a>Download

 [Descarregue o portal](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalar

1. Executar a configuração.

2. Selecione **Gateway de dados no local**.

   ![Selecione](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione uma localização, aceite os termos e, em seguida, clique em **Instalar**.

   ![Instalar termos de localização e licença](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta deve estar no Diretório Ativo Azure do seu inquilino. Esta conta é utilizada para o administrador do gateway. As contas Azure B2B (hóspede) não são suportadas na instalação e registo do gateway.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se iniciar sedutar com uma conta de domínio, está mapeado na sua conta organizacional em Azure AD. A sua conta organizacional é utilizada como administrador de gateway.

## <a name="register"></a>Registar

Para criar um recurso de gateway em Azure, deve registar a instância local que instalou com o Gateway Cloud Service. 

1.  **Selecione Registar uma nova porta de entrada neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e chave de recuperação para o seu gateway. Por predefinição, o gateway utiliza a região padrão da sua subscrição. Se precisar de selecionar uma região diferente, selecione **Change Region**.

    > [!IMPORTANT]
    > Guarde a chave de recuperação num lugar seguro. A chave de recuperação é necessária para poder assumir, migrar ou restaurar uma porta de entrada. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Criar um recurso de gateway Azure

Depois de ter instalado e registado o seu portal, precisa de criar um recurso de gateway em Azure. Inscreva-se no Azure com a mesma conta que usou ao registar o gateway.

1. No portal Azure, clique em **Criar um recurso,** em seguida, procure **por gateway de dados no local**e, em seguida, clique em **Criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Create connection gateway,** insira estas definições:

   * **Nome**: Introduza um nome para o seu recurso gateway. 

   * **Subscrição**: Selecione a subscrição Azure para associar-se ao seu recurso gateway. 
   
     A subscrição por defeito baseia-se na conta Azure que usou para iniciar scontabilidade.

   * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.

   * **Localização**: Selecione a região onde registou o seu portal.

   * **Nome de instalação**: Se a instalação do gateway ainda não estiver selecionada, selecione o portal que instalou no seu computador e está registado. 

     Quando terminar, clique em **Criar**.

## <a name="connect-gateway-resource-to-server"></a>Conecte o recurso gateway ao servidor

> [!NOTE]
> A ligação a um recurso de gateway numa subscrição diferente do seu servidor não é suportada no portal, mas é suportada através do PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na visão geral do servidor Azure Analysis Services, clique **em Gateway de dados no Local.**

   ![Ligue o servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Pick a On-Premises Data Gateway para ligar,** selecione o seu recurso gateway e, em seguida, clique em **Ligar o gateway selecionado**.

   ![Ligue o servidor ao recurso gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o seu gateway não aparecer na lista, é provável que o seu servidor não esteja na mesma região que a região especificada ao registar o gateway.

    Quando a ligação entre o seu servidor e o recurso gateway for bem sucedida, o estado mostrará **O Connected**.


    ![Conecte o servidor ao sucesso do recurso gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize [o Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) para obter o gateway ResourceID. Em seguida, ligue o recurso gateway a um servidor existente ou novo especificando **-GatewayResourceID** em [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) ou [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Para obter o ID do recurso gateway:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Para configurar um servidor existente:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Já está! Se precisar de abrir portas ou fazer qualquer resolução de problemas, [certifique-se de verificar o portal de dados no local.](analysis-services-gateway.md)

## <a name="next-steps"></a>Passos seguintes

* [Gerir o Analysis Services](analysis-services-manage.md)   
* [Obter dados a partir do Azure Analysis Services](analysis-services-connect.md)   
* [Utilizar o gateway para origens de dados numa Rede Virtual do Azure](analysis-services-vnet-gateway.md)
