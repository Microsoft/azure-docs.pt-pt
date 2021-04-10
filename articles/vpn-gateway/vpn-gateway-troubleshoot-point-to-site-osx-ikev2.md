---
title: 'Azure VPN Gateway: Ligações ponto-a-local de resolução de problemas: clientes Mac OS X'
description: Saiba como resolver problemas de conectividade ponto a local a partir do Mac OS X utilizando o cliente nativo VPN e iKEv2.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 4c8657f8a40084a726280efe471c0791931294f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366641"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Resolução de problemas Ligações VPN ponto-a-local de clientes Mac OS X VPN

Este artigo ajuda-o a resolver problemas de conectividade ponto-a-local do Mac OS X utilizando o cliente VPN nativo e o IKEv2. O cliente VPN em Mac para iKEv2 é muito básico e não permite muita personalização. Há apenas quatro configurações que precisam de ser verificadas:

* Endereço do servidor
* ID remoto
* Local ID
* Definições de Autenticação
* Versão OS (10.11 ou superior)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Autenticação baseada em certificados de resolução de problemas
1. Verifique as definições do cliente VPN. Aceda à **Definição** de Rede pressionando o Comando + Shift e, em seguida, escreva "VPN" para verificar as definições do cliente VPN. A partir da lista, clique na entrada VPN que precisa de ser investigada.

   ![Autenticação baseada em certificados IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. A **Identificação Local** deve ser a mesma que o **Objeto** do certificado de cliente.
5. Clique em **Definições de Autenticação** para abrir a página Definições de Autenticação.

   ![A screenshot mostra uma caixa de diálogo de definições de autenticação com certificado selecionado.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Verifique se o **Certificado** é selecionado a partir do dropdown.
7. Clique no botão **Selecione** e verifique se o certificado correto está selecionado. Clique **em OK** para guardar quaisquer alterações.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Nome de utilizador de resolução de problemas e autenticação de senha

1. Verifique as definições do cliente VPN. Aceda à **Definição** de Rede pressionando o Comando + Shift e, em seguida, escreva "VPN" para verificar as definições do cliente VPN. A partir da lista, clique na entrada VPN que precisa de ser investigada.

   ![Senha de nome de utilizador IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. O **ID remoto** deve ser o mesmo que o Endereço do Servidor (Gateway FQDN).
4. A **identificação local** pode estar em branco.
5. Clique no botão **Definição de Autenticação** e verifique se o "Nome de Utilizador" é selecionado a partir do dropdown.

   ![A screenshot mostra uma caixa de diálogo de definições de autenticação com o nome de utilizador selecionado.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Verifique se as credenciais corretas estão inseridas.

## <a name="additional-steps"></a><a name="additional"></a>Passos adicionais

Se experimentar os passos anteriores e tudo estiver configurado corretamente, descarregue [o Wireshark](https://www.wireshark.org/#download) e realize uma captura de pacotes.

1. Filtre no *isakmp* e olhe para os pacotes **de IKE_SA.** Você deve ser capaz de olhar para os detalhes da proposta SA no âmbito da **Payload: Security Association**. 
2. Verifique se o cliente e o servidor têm um conjunto comum.

   ![pacote](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Se não houver resposta do servidor nos vestígios de rede, verifique se ativou o protocolo IKEv2 na página de Configuração do Gateway Azure no website do portal Azure.

## <a name="next-steps"></a>Passos seguintes
Para obter ajuda adicional, consulte [o Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
