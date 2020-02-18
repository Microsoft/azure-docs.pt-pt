---
title: 'Azure VPN Gateway: Ligações ponto-a-site: clientes Mac OS X'
description: Passos para resolver problemas de ligações de cliente de P2S Mac OS X VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425731"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Resolver problemas de ligações de VPN Point-to-Site de clientes de VPN do Mac OS X

Este artigo ajuda-o a resolver problemas de conectividade de ponto a Site em Mac OS X com o cliente VPN nativo e IKEv2. O cliente VPN no Mac para IKEv2 é bastante básico e não permite uma personalização muito. Existem apenas quatro definições que precisam ser verificados:

* Endereço do Servidor
* ID remoto
* ID do local
* Definições de autenticação
* Versão do SO (10.11 ou superior)


## <a name="VPNClient"></a>Autenticação baseada em certificado de resolução de problemas
1. Verifique as definições de cliente VPN. Vá à **Definição** de Rede premindo comando + turno e, em seguida, digite "VPN" para verificar as definições do cliente VPN. Na lista, clique na entrada VPN que tem de ser investigado.

   ![Autenticação baseada em certificado do IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. O **ID local** deve ser o mesmo que o **sujeito** do certificado de cliente.
5. Clique em **Definições de Autenticação** para abrir a página Definições de Autenticação.

   ![definições de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Verifique se o **Certificado** é selecionado a partir da entrega.
7. Clique no botão **Selecionar** e verifique se o certificado correto está selecionado. Clique em **OK** para guardar quaisquer alterações.

## <a name="ikev2"></a>Nome de utilizador e autenticação de senha de resolução de problemas

1. Verifique as definições de cliente VPN. Vá à **Definição** de Rede premindo comando + turno e, em seguida, digite "VPN" para verificar as definições do cliente VPN. Na lista, clique na entrada VPN que tem de ser investigado.

   ![Palavra-passe de nome de utilizador de IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. A **identificação local** pode estar em branco.
5. Clique no botão Definição de **Autenticação** e verifique se o "Username" é selecionado a partir do dropdown.

   ![definições de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Certifique-se de que as credenciais corretas são introduzidas.

## <a name="additional"></a>Passos adicionais

Se experimentar os passos anteriores e tudo estiver configurado corretamente, baixe [o Wireshark](https://www.wireshark.org/#download) e execute uma captura de pacote.

1. Filtre em *isakmp* e veja os pacotes **IKE_SA.** Deverá poder analisar os detalhes da proposta da SA no âmbito da **Carga Útil: Associação**de Segurança . 
2. Certifique-se de que o cliente e o servidor tem um conjunto comum.

   ![pacote](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Se não houver resposta do servidor nos vestígios da rede, verifique se ativou o protocolo IKEv2 na página de Configuração Do Portal Azure no site do portal Azure.

## <a name="next-steps"></a>Passos seguintes
Para obter ajuda adicional, consulte [o Suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)do Microsoft .
