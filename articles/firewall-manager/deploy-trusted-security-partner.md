---
title: Implementar um parceiro de segurança fidedigno do Azure Firewall Manager
description: Saiba como implementar um Azure Firewall Manager de segurança fidedigno usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931311"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Implementar um parceiro de segurança fidedigno (pré-visualização)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Parceiros de segurança fidedignos* no Azure Firewall Manager permite-lhe utilizar as suas ofertas familiares e de segurança de terceiros (SECaaS) para proteger o acesso à Internet dos seus utilizadores.

Para saber mais sobre cenários apoiados e diretrizes de boas práticas, consulte O que são parceiros de [segurança de confiança (pré-visualização)?](trusted-security-partners.md)

Os parceiros de segurança apoiados são **zScaler** e **iboss** para esta pré-visualização. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A pré-visualização do Gestor de `Register-AzProviderFeature` Firewall Azure deve ser ativada explicitamente utilizando o comando PowerShell.

A partir de um pedido de comando PowerShell, executar os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Leva até 30 minutos para que a inscrição da funcionalidade seja concluída. Executar o seguinte comando para verificar o seu estado de registo:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implementar um fornecedor de segurança de terceiros num novo hub

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Em **Search**, digite **o Firewall Manager** e selecione-o em **Serviços**.
3. Navegar para **começar.** Selecione **Criar um Hub Virtual Seguro**. 
4. Insira o seu grupo de subscrição e recursos, selecione uma região apoiada e adicione o seu hub e informações virtuais wan. 
5. **A implementação** do gateway VPN está ativada por defeito. Um Gateway VPN é necessário para colocar um parceiro de segurança de confiança no centro. 
6. Selecione **Seguinte: Firewall Azure**
   > [!NOTE]
   > Parceiros de segurança fidedignos ligam-se ao seu hub usando túneis VPN Gateway. Se eliminar o Gateway VPN, as ligações aos seus parceiros de segurança fidedignos estão perdidas.
7. Se pretender implementar o Azure Firewall para filtrar o tráfego privado juntamente com um prestador de serviços de terceiros para filtrar o tráfego de Internet, selecione uma política para o Azure Firewall. Veja os [cenários suportados](trusted-security-partners.md#key-scenarios).
8. Se pretender apenas implantar um fornecedor de segurança de terceiros no centro, **selecione Firewall Azure: Ativado/Desativado** para o definir para **Desativado**. 
9. Selecione **Seguinte: Parceiros de Segurança Fidedignos**.
10. Selecione **Trusted Security Partner** para defini-lo para **Ativado**. Selecione um parceiro. 
11. Selecione **Next**. 
12. Reveja o conteúdo e, em seguida, selecione **Criar**.

A implantação do gateway VPN pode demorar mais de 30 minutos.

Para verificar se o hub foi criado, navegue para o Azure Firewall Manager->Secured Hubs. Selecione a página de visão geral do hub->para mostrar o nome do parceiro e o estado como **Ligação de Segurança Pendente**.

Assim que o centro for criado e o parceiro de segurança for criado, continue a ligar o fornecedor de segurança ao centro.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implementar um fornecedor de segurança de terceiros num centro existente

Também pode selecionar um hub existente num WAN virtual e convertê-lo num *centro virtual seguro.*

1. Em **Getting Started,** selecione **Converter Centros Existentes**.
2. Selecione uma subscrição e um hub existente. Siga o resto dos passos para implantar um fornecedor de terceiros num novo hub.

Lembre-se que deve ser implantado um gateway VPN para converter um hub existente para um hub seguro com fornecedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configure fornecedores de segurança de terceiros para ligar a um hub seguro

Para configurar túneis para o VPN Gateway do seu centro virtual, os fornecedores de terceiros precisam de direitos de acesso ao seu hub. Para isso, associe um diretor de serviço ao seu grupo de subscrição ou recursos e conceda direitos de acesso. Em seguida, deve dar estas credenciais a terceiros usando o seu portal.

1. Crie o diretor de serviço do Azure Ative Directory (AD): Pode saltar o URL de redirecionamento. 

   [Como: Utilizar o portal para criar uma aplicação e diretor de serviço seletiva azure que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adicione direitos de acesso e margem de manobra para o diretor de serviço.
   [Como: Utilizar o portal para criar uma aplicação e diretor de serviço seletiva azure que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Pode limitar o acesso apenas ao seu grupo de recursos para um controlo mais granular.
3. Siga o [ZScaler: Configurar as](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) instruções de integração virtual wan do Microsoft Azure para:

   - Inscreva-se no portal do parceiro e adicione as suas credenciais para dar ao parceiro de confiança acesso ao seu hub seguro.
   - Sincronize os centros virtuais no portal do parceiro e instale o túnel no centro virtual. Pode fazê-lo assim que as suas credenciais de autenticação Azure AD forem validadas.
   
4. Você pode olhar para o estado de criação do túnel no portal Azure Virtual WAN em Azure. Assim que os túneis mostrarem **ligados** tanto no Azure como no portal do parceiro, continue com os próximos passos para configurar rotas para selecionar quais os ramos e VNets que devem enviar tráfego de Internet para o parceiro.

## <a name="configure-route-settings"></a>Configurar as definições de rota

1. Navegue pelo Azure Firewall Manager -> Secured Hubs. 
2. Selecione um centro. O estatuto do Hub deve agora mostrar **o provisionamento** em vez de **ligação**de segurança pendente .

   Certifique-se de que o fornecedor de terceiros pode ligar-se ao centro. Os túneis na porta de entrada da VPN devem estar em estado **de ligação.** Este estado reflete mais a saúde da ligação entre o centro e o parceiro de terceiros, em comparação com o estatuto anterior.
3. Selecione o hub e navegue para **Definições de Rota**.

   Quando se coloca um fornecedor de terceiros no centro, converte o hub num *centro virtual seguro.* Isto garante que o fornecedor de terceiros está a anunciar uma rota de 0.0.0.0/0 (padrão) para o centro. No entanto, as ligações VNet e os sites ligados ao hub não obtêm esta rota a menos que opte por quais as ligações que devem obter esta rota padrão.
4. Sob **o tráfego**da Internet , selecione **VNet-to-Internet** ou **Branch-to-Internet** ou ambas as rotas são configuradas através de terceiros.

   Isto apenas indica que tipo de tráfego deve ser encaminhado para o centro, mas ainda não afeta as rotas em VNets ou ramos. Estas rotas não são propagadas a todos os VNets/ramos ligados ao centro por defeito.
5. Deve selecionar **ligações seguras** e selecionar as ligações em que estas rotas devem ser definidas. Isto indica quais vNets/sucursais podem começar a enviar tráfego de Internet para o fornecedor de terceiros.
6. A partir das **definições da Rota,** selecione **ligações Seguras** sob tráfego de Internet e, em seguida, selecione o VNet ou balcões *(sites* em WAN Virtual) para ser seguro. Selecione **tráfego de Internet seguro**.
   ![Tráfego de Internet seguro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navegue de volta para a página do hubs. O estatuto de parceiro de **segurança fidedigno** do centro deve agora ser **assegurado.**

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de Internet branch ou VNet via serviço de terceiros

Em seguida, pode verificar se as máquinas virtuais VNet ou o site da sucursal podem aceder à Internet e validar que o tráfego está a fluir para o serviço de terceiros.

Depois de terminar os passos de definição do percurso, as máquinas virtuais VNet, bem como os sites de ramificação são enviados uma rota de serviço de 0/0 para terceiros. Não pode sutificar ou SSH nestas máquinas virtuais. Para iniciar sessão, pode implantar o serviço [Azure Bastion](../bastion/bastion-overview.md) num VNet com um par.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com a Pré-visualização do Gestor de Firewall Azure utilizando o portal Azure](secure-cloud-network.md)




