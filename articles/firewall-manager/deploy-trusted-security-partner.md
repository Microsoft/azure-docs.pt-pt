---
title: Implantar um parceiro de segurança confiável do Gerenciador de firewall do Azure
description: Saiba como implantar uma segurança confiável do Gerenciador de firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: fe733b686f2b56beee26a6c33c4d6264d621e627
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516347"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Implantar um parceiro de segurança confiável (versão prévia)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Os *parceiros de segurança confiáveis* no Gerenciador de firewall do Azure permitem que você use suas ofertas de SECaaS (segurança como serviço) mais conhecidas e de terceiros para proteger o acesso à Internet para seus usuários.

Para saber mais sobre cenários com suporte e diretrizes de práticas recomendadas, consulte [o que são parceiros de segurança confiáveis (versão prévia)?](trusted-security-partners.md).

Os parceiros de segurança com suporte são **ZScaler** e **iboss** para esta versão prévia. As regiões com suporte são WestCentralUS, NorthCentralUS, Westus, WestUS2 e Eastus.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A versão prévia do Gerenciador de firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` PowerShell.

Em um prompt de comando do PowerShell, execute os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Demora até 30 minutos para que o registro do recurso seja concluído. Execute o seguinte comando para verificar o status do registro:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implantar um provedor de segurança de terceiros em um novo hub

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Em **pesquisa**, digite **Gerenciador de firewall** e selecione-o em **Serviços**.
3. Navegue até **introdução**. Selecione **criar um hub virtual protegido**. 
4. Insira sua assinatura e grupo de recursos, selecione uma região com suporte e adicione as informações de Hub e WAN virtual. 
5. A **implantação do gateway de VPN** está habilitada por padrão. Um gateway de VPN é necessário para implantar um parceiro de segurança confiável no Hub. 
6. Selecione **Avançar: Firewall do Azure**
   > [!NOTE]
   > Os parceiros de segurança confiáveis se conectam ao seu hub usando túneis de gateway de VPN. Se você excluir o gateway de VPN, as conexões com seus parceiros de segurança confiáveis serão perdidas.
7. Se você quiser implantar o Firewall do Azure para filtrar o tráfego privado junto com o provedor de serviços de terceiros para filtrar o tráfego de Internet, selecione uma política para o Firewall do Azure. Consulte os [cenários com suporte](trusted-security-partners.md#key-scenarios).
8. Se você quiser implantar apenas um provedor de segurança de terceiros no Hub, selecione **Firewall do Azure: habilitado/desabilitado** para defini-lo como **desabilitado**. 
9. Selecione **Avançar: parceiros de segurança confiáveis**.
10. Selecione **parceiro de segurança confiável** para defini-lo como **habilitado**. Selecione um parceiro. 
11. Selecione **Seguinte**. 
12. Examine o conteúdo e, em seguida, selecione **criar**.

A implantação do gateway de VPN pode levar mais de 30 minutos.

Para verificar se o Hub foi criado, navegue até o Gerenciador de firewall do Azure-> hubs protegidos. Selecione a página Visão geral do Hub-> para mostrar o nome do parceiro e o status como **conexão de segurança pendente**.

Depois que o Hub for criado e o parceiro de segurança estiver configurado, continue para conectar o provedor de segurança ao Hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implantar um provedor de segurança de terceiros em um Hub existente

Você também pode selecionar um Hub existente em uma WAN virtual e convertê-lo em um *Hub virtual protegido*.

1. Em **introdução**, selecione **converter hubs existentes**.
2. Selecione uma assinatura e um Hub existente. Siga as etapas restantes para implantar um provedor de terceiros em um novo hub.

Lembre-se de que um gateway de VPN deve ser implantado para converter um Hub existente para um hub protegido com provedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurar provedores de segurança de terceiros para se conectar a um hub protegido

Para configurar túneis para o gateway de VPN do seu hub virtual, os provedores de terceiros precisam de direitos de acesso ao seu hub. Para fazer isso, associe uma entidade de serviço com sua assinatura ou grupo de recursos e conceda direitos de acesso. Em seguida, você deve fornecer essas credenciais para terceiros usando seu portal.

1. Criar entidade de serviço do Azure Active Directory (AD): você pode ignorar a URL de redirecionamento. 

   [Como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adicionar direitos de acesso e escopo para a entidade de serviço.
   [Como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Você pode limitar o acesso somente ao seu grupo de recursos para um controle mais granular.
3. Siga as instruções no link a seguir.

   - Para entrar no portal de parceiros e adicionar suas credenciais para conceder ao parceiro confiável acesso ao seu hub protegido.
   - Depois que suas credenciais de autenticação do Azure AD forem validadas, use as instruções a seguir para sincronizar os hubs virtuais no portal de parceiros e configurar o túnel para o Hub virtual.

   [ZScaler: Configurando uma integração de WAN virtual Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)
4. Você pode examinar o status de criação do túnel no portal de WAN virtual do Azure no Azure. Depois que os túneis mostrarem **conectados** no portal do Azure e do parceiro, continue com as próximas etapas para configurar as rotas para selecionar quais branches e VNets devem enviar o tráfego de Internet para o parceiro.

## <a name="configure-route-settings"></a>Definir configurações de rota

1. Navegue até o Gerenciador de firewall do Azure-> os hubs protegidos. 
2. Selecione um Hub. O status do hub agora deve mostrar **provisionado** em vez da **conexão de segurança pendente**.

   Verifique se o provedor de terceiros pode se conectar ao Hub. Os túneis no gateway de VPN devem estar em um estado **conectado** . Esse estado é mais refletido na integridade da conexão entre o Hub e o parceiro de terceiros, em comparação com o status anterior.
3. Selecione o Hub e navegue até **configurações de rota**.

   Quando você implanta um provedor de terceiros no Hub, ele converte o Hub em um *Hub virtual protegido*. Isso garante que o provedor de terceiros esteja anunciando uma rota 0.0.0.0/0 (padrão) para o Hub. No entanto, conexões VNet e sites conectados ao Hub não obtêm essa rota, a menos que você opte pelo qual as conexões devem obter essa rota padrão.
4. Em **tráfego da Internet**, selecione **VNet a Internet** ou **ramificação para a Internet** ou ambas as rotas são configuradas como enviar por meio de terceiros.

   Isso indica apenas o tipo de tráfego que deve ser roteado para o Hub, mas ele não afeta as rotas em VNets ou branches ainda. Essas rotas não são propagadas para todos os VNets/branches anexados ao Hub por padrão.
5. Você deve selecionar **conexões seguras** e selecionar as conexões nas quais essas rotas devem ser definidas. Isso indica quais VNets/branches podem começar a enviar tráfego de Internet para o provedor de terceiros.
6. Em **configurações de rota**, selecione **conexões seguras** em tráfego da Internet e, em seguida, selecione a VNet ou branches (*sites* na WAN virtual) a serem protegidos. Selecione **proteger o tráfego da Internet**.
   ![o tráfego de Internet seguro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navegue de volta para a página hubs. O status do **parceiro de segurança confiável** do hub agora deve ser **protegido**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de Internet de filial ou VNet por meio de serviço de terceiros

Em seguida, você pode verificar se as máquinas virtuais VNet ou o site da filial podem acessar a Internet e validar se o tráfego está fluindo para o serviço de terceiros.

Depois de concluir as etapas de configuração de rota, as máquinas virtuais de VNet, bem como os sites de ramificação, são enviadas um 0/0 para rota de serviço de terceiros. Não é possível RDP ou SSH nessas máquinas virtuais. Para entrar, você pode implantar o serviço de [bastiões do Azure](../bastion/bastion-overview.md) em uma VNet emparelhada.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: proteger sua rede de nuvem com a versão prévia do Gerenciador de firewall do Azure usando o portal do Azure](secure-cloud-network.md)




