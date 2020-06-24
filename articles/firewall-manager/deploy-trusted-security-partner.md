---
title: Implementar um fornecedor de parceiros de segurança Azure Firewall Manager
description: Saiba como implementar um fornecedor de parceiros de segurança Azure Firewall Manager utilizando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: victorh
ms.openlocfilehash: e06f8e3adaedbc8847aacba0ca4ad9c6a172c9b7
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791695"
---
# <a name="deploy-a-security-partner-provider-preview"></a>Implementar um fornecedor de parceiros de segurança (pré-visualização)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Os fornecedores de parceiros* de segurança no Azure Firewall Manager permitem-lhe utilizar as suas ofertas familiares e de segurança de terceiros (SECaaS) para proteger o acesso à Internet aos seus utilizadores.

Para saber mais sobre cenários apoiados e diretrizes de boas práticas, veja [o que são parceiros de segurança de confiança (pré-visualização)?](trusted-security-partners.md)

Os parceiros de segurança apoiados são **ZScaler,** **Check Point**e **iboss** para esta pré-visualização. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A pré-visualização do Gestor de Firewall Azure deve ser ativada explicitamente utilizando o `Register-AzProviderFeature` comando PowerShell.

A partir de um pedido de comando PowerShell, executar os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Leva até 30 minutos para que a inscrição da funcionalidade esteja concluída. Executar o seguinte comando para verificar o seu estado de registo:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implementar um fornecedor de segurança de terceiros num novo hub

Ignore esta secção se estiver a colocar um fornecedor de terceiros num centro existente.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Em **Busca**, escreva **Gestor de Firewall** e selecione-o em **Serviços**.
3. Navegue para **começar.** Selecione **Criar um Hub Virtual Seguro**. 
4. Insira-lhe o grupo de subscrição e recursos, selecione uma região suportada e adicione o seu hub e informações de WAN virtuais. 
5. **O gateway VPN de implementação** é ativado por predefinição. Um Gateway VPN é necessário para colocar um parceiro de segurança fidedigno no centro. 
6. Selecione **Seguinte: Azure Firewall**
   > [!NOTE]
   > Parceiros de segurança de confiança ligam-se ao seu hub usando túneis VPN Gateway. Se eliminar o Gateway VPN, as ligações aos seus parceiros de segurança fidedignos perdem-se.
7. Se pretender implantar o Azure Firewall para filtrar o tráfego privado juntamente com o fornecedor de serviços de terceiros para filtrar o tráfego da Internet, selecione uma política para o Azure Firewall. Veja os [cenários apoiados.](trusted-security-partners.md#key-scenarios)
8. Se pretender colocar apenas um fornecedor de segurança de terceiros no centro, selecione **Azure Firewall: Habilitado/Desativado** para o definir para **Desativado**. 
9. Selecione **Seguinte: Fornecedor de parceiros de segurança**.
10. Selecione **o Fornecedor de Parceiros de Segurança** para defini-lo como **Ativado**. Selecione um parceiro. 
11. Selecione **Seguinte**. 
12. Reveja o conteúdo e, em seguida, **selecione Criar**.

A colocação de gateway VPN pode demorar mais de 30 minutos.

Para verificar se o hub foi criado, navegue para Azure Firewall Manager->Secured Hubs. Selecione a página de visão geral >hub para mostrar o nome do parceiro e o estado como **Ligação de Segurança Pendente**.

Assim que o hub for criado e o parceiro de segurança for criado, continue a ligar o fornecedor de segurança ao centro.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implementar um fornecedor de segurança de terceiros num hub existente

Também pode selecionar um hub existente num WAN Virtual e convertê-lo num *hub virtual seguro.*

1. Em **"Começar",** selecione **Converter os Centros Existentes**.
2. Selecione uma subscrição e um hub existente. Siga o resto dos passos para colocar um fornecedor de terceiros num novo hub.

Lembre-se que deve ser implantado um gateway VPN para converter um hub existente para um hub seguro com fornecedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configure fornecedores de segurança de terceiros para ligar a um centro seguro

Para configurar túneis para o VPN Gateway do seu hub virtual, os fornecedores de terceiros precisam de direitos de acesso ao seu hub. Para isso, associe um diretor de serviço ao seu grupo de subscrição ou recursos e conceda direitos de acesso. Em seguida, deve dar estas credenciais a terceiros usando o seu portal.

### <a name="create-and-authorize-a-service-principal"></a>Criar e autorizar um diretor de serviço

1. Criar o diretor de serviço Azure Ative Directory (AD): Pode saltar o URL de redirecionamento. 

   [How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adicione direitos de acesso e âmbito para o principal serviço.
   [How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Pode limitar o acesso apenas ao seu grupo de recursos para um maior controlo granular.

### <a name="visit-partner-portal"></a>Visite o portal do parceiro

1. Siga o seu parceiro com instruções para completar a configuração. Isto inclui submeter informações de AAD para detetar e ligar ao hub, atualizar as políticas de saída e verificar o estado de conectividade e registos.

   - [Zscaler: Configurar a integração virtual WAN do Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: Configurar a integração do Microsoft Azure Virtual WAN](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   
2. Pode olhar para o estado de criação do túnel no portal Azure Virtual WAN em Azure. Assim que os túneis mostrarem **ligados** tanto no Azure como no portal do parceiro, continue com os próximos passos para definir rotas para selecionar quais os ramos e VNets que devem enviar o tráfego de Internet para o parceiro.

## <a name="configure-route-settings"></a>Configurar definições de rota

1. Navegue pelo Azure Firewall Manager -> Secured Hubs. 
2. Selecione um hub. O estatuto do Hub deve agora mostrar **provisionado** em vez de **Conexão**de Segurança Pendente .

   Certifique-se de que o fornecedor de terceiros pode ligar-se ao hub. Os túneis na porta de entrada da VPN devem estar num estado **ligado.** Este estado é mais reflexo da ligação entre o centro e o parceiro de terceiros, em comparação com o estado anterior.
3. Selecione o hub e navegue para **definições de rota**.

   Quando coloca um fornecedor de terceiros no centro, converte o hub num *centro virtual seguro.* Isto garante que o fornecedor de terceiros está a publicitar uma rota 0.0.0.0/0 (padrão) para o hub. No entanto, as ligações VNet e os sites ligados ao hub não obtêm esta rota a menos que opte por quais ligações devem obter esta rota predefinido.
4. No **tráfego de Internet**, selecione **VNet-to-Internet** ou **Branch-to-Internet** ou ambas as rotas são configuradas enviar através de terceiros.

   Isto apenas indica que tipo de tráfego deve ser encaminhado para o centro, mas ainda não afeta as rotas em VNets ou ramos. Estas rotas não são propagadas a todos os VNets/ramos ligados ao hub por padrão.
5. Tem de selecionar **ligações seguras** e selecionar as ligações em que estas rotas devem ser definidas. Isto indica quais vNets/branches podem começar a enviar tráfego de Internet para o fornecedor de terceiros.
6. A partir **das definições**de Rota , selecione **ligações Seguras** no tráfego da Internet e, em seguida, selecione o VNet ou os ramos *(sites* em WAN Virtual) a serem protegidos. Selecione **tráfego de Internet seguro**.
   ![Tráfego de Internet seguro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navegue de volta para a página dos hubs. O estatuto de parceiro de **segurança fidedigno** do centro deve agora ser **garantido.**

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de internet de sucursais ou VNet através de serviço de terceiros

Em seguida, pode verificar se as máquinas virtuais VNet ou o site da sucursal podem aceder à Internet e validar que o tráfego está a fluir para o serviço de terceiros.

Depois de terminar os passos de definição da rota, as máquinas virtuais VNet, bem como os sites de ramificação, são enviadas uma rota de serviço de 0/0 para terceiros. Não se pode pDR ou SSH nestas máquinas virtuais. Para iniciar sação, pode implantar o serviço [Azure Bastion](../bastion/bastion-overview.md) num VNet com par.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com pré-visualização do Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md)