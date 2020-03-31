---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 921e22e637782ffd744af1a28e6bd43e7dd53c67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066169"
---
## <a name="windows-clients"></a><a name="windows"></a>Clientes windows

1. Descarregue e instale o cliente OpenVPN (versão 2.4 ou superior) a partir do site oficial da [OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configuração Ponto-a-site no portal Azure, ou 'New-AzVpnClientConfiguration' no PowerShell.
3. Deszipe o perfil. Em seguida, abra o ficheiro de configuração *vpnconfig.ovpn* da pasta OpenVPN utilizando o Bloco de Notas.
4. Exporte o certificado de cliente ponto-a-site que criou e carregou para a sua configuração P2S no gateway. Utilize as seguintes ligações de artigo:

   * [Instruções vpn Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Instruções [virtuais wan](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrair a chave privada e a impressão digital base64 do *.pfx*. Existem múltiplas formas de efetuar este procedimento. Usar o OpenSSL na sua máquina é de uma maneira. O ficheiro *profileinfo.txt* contém a chave privada e a impressão digital para o certificado CA e cliente. Certifique-se de usar a impressão digital do certificado de cliente.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Abra *profileinfo.txt* no Bloco de Notas. Para obter a impressão digital do certificado do cliente (criança), selecione o texto (incluindo e entre)"-----CERTIFICADOBEGIN-----" e "-----END CERTIFICATE-----" para o certificado de criança e copie-o. Pode identificar o certificado de criança olhando para o sujeito=/ linha.
7. Mude para o ficheiro *vpnconfig.ovpn* que abriu no Bloco de Notas a partir do passo 3. Encontre a secção abaixo e substitua tudo entre "cert" e "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o *profileinfo.txt* no Bloco de Notas. Para obter a chave privada, selecione o texto (incluindo e entre) "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copie-o.
9. Volte ao ficheiro vpnconfig.ovpn no Bloco de Notas e encontre esta secção. Colar a chave privada substituindo tudo entre e "chave" e "/chave".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
11. Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
12. Clique com o botão direito do rato no ícone OpenVPN no tabuleiro do sistema e clique em Ligar.

## <a name="mac-clients"></a><a name="mac"></a>Clientes Mac

1. Descarregue e instale um cliente OpenVPN, como [tunnelBlick](https://tunnelblick.net/downloads.html). 
2. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configuração ponto-a-site no portal Azure, ou utilizando 'New-AzVpnClientConfiguration' no PowerShell.
3. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn da pasta OpenVPN num editor de texto.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Utilize os seguintes links de artigo para obter informações sobre como exportar um certificado para obter a chave pública codificada:

   * [Instruções vpn Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instruções [virtuais wan](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Consulte a [Exportação](https://openvpn.net/community-resources/how-to/#pki) da sua chave privada no site openVPN para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7. Clique duas vezes no ficheiro de perfil para criar o perfil em Tunnelblick.
8. Lance tunnelblick a partir da pasta de aplicações.
9. Clique no ícone Tunnelblick na bandeja do sistema e escolha a ligação.

> [!IMPORTANT]
>Apenas o iOS 11.0 e superior e o MacOS 10.13 ou superior são suportados com o protocolo OpenVPN.
>
## <a name="ios-clients"></a><a name="iOS"></a>clientes iOS

1. Instale o cliente OpenVPN (versão 2.4 ou superior) na loja da App.
2. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configuração ponto-a-site no portal Azure, ou utilizando 'New-AzVpnClientConfiguration' no PowerShell.
3. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn da pasta OpenVPN num editor de texto.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Utilize os seguintes links de artigo para obter informações sobre como exportar um certificado para obter a chave pública codificada:

   * [Instruções vpn Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instruções [virtuais wan](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Consulte [Exportar a sua chave privada](https://openvpn.net/community-resources/how-to/#pki) no site openVPN para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos.
7. Envie por e-mail o ficheiro de perfil (.ovpn) para a sua conta de e-mail que está configurada na aplicação de correio no seu iPhone. 
8. Abra o e-mail na aplicação de correio no iPhone e toque no ficheiro anexo

    ![E-mail aberto](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Toque em **Mais** se não vir a opção **Copy to OpenVPN**

    ![Mais](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Toque em **Copiar para OpenVPN** 

    ![Cópia para OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Toque em **ADD** na página perfil de **importação**

    ![Adicionar](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Toque em **ADD** na página **perfil importado**

    ![Toque EM ADICIONAR](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Lance a aplicação OpenVPN e deslize o interruptor na página **perfil** direito de ligar

    ![Ligar](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de Terminal. Pode abrir uma nova sessão premindo 'Ctrl + Alt + t' ao mesmo tempo.
2. Introduza o seguinte comando para instalar os componentes necessários:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configuração Ponto-a-local no portal Azure.
4. Exporte o certificado de cliente P2S que criou e carregou para a sua configuração P2S no gateway. Utilize as seguintes ligações de artigo:

   * [Instruções vpn Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Instruções [virtuais wan](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extraia a chave privada e a impressão digital base64 do .pfx. Existem múltiplas formas de efetuar este procedimento. Usar o OpenSSL no computador é uma maneira.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   O ficheiro *profileinfo.txt* conterá a chave privada e a impressão digital para a AC e o certificado cliente. Certifique-se de usar a impressão digital do certificado de cliente.

6. Abra *profileinfo.txt* num editor de texto. Para obter a impressão digital do certificado do cliente (criança), selecione o texto incluindo e entre "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" para o certificado de criança e copie-o. Pode identificar o certificado de criança olhando para o sujeito=/ linha.

7. Abra o ficheiro *vpnconfig.ovpn* e encontre a secção abaixo mostrada. Substitua tudo entre o e o "cert" e o "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o profileinfo.txt num editor de texto. Para obter a chave privada, selecione o texto incluindo e entre "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copie-o.

9. Abra o ficheiro vpnconfig.ovpn num editor de texto e encontre esta secção. Colar a chave privada substituindo tudo entre e "chave" e "/chave".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
11. Para ligar utilizando a linha de comando, escreva o seguinte comando:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Para ligar o GUI, vá às definições do sistema.
13. Clique **+** para adicionar uma nova ligação VPN.
14. Em **Add VPN,** escolha **Import from file...**
15. Navegue no ficheiro de perfil e clique duas vezes ou escolha **Abrir**.
16. Clique em **Adicionar** a janela **VPN.**
  
    ![Importar do ficheiro](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Pode ligar-se ligando a VPN **ON** na página **Definições** de Rede ou sob o ícone da rede na bandeja do sistema.