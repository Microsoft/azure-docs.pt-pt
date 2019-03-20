---
title: Instalar o gateway de dados no local | Documentos da Microsoft
description: Saiba como instalar e configurar um gateway de dados no local.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bd4c024e14e70b5937d85e9917340d25f552096d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110873"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local

Um gateway de dados no local é necessário quando um ou mais servidores do Azure Analysis Services na mesma região que se ligam a origens de dados no local. Para saber mais sobre o gateway, veja [gateway de dados no local](analysis-services-gateway.md).

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos mínimos:**

* .NET 4.5 Framework
* versão de 64 bits do Windows 7 / Windows Server 2008 R2 (ou posterior)

**Recomendado:**

* CPU de 8 núcleos
* 8 GB de memória
* versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a configuração, ao registar o gateway com o Azure, é selecionada a região predefinida para a sua subscrição. Pode escolher uma região diferente. Se tiver servidores em mais do que uma região, tem de instalar um gateway para cada região. 
* O gateway não pode ser instalado num controlador de domínio.
* Apenas um gateway pode ser instalado num único computador.
* Instale o gateway num computador que permanece no e não entra em suspensão.
* Não instale o gateway num computador wirelessly ligado à sua rede. Pode ser reduzido o desempenho.
* Ao instalar o gateway, a conta de utilizador que tem sessão iniciada para o seu computador com necessita de registo no como privilégios de serviço. Quando a instalação estiver concluída, o serviço de gateway de dados no local utiliza a conta NT SERVICE\PBIEgwService para iniciar sessão como um serviço. Uma conta diferente pode ser especificada durante a configuração ou em serviços, após a conclusão da configuração. Definições de política de grupo Certifique-se de permitir que a conta que iniciou sessão com durante a instalação e a conta de serviço que escolher tem registo no como privilégios de serviço.
* Inicie sessão no Azure com uma conta no Azure AD para o mesmo [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) que a subscrição que está a registar o gateway. B2B do Azure contas (convidado) não são suportadas quando instalar e registar um gateway.
* Se a origens de dados estiverem numa rede Virtual do Azure (VNet), tem de configurar o [AlwaysUseGateway](analysis-services-vnet-gateway.md) propriedade do servidor.
* O gateway (unificado) descrito aqui não é suportado nas regiões do Azure Alemanha. Em alternativa, utilize **gateway no local dedicado para o Azure Analysis Services**, instalado a partir do seu servidor **início rápido** no portal. 


## <a name="download"></a>Transferir

 [Transferir o gateway](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalar

1. Execute a configuração.

2. Selecione uma localização, aceite os termos e, em seguida, clique em **instalar**.

   ![Instalar os termos de licença e de localização](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta tem de estar do Azure Active Directory seu inquilino. Esta conta é utilizada para o administrador do gateway. B2B do Azure contas (convidado) não são suportadas quando instalar e registar o gateway.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se iniciar sessão com uma conta de domínio, é mapeado para a sua conta organizacional no Azure AD. Sua conta institucional é utilizada como o administrador do gateway.

## <a name="register"></a>Registre-se

Para criar um recurso de gateway no Azure, tem de registar a instância local instalado com o serviço Cloud do Gateway. 

1.  Selecione **registar um novo gateway neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Escreva uma chave de recuperação e o nome para o seu gateway. Por predefinição, o gateway utiliza a região de predefinida da sua subscrição. Se tiver de selecionar uma região diferente, selecione **alterar região**.

    > [!IMPORTANT]
    > Guarde a sua chave de recuperação num local seguro. A chave de recuperação é necessária por ordem para a obtenção de controlo, migrar ou restaurar um gateway. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Criar um recurso do gateway do Azure

Depois de ter instalado e registado o gateway, terá de criar um recurso de gateway na sua subscrição do Azure. Inicie sessão no Azure com a mesma conta que utilizou quando registar o gateway.

1. No portal do Azure, clique em **criar um recurso** > **integração** > **gateway de dados no local**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Na **criar gateway de ligação**, introduzirem estas definições:

   * **Nome**: Introduza um nome para o seu recurso de gateway. 

   * **Subscrição**: Selecione a subscrição do Azure para associar o seu recurso de gateway. 
   
     A assinatura padrão baseia-se a conta do Azure que utilizou para iniciar sessão.

   * **Grupo de recursos**: Crie um grupo de recursos ou selecione um existente.

   * **Localização**: Selecione a região que registou o seu gateway no.

   * **Nome de instalação**: Se a instalação do gateway não estiver selecionada, selecione o gateway registado. 

     Quando tiver terminado, clique em **criar**.

## <a name="connect-servers"></a>Ligar servidores para o recurso de gateway

1. Na sua descrição de geral do servidor de Azure Analysis Services, clique em **Gateway de dados no local**.

   ![Ligar o servidor de gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Na **escolher um Gateway de dados no local para ligar**, selecione o recurso de gateway e, em seguida, clique em **gateway selecionado do Connect**.

   ![Ligar o servidor de recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, o servidor provavelmente não na mesma região que a região que especificou ao registar o gateway. 

Já está. Se precisar de abrir as portas ou fazer qualquer solução de problemas, certifique-se de que verifique [gateway de dados no local](analysis-services-gateway.md).

## <a name="next-steps"></a>Passos Seguintes

* [Gerir os serviços de análise](analysis-services-manage.md)   
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)   
* [Utilizar o gateway para origens de dados numa Rede Virtual do Azure](analysis-services-vnet-gateway.md)
