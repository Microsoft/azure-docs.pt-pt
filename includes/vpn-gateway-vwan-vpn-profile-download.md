---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650644"
---
## <a name="1-download-the-file"></a>1. Descarregue o ficheiro

Execute os seguintes comandos. Copie o URL de resultados para o seu navegador para descarregar o ficheiro zip do perfil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrair o ficheiro zip

Extraia o ficheiro zip. O ficheiro contém as seguintes pastas:

* AzureVPN
* Genérica
* OpenVPN (Se tiver ativado as definições de autenticação OpenVPN e Azure AD no gateway). Para VPN Gateway, consulte [Criar um inquilino.](../articles/vpn-gateway/openvpn-azure-ad-tenant.md) Para O WAN Virtual, consulte [Criar um inquilino - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperar informações

Na pasta **AzureVPN,** navegue para o ficheiro ***azurevpnconfig.xml*** e abra-o com o Bloco de Notas. Tome nota do texto entre as seguintes etiquetas.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Quando adicionar uma ligação, utilize as informações recolhidas no passo anterior para a página de detalhes do perfil. Os campos correspondem às seguintes informações:

   * **Público:** Identifica o recurso recetor para o que o símbolo destina-se
   * **Emitente:** Identifica o Serviço de Token de Segurança (STS) que emitia o símbolo, bem como o inquilino da AD Azure
   * **Inquilino:** Contém um identificador imutável e único do inquilino do diretório que emitiu o símbolo
   * **FQDN:** O nome de domínio totalmente qualificado (FQDN) no gateway Azure VPN
   * **ServerSecret:** A chave pré-partilhada de gateway VPN

## <a name="folder-contents"></a>Conteúdo da pasta

* A **pasta genérica** contém o certificado de servidor público e o ficheiro VpnSettings.xml. O ficheiro VpnSettings.xml contém informações necessárias para configurar um cliente genérico.

* O ficheiro zip descarregado também pode conter as pastas **WindowsAmd64** e **WindowsX86.** Estas pastas contêm o instalador para clientes SSTP e IKEv2 para clientes Windows. Precisa de direitos administrativos sobre o cliente para instalá-los.
