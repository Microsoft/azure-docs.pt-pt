---
title: Instalar o gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Saiba como instalar e configurar um gateway de dados local para se conectar a fontes de dados locais de um servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062154"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local

Um gateway de dados local é necessário quando um ou mais servidores de Azure Analysis Services na mesma região se conectam a fontes de dados locais.  Embora o gateway que você instalar seja o mesmo usado por outros serviços como Power BI, Power apps e aplicativos lógicos, ao instalar o para Azure Analysis Services, há algumas etapas adicionais que você precisa concluir. Este artigo de instalação é específico dos Serviços de **Análise Azure.** 

Para saber mais sobre como os Serviços de Análise Azure funcionam com o portal, consulte [a Connecting com fontes de dados no local.](analysis-services-gateway.md) Para saber mais sobre cenários avançados de instalação e a porta de entrada em geral, consulte a documentação de [gateways de dados no local.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos mínimos:**

* .net 4.5 Quadro
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Recomendado:**

* 8 CPU central
* 8 GB Memória
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a instalação, ao registrar seu gateway com o Azure, a região padrão para sua assinatura é selecionada. Você pode escolher uma assinatura e uma região diferentes. Se você tiver servidores em mais de uma região, deverá instalar um gateway para cada região. 
* O gateway não pode ser instalado em um controlador de domínio.
* Somente um gateway pode ser instalado em um único computador.
* Instale o gateway em um computador que permaneça ligado e não vá para o estado de suspensão.
* Não instale o gateway em um computador com uma conexão somente sem fio com a rede. O desempenho pode ser reduzido.
* Ao instalar o gateway, a conta de usuário que você está conectado ao seu computador com deve ter privilégios de logon como serviço. Quando a instalação é concluída, o serviço do gateway de dados local usa a conta do NT SERVICE\PBIEgwService para fazer logon como um serviço. Uma conta diferente pode ser especificada durante a instalação ou em serviços após a conclusão da instalação. Verifique se as configurações de Política de Grupo permitem que a conta com a qual você está conectado ao instalar o e a conta de serviço que você escolher tenham logon como privilégios de serviço.
* Inscreva-se no Azure com uma conta em Azure AD para o mesmo [inquilino](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a subscrição em que está a registar o portal. Não há suporte para contas do Azure B2B (convidado) ao instalar e registrar um gateway.
* Se as fontes de dados estiverem numa Rede Virtual Azure (VNet), deve configurar a propriedade do servidor [AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a>Baixar

 [Descarregue o portal](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalar

1. Execute a instalação.

2. Selecione **gateway de dados no local**.

   ![Selecione](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione uma localização, aceite os termos e, em seguida, clique em **Instalar**.

   ![Local de instalação e termos de licença](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta deve estar na Azure Active Directory do seu locatário. Essa conta é usada para o administrador do gateway. Não há suporte para contas do Azure B2B (convidado) ao instalar e registrar o gateway.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você entrar com uma conta de domínio, ela será mapeada para sua conta institucional no Azure AD. Sua conta institucional é usada como o administrador do gateway.

## <a name="register"></a>Registo

Para criar um recurso de gateway no Azure, você deve registrar a instância local instalada com o serviço de nuvem do gateway. 

1.  Selecione **Registar uma nova porta de entrada neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e uma chave de recuperação para seu gateway. Por padrão, o gateway usa a região padrão da sua assinatura. Se precisar selecionar uma região diferente, selecione **Change Region**.

    > [!IMPORTANT]
    > Salve sua chave de recuperação em um local seguro. A chave de recuperação é necessária para tomada, migrar ou restaurar um gateway. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Criar um recurso de gateway Azure

Depois de instalar e registrar seu gateway, você precisará criar um recurso de gateway no Azure. Entre no Azure com a mesma conta que você usou ao registrar o gateway.

1. No portal Azure, clique em **Criar um recurso,** depois procure o **gateway de dados no local,** e depois clique em **Criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Na Criação de gateway de **ligação,** introduza estas definições:

   * **Nome**: Introduza um nome para o seu recurso gateway. 

   * **Subscrição**: Selecione a subscrição Azure para associar com o seu recurso gateway. 
   
     A assinatura padrão é baseada na conta do Azure que você usou para entrar.

   * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.

   * **Localização**: Selecione a região onde registou o seu portal.

   * Nome de **instalação**: Se a instalação do gateway ainda não estiver selecionada, selecione o portal instalado no computador e registado. 

     Quando terminar, clique em **Criar**.

## <a name="connect-servers"></a>Ligar servidores ao recurso gateway

1. Na sua visão geral do servidor dos **Serviços**de Análise Azure, clique no Gateway de Dados no Local .

   ![Conectar servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Pick a On-Premises Data Gateway para ligar,** selecione o seu recurso de gateway e, em seguida, clique em Ligar gateway **selecionado**.

   ![Conectar servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, provavelmente o servidor não está na mesma região que a região especificada ao registrar o gateway.

    Quando a ligação entre o servidor e o recurso gateway for bem sucedida, o estado mostrará **Connected**.


    ![Êxito ao conectar o servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Já está. Se precisar de abrir portas ou fazer qualquer resolução de [problemas, certifique-se de](analysis-services-gateway.md)verificar a porta de dados no local .

## <a name="next-steps"></a>Passos seguintes

* [Gerir o Analysis Services](analysis-services-manage.md)   
* [Obtenha dados dos Serviços de Análise Azure](analysis-services-connect.md)   
* [Utilizar o gateway para origens de dados numa Rede Virtual do Azure](analysis-services-vnet-gateway.md)
