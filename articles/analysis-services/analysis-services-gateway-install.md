---
title: Instalar a porta de dados no local para os Serviços de Análise do Azure [ Microsoft Docs
description: Aprenda a instalar e configurar uma porta de dados no local para se ligar a fontes de dados no local a partir de um servidor dos Serviços de Análise do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062154"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local

É necessária uma porta de dados no local quando um ou mais servidores dos Serviços de Análise Azure na mesma região se ligam a fontes de dados no local.  Embora o portal que instale seja o mesmo utilizado por outros serviços como Power BI, Power Apps e Logic Apps, quando instalar para serviços de análise Azure, existem alguns passos adicionais que precisa de ser concluído. Este artigo de instalação é específico dos Serviços de **Análise Azure.** 

Para saber mais sobre como os Serviços de Análise Azure funcionam com o portal, consulte [a Connecting com fontes de dados no local.](analysis-services-gateway.md) Para saber mais sobre cenários avançados de instalação e a porta de entrada em geral, consulte a documentação de [gateways de dados no local.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos Mínimos:**

* 4,5 de .NET framework
* Versão de 64 bits do Windows 8 / Windows Server 2012 R2 (ou posterior)

**Recomendação:**

* CPU de 8 Núcleos
* 8 GB de memória
* Versão de 64 bits do Windows 8 / Windows Server 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a configuração, ao registar a sua porta de entrada com o Azure, a região padrão para a sua subscrição é selecionada. Pode escolher uma subscrição e região diferentes. Se tiver servidores em mais de uma região, tem de instalar uma porta de entrada para cada região. 
* O portal não pode ser instalado num controlador de domínio.
* Apenas um portal pode ser instalado num único computador.
* Instale o portal num computador que permaneça ligado e não durma.
* Não instale o portal num computador com uma ligação sem fios apenas à sua rede. O desempenho pode ser diminuído.
* Ao instalar o portal, a conta de utilizador em que está inscrito no seu computador deve ter o Log on como privilégios de serviço. Quando a instalação estiver concluída, o serviço de gateway de dados No local utiliza a conta NT SERVICE\PBIEgwService para iniciar sessão como serviço. Uma conta diferente pode ser especificada durante a configuração ou nos Serviços após a configuração estar concluída. Certifique-se de que as definições de Política de Grupo permitem tanto a conta em que assinou quando está a instalar e a conta de serviço que escolhe ter o Log on como privilégios de serviço.
* Inscreva-se no Azure com uma conta em Azure AD para o mesmo [inquilino](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a subscrição em que está a registar o portal. As contas Azure B2B (hóspede) não são suportadas na instalação e registo de um portal.
* Se as fontes de dados estiverem numa Rede Virtual Azure (VNet), deve configurar a propriedade do servidor [AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a><a name="download"></a>Baixar

 [Descarregue o portal](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Instalar

1. Executar configuração.

2. Selecione **gateway de dados no local**.

   ![Selecione](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione uma localização, aceite os termos e, em seguida, clique em **Instalar**.

   ![Instalar termos de localização e licença](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta deve estar no Diretório Ativo Azure do seu inquilino. Esta conta é utilizada para o administrador de gateway. As contas Azure B2B (hóspede) não são suportadas na instalação e registo do portal.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você assinar com uma conta de domínio, está mapeada para a sua conta organizacional em Azure AD. A sua conta organizacional é usada como administradora de gateway.

## <a name="register"></a><a name="register"></a>Registar

Para criar um recurso gateway em Azure, deve registar a instância local que instalou no Gateway Cloud Service. 

1.  Selecione **Registar uma nova porta de entrada neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e chave de recuperação para o seu portal. Por predefinição, o gateway utiliza a região padrão da sua subscrição. Se precisar selecionar uma região diferente, selecione **Change Region**.

    > [!IMPORTANT]
    > Guarde a chave de recuperação num lugar seguro. A chave de recuperação é necessária para tomar posse, migrar ou restaurar uma porta de entrada. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Criar um recurso de gateway Azure

Depois de ter instalado e registado o seu portal, precisa de criar um recurso de gateway em Azure. Inscreva-se no Azure com a mesma conta que utilizou ao registar o portal.

1. No portal Azure, clique em **Criar um recurso,** depois procure o **gateway de dados no local,** e depois clique em **Criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Na Criação de gateway de **ligação,** introduza estas definições:

   * **Nome**: Introduza um nome para o seu recurso gateway. 

   * **Subscrição**: Selecione a subscrição Azure para associar com o seu recurso gateway. 
   
     A subscrição predefinida baseia-se na conta Azure que usou para iniciar sessão.

   * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.

   * **Localização**: Selecione a região onde registou o seu portal.

   * Nome de **instalação**: Se a instalação do gateway ainda não estiver selecionada, selecione o portal instalado no computador e registado. 

     Quando terminar, clique em **Criar**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Ligar servidores ao recurso gateway

1. Na sua visão geral do servidor dos **Serviços**de Análise Azure, clique no Gateway de Dados no Local .

   ![Ligar servidor a gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Pick a On-Premises Data Gateway para ligar,** selecione o seu recurso de gateway e, em seguida, clique em Ligar gateway **selecionado**.

   ![Ligar servidor ao recurso gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o seu portal não aparecer na lista, o seu servidor provavelmente não se encontra na mesma região que especificou ao registar o gateway.

    Quando a ligação entre o servidor e o recurso gateway for bem sucedida, o estado mostrará **Connected**.


    ![Ligar servidor ao sucesso de recursos gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Já está. Se precisar de abrir portas ou fazer qualquer resolução de [problemas, certifique-se de](analysis-services-gateway.md)verificar a porta de dados no local .

## <a name="next-steps"></a>Passos seguintes

* [Gerir o Analysis Services](analysis-services-manage.md)   
* [Obtenha dados dos Serviços de Análise Azure](analysis-services-connect.md)   
* [Utilizar o gateway para origens de dados numa Rede Virtual do Azure](analysis-services-vnet-gateway.md)
