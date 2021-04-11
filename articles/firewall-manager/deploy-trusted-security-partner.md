---
title: Implementar um fornecedor de parceiros de segurança Azure Firewall Manager
description: Saiba como implementar um fornecedor de parceiros de segurança Azure Firewall Manager utilizando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: b8e10eef89df12807cabd96d64d9c7d659f91d6c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109514"
---
# <a name="deploy-a-security-partner-provider"></a>Implementar um fornecedor de parceiros de segurança

*Os fornecedores de parceiros* de segurança no Azure Firewall Manager permitem-lhe utilizar as suas ofertas familiares e de segurança de terceiros (SECaaS) para proteger o acesso à Internet aos seus utilizadores.

Para saber mais sobre cenários apoiados e diretrizes de boas práticas, veja [o que são os fornecedores de parceiros de segurança?](trusted-security-partners.md)


Estão agora disponíveis parceiros integrados de Segurança de terceiros como um serviço (SECaaS): 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implementar um fornecedor de segurança de terceiros num novo hub

Ignore esta secção se estiver a colocar um fornecedor de terceiros num centro existente.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Em **Busca**, escreva **Gestor de Firewall** e selecione-o em **Serviços**.
3. Navegue para **começar.** Selecione **Ver centros virtuais seguros**.
4. **Selecione Criar um novo hub virtual seguro**.
5. Insira-lhe o grupo de subscrição e recursos, selecione uma região suportada e adicione o seu hub e informações de WAN virtuais. 
6. **Selecione Incluir gateway VPN para ativar fornecedores de parceiros de segurança**.
7. Selecione as **unidades de escala Gateway** adequadas para os seus requisitos.
8. Selecione **Seguinte: Azure Firewall**
   > [!NOTE]
   > Os fornecedores de parceiros de segurança ligam-se ao seu hub utilizando túneis VPN Gateway. Se eliminar o Gateway VPN, perdem-se as ligações com os fornecedores de parceiros de segurança.
9. Se pretender implantar o Azure Firewall para filtrar o tráfego privado juntamente com o fornecedor de serviços de terceiros para filtrar o tráfego da Internet, selecione uma política para o Azure Firewall. Veja os [cenários apoiados.](trusted-security-partners.md#key-scenarios)
10. Se pretender colocar apenas um fornecedor de segurança de terceiros no centro, selecione **Azure Firewall: Habilitado/Desativado** para o definir para **Desativado**. 
11. Selecione  **Seguinte: Fornecedor de parceiros de segurança**.
12. Definir **o fornecedor de parceiros de** segurança para **ativar**. 
13. Selecione um parceiro. 
14. Selecione **Seguinte: Rever + criar**. 
15. Reveja o conteúdo e, em seguida, **selecione Criar**.

A colocação de gateway VPN pode demorar mais de 30 minutos.

Para verificar se o hub foi criado, navegue para Azure Firewall Manager->Secured Hubs. Selecione a página de visão geral >hub para mostrar o nome do parceiro e o estado como **Ligação de Segurança Pendente**.

Assim que o hub for criado e o parceiro de segurança for criado, continue a ligar o fornecedor de segurança ao centro.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implementar um fornecedor de segurança de terceiros num hub existente

Também pode selecionar um hub existente num WAN Virtual e convertê-lo num *hub virtual seguro.*

1. Em **Getting Start**, selecione Ver centros virtuais **seguros**.
2. Selecione **Converter os centros existentes**.
3. Selecione uma subscrição e um hub existente. Siga o resto dos passos para colocar um fornecedor de terceiros num novo hub.

Lembre-se que deve ser implantado um gateway VPN para converter um hub existente para um hub seguro com fornecedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configure fornecedores de segurança de terceiros para ligar a um centro seguro

Para configurar túneis para o VPN Gateway do seu hub virtual, os fornecedores de terceiros precisam de direitos de acesso ao seu hub. Para isso, associe um diretor de serviço ao seu grupo de subscrição ou recursos e conceda direitos de acesso. Em seguida, deve dar estas credenciais a terceiros usando o seu portal.

### <a name="create-and-authorize-a-service-principal"></a>Criar e autorizar um diretor de serviço

1. Criar o diretor de serviço Azure Ative Directory (AD): Pode saltar o URL de redirecionamento. 

   [How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Adicione direitos de acesso e âmbito para o principal serviço.
   [How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Pode limitar o acesso apenas ao seu grupo de recursos para um maior controlo granular.

### <a name="visit-partner-portal"></a>Visite o portal do parceiro

1. Siga o seu parceiro com instruções para completar a configuração. Isto inclui submeter informações de AAD para detetar e ligar ao hub, atualizar as políticas de saída e verificar o estado de conectividade e registos.

   - [Zscaler: Configurar a integração virtual WAN do Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: Configurar a integração do Microsoft Azure Virtual WAN](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: Configurar a integração virtual WAN da Microsoft Azure](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Pode olhar para o estado de criação do túnel no portal Azure Virtual WAN em Azure. Assim que os túneis mostrarem **ligados** tanto no Azure como no portal do parceiro, continue com os próximos passos para definir rotas para selecionar quais os ramos e VNets que devem enviar o tráfego de Internet para o parceiro.

## <a name="configure-security-with-firewall-manager"></a>Configure a segurança com o Gestor de Firewall

1. Navegue pelo Azure Firewall Manager -> Secured Hubs. 
2. Selecione um hub. O estado do hub deve agora mostrar **provisionado** em vez de **Conexão** de Segurança Pendente .

   Certifique-se de que o fornecedor de terceiros pode ligar-se ao hub. Os túneis na porta de entrada da VPN devem estar num estado **ligado.** Este estado é mais reflexo da ligação entre o centro e o parceiro de terceiros, em comparação com o estado anterior.
3. Selecione o hub e navegue para **configurações de segurança**.

   Quando coloca um fornecedor de terceiros no centro, converte o hub num *centro virtual seguro.* Isto garante que o fornecedor de terceiros está a publicitar uma rota 0.0.0.0/0 (padrão) para o hub. No entanto, as ligações VNet e os sites ligados ao hub não obtêm esta rota a menos que opte por quais ligações devem obter esta rota predefinido.
4. Configure a segurança virtual WAN definindo **o tráfego de Internet** através do Azure Firewall e do Tráfego **Privado** através de um parceiro de segurança confiável. Isto protege automaticamente as ligações individuais no WAN Virtual.

   :::image type="content" source="media/deploy-trusted-security-partner/security-configuration.png" alt-text="Configuração de segurança":::
5. Além disso, se a sua organização utilizar gamas de IP públicas em redes virtuais e sucursais, tem de especificar explicitamente esses prefixos IP utilizando **prefixos de tráfego privado.** Os prefixos de endereço IP públicos podem ser especificados individualmente ou como agregados.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de internet de sucursais ou VNet através de serviço de terceiros

Em seguida, pode verificar se as máquinas virtuais VNet ou o site da sucursal podem aceder à Internet e validar que o tráfego está a fluir para o serviço de terceiros.

Depois de terminar os passos de definição da rota, as máquinas virtuais VNet, bem como os sites de ramificação são enviados um 0/0 para a rota de serviço de terceiros. Não se pode pDR ou SSH nestas máquinas virtuais. Para iniciar sação, pode implantar o serviço [Azure Bastion](../bastion/bastion-overview.md) num VNet com par.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md)