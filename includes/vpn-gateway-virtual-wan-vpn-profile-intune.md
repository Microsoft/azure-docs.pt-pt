---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f64bb0dd0841e89d05a4399db4373a9eaaec48a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750652"
---
Pode implementar perfis para clientes Azure VPN (Windows 10) utilizando o Microsoft Intune. Este artigo ajuda-o a criar um perfil Intune utilizando configurações personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Os dispositivos já estão matriculados com o Intune MDM.
* O Cliente Azure VPN para o Windows 10 já está implantado na máquina do cliente.
* Apenas a versão 19H2 ou superior do Windows é suportada.

## <a name="modify-xml"></a><a name="xml"></a>Modificar XML

Nos seguintes passos, utilizamos uma amostra de XML para um perfil OMA-URI personalizado para Intune com as seguintes definições:

* Ligação automática ON
* Deteção de rede fidedigna ativada.

Para outras opções apoiadas, consulte o artigo [do VPNv2 CSP.](/windows/client-management/mdm/vpnv2-csp)

1. Descarregue o perfil VPN do portal Azure e extraia o ficheiro *azurevpnconfig.xml* do pacote.
1. Copie e cole o texto abaixo num novo ficheiro de editor de texto.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Modifique a entrada entre ```<ServerUrlList>``` e com a entrada do seu perfil descarregado ```</ServerUrlList>``` (azurevpnconfig.xml). Altere o FQDN "TrustedNetworkDetection" para se adaptar ao seu ambiente.
1. Abra o perfil descarregado do Azure (azurevpnconfig.xml) e copie todo o conteúdo para a área de transferência, destacando o texto e pressionando (ctrl) + C. 
1. Cole o texto copiado do passo anterior para o ficheiro que criou no passo 2 entre as ```<CustomConfiguration>  </CustomConfiguration>``` etiquetas. Guarde o ficheiro com uma extensão xml.
1. Anota o valor nas ```<name>  </name>``` etiquetas. Este é o nome do perfil. Vai precisar deste nome quando criar o perfil no Intune. Feche o ficheiro e lembre-se do local onde está guardado.

## <a name="create-intune-profile"></a>Criar perfil Intune

Nesta secção, cria-se um perfil Microsoft Intune com definições personalizadas.

1. Inscreva-se no Intune e navegue para **dispositivos -> perfis de configuração**. Selecione **+ Criar perfil**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Perfis de configuração":::
1. Em **Plataforma**, selecione **Windows 10 e versões posteriores**. Para **Perfil**, selecione **Custom**. Em seguida, selecione **Criar**.
1. Dê ao perfil um nome e descrição e, em seguida, selecione **Next**.
1. No separador definições de **configuração,** selecione **Adicionar**.

    * **Nome:** Introduza um nome para a configuração.
    * **Descrição:** Descrição opcional.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (esta informação pode ser encontrada no ficheiro <name></name> azurevpnconfig.xml na etiqueta).
    * **Tipo de dados:** String (ficheiro XML).

   Selecione o ícone da pasta e escolha o ficheiro guardado no passo 6 nos passos [XML.](#xml) Selecione **Adicionar**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Definições de configuração" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Selecione **Seguinte**.
1. Em **Atribuições**, selecione o grupo ao qual pretende premir a configuração. Em seguida, selecione **Seguinte**.
1. As regras de aplicabilidade são opcionais. Defina quaisquer regras se necessário e, em seguida, selecione **Seguinte**.
1. Na página **'Rever + criar',** selecione **Criar.**

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Criar perfil":::
1. O seu perfil personalizado está agora criado. Para que os passos do Microsoft Intune implementem este perfil, consulte [perfils de utilizador e dispositivo de atribuição](/mem/intune/configuration/device-profile-assign).