---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979078"
---
## <a name="extract-the-zip-file"></a>Extrair o ficheiro zip

Extraia o ficheiro zip. O ficheiro contém as seguintes pastas:

* AzureVPN
* Genérica
* OpenVPN (Se tiver ativado o OpenVPN com **certificado Azure** ou definições **de autenticação RADIUS** no gateway). Selecione o artigo apropriado que corresponda à sua configuração para criar um inquilino.

  * [VPN Gateway- Criar um inquilino.](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)
  * [Virtual WAN - Criar um inquilino.](../articles/virtual-wan/openvpn-azure-ad-tenant.md)

## <a name="retrieve-information"></a>Recuperar informações

Na pasta **AzureVPN,** navegue para o ficheiro **_azurevpnconfig.xml_** e abra-o com o Bloco de Notas. Tome nota do texto entre as seguintes etiquetas.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Quando adicionar uma ligação, utilize as informações recolhidas no passo anterior para a página de detalhes do perfil. Os campos correspondem às seguintes informações:

* **Público:** Identifica o recurso do destinatário para o qual o token se destina.
* **Emitente:** Identifica o Serviço de Token de Segurança (STS) que emitia o token, bem como o inquilino da AD Azure.
* **Inquilino:** Contém um identificador imutável e único do inquilino do diretório que emitiu o símbolo.
* **FQDN:** O nome de domínio totalmente qualificado (FQDN) no gateway Azure VPN.
* **ServerSecret:** A chave pré-partilhada do gateway VPN.

## <a name="folder-contents"></a>Conteúdo de pasta

* A **pasta genérica** contém o certificado do servidor público e o ficheiro VpnSettings.xml. O ficheiro VpnSettings.xml contém informações necessárias para configurar um cliente genérico.

* O ficheiro zip descarregado também pode conter **pastas WindowsAmd64** e **WindowsX86.** Estas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Precisa de direitos de administração sobre o cliente para instalá-los.
