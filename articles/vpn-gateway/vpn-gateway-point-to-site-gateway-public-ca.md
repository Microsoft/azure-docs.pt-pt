---
title: Transição com assinatura automática para certificados de AC públicas para P2S gateways | Gateway de VPN do Azure | Documentos da Microsoft
description: Este artigo ajuda-o com êxito a transição para os novos certificados de AC públicos para gateways de P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cherylmc
ms.openlocfilehash: e5a75826730219adc643d7c6ca300a38c8640006
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428369"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Transição para um certificado de gateway de AC público para P2S

O Gateway de VPN do Azure já não emite certificados autoassinados do nível do Azure com seus gateways para ligações P2S. Certificados emitidos agora são assinados por um certificado de autoridade (autoridade de certificação pública). No entanto, alguns dos gateways mais antigos podem ainda utilizar certificados autoassinados. Estes certificados autoassinados são quase respetivas datas de expiração e devem fazer a transição para certificados de AC públicos.

Os certificados neste contexto são um certificado de nível de Azure adicional. Não são as cadeias de certificados que utiliza ao gerar seus próprios certificados de raiz autoassinados e certificados de cliente para autenticação. Esses certificados não são afetados e irão expirar nas datas gerado-los para fazer isso.

>[!NOTE]
> Utilizado para autenticação de cliente de P2S os certificados autoassinados não são afetados por esta alteração de certificado de nível do Azure. Pode continuar a emitir e utilizar certificados autoassinados como habitualmente.
>

Anteriormente, um certificado autoassinado para o gateway (emitido em segundo plano pelo Azure) necessário para ser atualizado a cada 18 meses. Ficheiros de configuração de cliente VPN, em seguida, tinham que ser gerado e reimplantada em todos os clientes de P2S. Mover para certificados de AC públicos elimina essa limitação. Além da transição para certificados, esta alteração também fornece aprimoramentos de plataforma, métricas melhor e maior estabilidade.

Apenas os gateways mais antigos são afetados por esta alteração. Se o certificado de gateway tem de ser transferido, receberá comunicação ou alerta no portal do Azure. Pode verificar se o gateway é afetado, utilizando os passos neste artigo.

>[!IMPORTANT]
>A transição está agendada para Março 12,2019 começando 18:00 UTC. Pode criar um incidente de suporte, se preferir uma janela de tempo diferentes. Faça e finalizar o pedido com antecedência, pelo menos, 24 horas.  Pode pedir uma das janelas do seguintes:
>
>* UTC GMT+06:00 em 25 de Fevereiro
>* UTC 18:00 em 25 de Fevereiro
>* UTC 06:00 no 1 Mar
>* UTC 18:00 em 1 de Março
>
>**Todos os gateways restantes farão a transição 12 de Março de 2019 começando 18:00 UTC**.
>

## <a name="1-verify-your-certificate"></a>1. Verifique se o seu certificado

1. Verifique se são afetados por esta atualização. Transferir a configuração atual do cliente VPN utilizando os passos em [este artigo](point-to-site-vpn-client-configuration-azure-cert.md).

2. Abra ou extraia o ficheiro zip e navegue até à pasta "Genérica". Na pasta genérica, verá dois arquivos, um dos quais é *Vpnsettings*.
3. Open *Vpnsettings* em qualquer Visualizador/editor de xml. No arquivo xml, procure os seguintes campos:

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Se *ServerCertRotCn* e *ServerCertIssuerCn* são "DigiCert Global AC de raiz", não são afetadas por esta atualização e não precisa de continuar com os passos neste artigo. No entanto, se eles mostram algo mais, o seu certificado de gateway faz parte da atualização e será transferido.

## <a name="2-check-certificate-transition-schedule"></a>2. Agenda de transição de certificado de verificação

Se o certificado é parte da atualização, o certificado de gateway será transferido. Consulte a **importante** nota para os tempos de transição. Após a atualização, os clientes de P2S não serão possível estabelecer ligação utilizando o respetivo perfil antigo. Tem de gerar novos perfis de cliente VPN e instalá-las nos clientes.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Gerar o perfil de configuração de cliente VPN

Depois do certificado foram migrado, Baixe o novo perfil VPN (ficheiros de configuração de cliente VPN) no portal do Azure. Para obter os passos, consulte [criar e instalar ficheiros de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md). Não é necessário gerar novos certificados de cliente.

## <a name="4-deploy-vpn-client-profile"></a>4. Implementar perfil de cliente VPN

Implemente o novo perfil a todos os clientes VPN ponto a site. Os clientes VPN irão perder a conectividade de até que o novo perfil VPN para ligações ponto a site é transferido e implementado em dispositivos cliente. Os certificados de cliente que já estão instalados nos computadores de cliente VPN não precisa de ser emitido novamente.

## <a name="5-connect-the-vpn-client"></a>5. Ligue-se o cliente VPN

Ligar ao Azure do cliente VPN, tal como faria normalmente.
