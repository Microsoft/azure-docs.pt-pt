---
title: Instalar o gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Saiba como instalar e configurar um gateway de dados local para se conectar a fontes de dados locais de um servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f578840726543027a8c1b1db9bd88ea42f6e85fa
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264072"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local

Um gateway de dados local é necessário quando um ou mais servidores de Azure Analysis Services na mesma região se conectam a fontes de dados locais.  Embora o gateway que você instalar seja o mesmo usado por outros serviços como Power BI, Power apps e aplicativos lógicos, ao instalar o para Azure Analysis Services, há algumas etapas adicionais que você precisa concluir. Este artigo de instalação é específico para **Azure Analysis Services**. 

Para saber mais sobre como Azure Analysis Services funciona com o gateway, consulte [conectando-se a fontes de dados locais](analysis-services-gateway.md). Para saber mais sobre os cenários de instalação avançada e o gateway em geral, consulte [documentação de gateways de dados locais](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos Mínimos:**

* .NET 4.5 Framework
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Recomendado:**

* CPU de 8 Núcleos
* 8 GB de memória
* versão de 64 bits do Windows 8/Windows Server 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a instalação, ao registrar seu gateway com o Azure, a região padrão para sua assinatura é selecionada. Você pode escolher uma assinatura e uma região diferentes. Se você tiver servidores em mais de uma região, deverá instalar um gateway para cada região. 
* O gateway não pode ser instalado em um controlador de domínio.
* Somente um gateway pode ser instalado em um único computador.
* Instale o gateway em um computador que permaneça ligado e não vá para o estado de suspensão.
* Não instale o gateway em um computador com uma conexão somente sem fio com a rede. O desempenho pode ser reduzido.
* Ao instalar o gateway, a conta de usuário que você está conectado ao seu computador com deve ter privilégios de logon como serviço. Quando a instalação é concluída, o serviço do gateway de dados local usa a conta do NT SERVICE\PBIEgwService para fazer logon como um serviço. Uma conta diferente pode ser especificada durante a instalação ou em serviços após a conclusão da instalação. Verifique se as configurações de Política de Grupo permitem que a conta com a qual você está conectado ao instalar o e a conta de serviço que você escolher tenham logon como privilégios de serviço.
* Entre no Azure com uma conta no Azure AD para o mesmo [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a assinatura na qual você está registrando o gateway. Não há suporte para contas do Azure B2B (convidado) ao instalar e registrar um gateway.
* Se as fontes de dados estiverem em uma VNet (rede virtual) do Azure, você deverá configurar a propriedade do servidor [AlwaysUseGateway](analysis-services-vnet-gateway.md) .
* O gateway (unificado) descrito aqui não tem suporte nas regiões do Azure Alemanha. Em vez disso, use o **Gateway local dedicado para Azure Analysis Services**, instalado do **início rápido** do seu servidor no Portal. 


## <a name="download"></a>Baixar

 [Baixar o gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Pré-instalação

1. Execute a instalação.

2. Selecione **Gateway de dados local**.

   ![Selecione](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione um local, aceite os termos e clique em **instalar**.

   ![Local de instalação e termos de licença](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta deve estar na Azure Active Directory do seu locatário. Essa conta é usada para o administrador do gateway. Não há suporte para contas do Azure B2B (convidado) ao instalar e registrar o gateway.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você entrar com uma conta de domínio, ela será mapeada para sua conta institucional no Azure AD. Sua conta institucional é usada como o administrador do gateway.

## <a name="register"></a>Registr

Para criar um recurso de gateway no Azure, você deve registrar a instância local instalada com o serviço de nuvem do gateway. 

1.  Selecione **registrar um novo gateway neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e uma chave de recuperação para seu gateway. Por padrão, o gateway usa a região padrão da sua assinatura. Se você precisar selecionar uma região diferente, selecione **alterar região**.

    > [!IMPORTANT]
    > Salve sua chave de recuperação em um local seguro. A chave de recuperação é necessária para tomada, migrar ou restaurar um gateway. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Criar um recurso de gateway do Azure

Depois de instalar e registrar seu gateway, você precisará criar um recurso de gateway no Azure. Entre no Azure com a mesma conta que você usou ao registrar o gateway.

1. Em portal do Azure, clique em **criar um recurso**, procure por **Gateway de dados local**e, em seguida, clique em **criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Em **criar gateway de conexão**, insira estas configurações:

   * **Nome**: Insira um nome para o recurso de gateway. 

   * **Assinatura**: selecione a assinatura do Azure a ser associada ao recurso de gateway. 
   
     A assinatura padrão é baseada na conta do Azure que você usou para entrar.

   * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.

   * **Local**: selecione a região em que você registrou o gateway.

   * **Nome da instalação**: se a instalação do gateway ainda não estiver selecionada, selecione o gateway que você instalou no computador e registrado. 

     Quando tiver terminado, clique em **criar**.

## <a name="connect-servers"></a>Conectar servidores ao recurso de gateway

1. Na visão geral do Azure Analysis Services Server, clique em **Gateway de dados local**.

   ![Conectar servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Selecione um gateway de dados local para se conectar**, selecione o recurso de gateway e clique em **conectar gateway selecionado**.

   ![Conectar servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, provavelmente o servidor não está na mesma região que a região especificada ao registrar o gateway.

    Quando a conexão entre o seu servidor e o recurso de gateway for bem-sucedida, o status mostrará **conectado**.


    ![Êxito ao conectar o servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

E já está. Se você precisar abrir portas ou executar qualquer solução de problemas, não deixe de conferir o [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Passos seguintes

* [Gerir o Analysis Services](analysis-services-manage.md)   
* [Obter dados de Azure Analysis Services](analysis-services-connect.md)   
* [Utilizar o gateway para origens de dados numa Rede Virtual do Azure](analysis-services-vnet-gateway.md)
