---
title: Configurar a conectividade de cloud híbrida com o Azure e o Azure Stack | Documentos da Microsoft
description: Saiba como configurar a conectividade de cloud híbrida com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a05021255c6226329f1d7a3f0e7fa8c9be756646
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546726"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: configurar a conectividade de cloud híbrida com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode aceder a recursos com segurança no global Azure e usando o padrão de conectividade híbrida do Azure Stack.

Neste tutorial, irá criar um ambiente de exemplo para:

> [!div class="checklist"]
> - Manter os dados no local para cumprir os requisitos de regulamentação ou de privacidade, mas tem acesso aos recursos do Azure global.
> - Manter um sistema legado, ao utilizar a implementação de aplicações de dimensionamento na cloud e de recursos no global Azure.

> [!Tip]  
> ![pillars.png híbrida](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack é uma extensão do Azure. O Azure Stack coloca a agilidade e inovação da cloud de informática para o seu ambiente no local e ativar a única cloud híbrida que permite-lhe criar e implementar aplicações de híbridas em qualquer lugar.  
> 
> O White Paper [considerações de Design para aplicações híbridas](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares de qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para criação, implantação e operação do híbrida aplicações. As considerações de design ajudam a otimizar o design de aplicações híbridas, minimizando desafios em ambientes de produção.


## <a name="prerequisites"></a>Pré-requisitos

Alguns componentes necessários para criar uma implementação de conectividade híbrida. Alguns desses componentes levará tempo para preparar, portanto, terá de planear em conformidade.

**Azure Stack**

Um parceiro OEM/Hardware do Azure podem implementar uma produção do Azure Stack e todos os utilizadores podem implementar um Azure Stack Development Kit (ASDK).

**Componentes de pilha do Azure**

Um operador de pilha do Azure tem de implementar o serviço de aplicações, criar planos e ofertas, criar uma subscrição de inquilino e adicionar a imagem do Windows Server 2016. Se já tiver alguns desses componentes, certifique-se de que cumpram os requisitos antes de começar este tutorial.

Este tutorial parte do princípio de que tenha algum conhecimento básico do Azure e o Azure Stack. Para saber mais antes de iniciar o tutorial, leia os artigos seguintes:

 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceitos-chave do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se não tiver uma subscrição do Azure, crie uma  [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  antes de começar.
 - Criar uma [aplicação Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) no Azure. Tome nota do URL da aplicação Web, porque ele será necessário no tutorial.

### <a name="azure-stack"></a>Azure Stack

 - Utilizar a Azure Stack de produção ou implementar o Development Kit do Azure Stack de https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Implementar o ASDK pode demorar até 7 horas, por isso, planeie em conformidade.

 - Implementar [serviço de aplicações](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS para o Azure Stack.
 - [Criar planos e ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) no ambiente do Azure Stack.
 - [Criar subscrição de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente do Azure Stack.

### <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpre os seguintes critérios antes de começar a configurar a conectividade de cloud híbrida:

 - Precisa de um endereço IPv4 público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
 - Todos os recursos são implementados na mesma região/localização.

#### <a name="tutorial-example-values"></a>Valores de exemplo tutorial

Os exemplos neste tutorial utilizam os seguintes valores. Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para uma melhor compreensão dos exemplos. Para obter mais informações sobre o Gateway de VPN definições em geral, consulte [sobre o VPN Gateway Settings](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificações de ligação:

 - **Tipo de VPN**: Baseado na rota
 - **Tipo de ligação**: Site a site (IPsec)
 - **Tipo de gateway**: VPN
 - **Nome da ligação do Azure**: Azure-Gateway-AzureStack-S2SGateway (o portal será preenchido automaticamente este valor)
 - **Nome da ligação do Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (o portal será preenchido automaticamente este valor)
 - **Chave partilhada**: Qualquer compatível com o hardware VPN, com valores em ambos os lados da ligação correspondentes
 - **Subscrição**: Qualquer subscrição preferencial
 - **Grupo de recursos**: Teste-infra-estrutura

Endereços IP de rede e sub-rede:

| Ligação do Azure/Azure Stack | Name | Sub-rede | Endereço IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Gateway de rede Virtual do Azure | Azure-Gateway |  |  |
| Gateway de rede Virtual do Azure Stack | AzureStack-Gateway |  |  |
| IP público do Azure | Azure-GatewayPublicIP |  | Determinar durante a criação |
| IP público do Azure Stack | AzureStack-GatewayPublicIP |  | Determinar durante a criação |
| Gateway de rede Local do Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valor IP público do Azure Stack |
| Gateway de rede Local do Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Valor IP público do Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Criar uma rede virtual no Azure global e o Azure Stack

Utilize os seguintes passos para criar uma rede virtual com o portal. Pode usar esses [valores de exemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se estiver a utilizar este artigo como um tutorial. No entanto, se estiver a utilizar este artigo para configurar um ambiente de produção, substitua as definições de exemplo pelos seus próprios valores.

> [!IMPORTANT]
> Tem de se certificar de que não existir uma sobreposição de endereços IP no Azure ou no Azure Stack espaços de endereços da vNet.

Para criar uma vNet no Azure:

1. Utilizar o browser para ligar para o [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.
2. Selecione **criar um recurso**. Na **pesquisar no marketplace** , insira `virtual network`'. Encontrar **rede Virtual** na lista de resultados e, em seguida, selecione **rede Virtual**.
3. Partir do **selecionar um modelo de implementação** , selecione **Resource Manager**e, em seguida, selecione **criar**.
4. No **criar rede virtual**, configurar as definições da VNet. Os nomes de campos obrigatórios são prefixados com um asterisco vermelho.  Ao introduzir um valor válido, o asterisco muda para uma marca de verificação verde.

Para criar uma vNet no Azure Stack:

* Repita os passos anteriores (1 a 4) a utilizar o Azure Stack **portal de inquilinos**.

## <a name="add-a-gateway-subnet"></a>Adicionar uma sub-rede do gateway

Antes de ligar a rede virtual a um gateway, terá de criar a sub-rede do gateway para a rede virtual que pretende ligar. Os serviços do gateway utilizam os endereços IP que especificar na sub-rede de gateway.

Na [portal do Azure](https://portal.azure.com/), navegue para o Gestor de recursos de rede virtual onde pretende criar um gateway de rede virtual.

1. Selecione a vNet para abrir o **rede Virtual** página.
2. Na **configurações**, selecione **sub-redes**.
3. Sobre o **sub-redes** página, selecione **+ sub-rede de Gateway** para abrir o **adicionar sub-rede** página.

    ![Adicionar sub-rede do gateway](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. O **Name** para a sub-rede é preenchida automaticamente com o valor "GatewaySubnet". Este valor é necessário para o Azure reconheça a sub-rede como a sub-rede do gateway.
5. Alteração da **intervalo de endereços** valores que são fornecidos para corresponder aos seus requisitos de configuração e, em seguida, selecione **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Criar um Gateway de rede Virtual no Azure e o Azure Stack

Utilize os seguintes passos para criar um gateway de rede virtual no Azure.

1. No lado esquerdo da página do portal, selecione **+**  e introduza "gateway de rede virtual" no campo de pesquisa.
2. Na **resultados**, selecione **gateway de rede Virtual**.
3. Na **gateway de rede Virtual**, selecione **Create** para abrir o **criar gateway de rede virtual** página.
4. No **criar gateway de rede virtual**, especifique os valores para o seu gateway de rede, conforme mostrado na **valores de exemplo Tutorial**e os seguintes valores adicionais:

    - **SKU**: básico
    - **Rede virtual**: Selecione a rede Virtual que criou anteriormente. A sub-rede do gateway que criou é selecionada automaticamente.
    - **Configuração do primeiro IP**:  Este é o IP público do seu Gateway.
        - Selecione **criar configuração de IP do gateway**, que leva-o para o **escolher endereço IP público** página.
        - Selecione **+ criar novo** para abrir o **Criar endereço IP público** página.
        - Introduza um **Name** para seu endereço IP público. Deixe o SKU como **básica**e, em seguida, selecione **OK** para guardar as alterações.

       > [!Note]
       > Atualmente, o Gateway de VPN só suporta alocação de endereço IP público dinâmico. No entanto, isso não significa que o endereço IP é alterado depois do que é atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Redimensionar, repor ou outra manutenção/atualização interna ao gateway de VPN não alterar o endereço IP.

4. Verifique se as definições do gateway.
5. Selecione **Create** para criar o gateway VPN. As definições do gateway são validadas e o mosaico "Gateway de rede Virtual da implementação" é apresentado no seu dashboard.

   >[!Note]
   >A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

    Depois do gateway ser criado, pode ver o endereço IP atribuído ao mesmo ao examinar a rede virtual no portal. O gateway aparece como um dispositivo ligado. Para obter mais informações sobre o gateway, selecione o dispositivo.

6. Repita os passos anteriores (1 a 5) na sua implementação do Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Criar o gateway de rede local no Azure e o Azure Stack

O gateway de rede local refere-se normalmente à sua localização no local. Dar ao site um nome que o Azure ou no Azure Stack possa consultar e, em seguida, especifique:

- O endereço IP do dispositivo VPN no local que está a criar uma ligação para.
- Os prefixos de endereço IP que vão ser encaminhados através do gateway VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local.

  >[!Note]
  >Se as alterações de rede no local ou se precisar alterar o endereço IP público para o dispositivo VPN, pode atualizar facilmente estes valores mais tarde.

1. No portal, selecione **+ criar um recurso**.
2. Na caixa de pesquisa, introduza **gateway de rede Local**, em seguida, selecione **Enter** para procurar. Isto irá devolver uma lista de resultados.
3. Selecione **gateway de rede Local**, em seguida, selecione **Create** para abrir o **criar gateway de rede local** página.
4. No **criar gateway de rede local**, especifique os valores para o seu gateway de rede local, com o nosso **valores de exemplo Tutorial**. Incluem os seguintes valores adicionais.

    - **Endereço IP**: Este é o endereço IP público do dispositivo VPN que pretende que o Azure ou no Azure Stack para ligar a. Especifique um endereço IP público válido que não está protegido por um NAT para que o Azure consegue contactar o endereço. Se não tiver o endereço IP neste momento, pode utilizar o valor do exemplo como um espaço reservado, mas terá de voltar atrás e substituir o marcador de posição pelo endereço IP público do seu dispositivo VPN. Azure não consegue ligar ao dispositivo, enquanto não fornecer um endereço válido.
    - **Espaço de endereços**: Este é o intervalo de endereços para a rede que representa esta rede local. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar não se sobrepõem aos intervalos de outras redes às quais deseja se conectar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. Utilize os seus próprios valores para ligar ao seu site no local, não um valor de exemplo.
    - **Configurar definições de BGP**: Utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
    - **Subscrição**: Certifique-se de que é apresentada a subscrição correta.
    - **Grupo de recursos**: Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já criou.
    - **Localização**: Selecione a localização que este objeto será criado. Pode querer selecionar a mesma localização que a VNet se encontra, mas não é necessário para fazer isso.
5. Quando terminar de especificar os valores necessários, selecione **Create** para criar o gateway de rede local.
6. Repita estes passos (1 a 5) na sua implementação do Azure Stack.

## <a name="configure-your-connection"></a>Configurar a ligação

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Configurar o dispositivo de VPN é referido como uma ligação. Para configurar a ligação, terá de:

- Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
- O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para localizar o endereço IP público do seu gateway VPN no portal do Azure, navegue para gateways de rede Virtual, em seguida, selecione o nome do seu gateway.

Utilize os seguintes passos para criar uma ligação de VPN de Site a Site entre o gateway de rede virtual e o dispositivo VPN no local.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Procure **ligações**.
3. Na **resultados**, selecione **ligações**.
4. No **conexão**, selecione **criar**.
5. No **criar ligação**, configure as seguintes definições:

    - **Tipo de ligação**: Selecione o Site a site (IPSec).
    - **Grupo de recursos**: Selecione o grupo de recursos de teste.
    - **Gateway de rede virtual**: Selecione o gateway de rede Virtual que criou.
    - **Gateway de rede local**: Selecione o Gateway de rede Local que criou.
    - **Nome da ligação**: Isso é preenchido automaticamente com os valores dos dois gateways.
    - **Chave partilhada**: Este valor tem de corresponder ao valor que está a utilizar para o dispositivo VPN no local. Exemplo do tutorial utiliza "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que este valor tem de ser o mesmo valor que especificou quando configurar o dispositivo VPN.
    - Os valores para **subscrição**, **grupo de recursos**, e **localização** são fixos.

6. Selecione **OK** para criar a ligação.

Pode ver a ligação no **conexões** página do gateway de rede virtual. O estado será alterado de *desconhecido* para *ligar*e clique em *Succeeded*.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre os padrões de Cloud do Azure, veja [padrões de conceção de Cloud](https://docs.microsoft.com/azure/architecture/patterns).
