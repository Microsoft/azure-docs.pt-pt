---
title: 'Azure VPN Gateway: Ligações ponto-a-site: clientes Mac OS X'
description: Passos para resolver as ligações de clientes P2S Mac OS X VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425731"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Ligações VPN ponto-a-site de clientes Mac OS X VPN

Este artigo ajuda-o a resolver problemas de conectividade Point-to-Site do Mac OS X utilizando o cliente VPN nativo e o IKEv2. O cliente VPN em Mac para IKEv2 é muito básico e não permite muita personalização. Existem apenas quatro configurações que precisam de ser verificadas:

* Endereço do servidor
* ID remoto
* Local ID
* Definições de Autenticação
* Versão OS (10.11 ou superior)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Autenticação baseada em certificado de resolução de problemas
1. Verifique as definições do cliente VPN. Vá à **Definição** de Rede premindo comando + turno e, em seguida, digite "VPN" para verificar as definições do cliente VPN. Na lista, clique na entrada VPN que precisa de ser investigada.

   ![Autenticação baseada em certificados IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. O **ID local** deve ser o mesmo que o **sujeito** do certificado de cliente.
5. Clique em **Definições de Autenticação** para abrir a página Definições de Autenticação.

   ![Definições de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Verifique se o **Certificado** é selecionado a partir da entrega.
7. Clique no botão **Selecionar** e verifique se o certificado correto está selecionado. Clique em **OK** para guardar quaisquer alterações.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Nome de utilizador e autenticação de senha de resolução de problemas

1. Verifique as definições do cliente VPN. Vá à **Definição** de Rede premindo comando + turno e, em seguida, digite "VPN" para verificar as definições do cliente VPN. Na lista, clique na entrada VPN que precisa de ser investigada.

   ![Senha de nome de utilizador IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. A **identificação local** pode estar em branco.
5. Clique no botão Definição de **Autenticação** e verifique se o "Username" é selecionado a partir do dropdown.

   ![Definições de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Verifique se as credenciais corretas estão inseridas.

## <a name="additional-steps"></a><a name="additional"></a>Passos adicionais

Se experimentar os passos anteriores e tudo estiver configurado corretamente, baixe [o Wireshark](https://www.wireshark.org/#download) e execute uma captura de pacote.

1. Filtre em *isakmp* e veja os pacotes **IKE_SA.** Deverá poder analisar os detalhes da proposta da SA no âmbito da **Carga Útil: Associação**de Segurança . 
2. Verifique se o cliente e o servidor têm um conjunto comum.

   ![pacote](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Se não houver resposta do servidor nos vestígios da rede, verifique se ativou o protocolo IKEv2 na página de Configuração Do Portal Azure no site do portal Azure.

## <a name="next-steps"></a>Passos seguintes
Para obter ajuda adicional, consulte [o Suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)do Microsoft .
