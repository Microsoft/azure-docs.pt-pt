---
title: 'Sobre perfis de cliente VPN ponto a site: gateway de VPN do Azure | Microsoft Docs'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 4e3276e6cde254daf2299f33d8b5ed9f54985327
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517660"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre perfis de cliente VPN P2S

O arquivo de perfil baixado contém informações necessárias para configurar uma conexão VPN. Este artigo o ajudará a obter e entender as informações necessárias para um perfil de cliente VPN.

## <a name="1-download-the-file"></a>1. baixar o arquivo

Execute os comandos a seguir. Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extraia o arquivo zip

Extraia o ficheiro zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genérica
* OpenVPN

## <a name="3-retrieve-information"></a>3. recuperar informações

Na pasta **AzureVPN** , navegue até o arquivo ***azurevpnconfig. xml*** e abra-o com o bloco de notas. Anote o texto entre as marcas a seguir.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Ao adicionar uma conexão, use as informações coletadas na etapa anterior para a página de detalhes do perfil. Os campos correspondem às seguintes informações:

   * **Público-alvo:** Identifica o recurso de destinatário para o qual o token se destina
   * **Emissor:** Identifica o serviço de token de segurança (STS) que emitiu o token, bem como o locatário do Azure AD
   * **Locatário:** Contém um identificador exclusivo e imutável do locatário do diretório que emitiu o token
   * **FQDN:** O FQDN (nome de domínio totalmente qualificado) no gateway de VPN do Azure
   * **ServerSecret:** A chave pré-compartilhada de gateway de VPN

## <a name="folder-contents"></a>Conteúdo da pasta

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows).

* A **pasta genérico** contém o certificado do servidor público e o arquivo VpnSettings. xml. O arquivo VpnSettings. xml contém as informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter pastas **WindowsAmd64** e **WindowsX86** . Essas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Você precisa de direitos de administrador no cliente para instalá-los.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre ponto a site, consulte [sobre ponto a site](point-to-site-about.md).