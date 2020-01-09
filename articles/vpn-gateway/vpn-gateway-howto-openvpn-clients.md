---
title: Como configurar clientes OpenVPN para o gateway de VPN do Azure | Microsoft Docs
description: Etapas para configurar clientes OpenVPN para o gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: f78f416aaeedb2905cd77e94589121050757a202
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425686"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para o gateway de VPN do Azure

Este artigo ajuda você a configurar os clientes do **OpenVPN® Protocol** .

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verifique se você concluiu as etapas para configurar o OpenVPN para o gateway de VPN. Para obter detalhes, consulte [Configurar OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Clientes Windows

1. Baixe e instale o cliente do OpenVPN (versão 2,4 ou superior) no site oficial do [OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure ou ' New-AzVpnClientConfiguration ' no PowerShell.
3. Deszipe o perfil. Em seguida, abra o arquivo de configuração *vpnconfig. ovpn* na pasta OpenVPN usando o bloco de notas.
4. [Exporte](vpn-gateway-certificates-point-to-site.md#clientexport) o certificado de cliente P2S que você criou e carregou na configuração do P2S no gateway.
5. Extraia a chave privada e a impressão digital base64 do *. pfx*. Existem múltiplas formas de efetuar este procedimento. Usar o OpenSSL em seu computador é de uma maneira. O arquivo *ProfileInfo. txt* contém a chave privada e a impressão digital para a autoridade de certificação e o certificado do cliente. Certifique-se de usar a impressão digital do certificado do cliente.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Abra *ProfileInfo. txt* no bloco de notas. Para obter a impressão digital do certificado de cliente (filho), selecione o texto (incluindo e entre) "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" para o certificado filho e copie-o. Você pode identificar o certificado filho examinando o assunto =/linha.
7. Alterne para o arquivo *vpnconfig. ovpn* que você abriu no bloco de notas da etapa 3. Localize a seção mostrada abaixo e substitua tudo entre "CERT" e "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o *ProfileInfo. txt* no bloco de notas. Para obter a chave privada, selecione o texto (incluindo e entre) "-----iniciar a chave privada-----" e "-----END PRIVATE KEY-----" e copie-o.
9. Volte para o arquivo vpnconfig. ovpn no bloco de notas e localize esta seção. Cole a chave privada substituindo tudo entre "Key" e "/Key".

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

## <a name="mac"></a>Clientes Mac

1. Baixe e instale um cliente OpenVPN, como [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure ou usando ' New-AzVpnClientConfiguration ' no PowerShell.
3. Deszipe o perfil. Abra o arquivo de configuração vpnconfig. ovpn da pasta OpenVPN em um editor de texto.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Consulte [exportar a chave pública](vpn-gateway-certificates-point-to-site.md#cer) para obter informações sobre como exportar um certificado para obter a chave pública codificada.
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Consulte [exportar sua chave privada](https://openvpn.net/community-resources/how-to/#pki) para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7. Clique duas vezes no arquivo de perfil para criar o perfil em Tunnelblick.
8. Inicie o Tunnelblick na pasta aplicativos.
9. Clique no ícone de Tunnelblick na bandeja do sistema e selecione conectar.

> [!IMPORTANT]
>Somente o iOS 11,0 e superior e o MacOS 10,13 e superior têm suporte com o protocolo OpenVPN.
>
## <a name="iOS"></a>clientes iOS

1. Instale o cliente OpenVPN (versão 2,4 ou superior) na loja de aplicativos.
2. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure ou usando ' New-AzVpnClientConfiguration ' no PowerShell.
3. Deszipe o perfil. Abra o arquivo de configuração vpnconfig. ovpn da pasta OpenVPN em um editor de texto.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Consulte [exportar a chave pública](vpn-gateway-certificates-point-to-site.md#cer) para obter informações sobre como exportar um certificado para obter a chave pública codificada.
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Consulte [exportar sua chave privada](https://openvpn.net/community-resources/how-to/#pki) para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos.
7. Envie por email o arquivo de perfil (. ovpn) para sua conta de email configurada no aplicativo de email em seu iPhone. 
8. Abra o email no aplicativo de email no iPhone e toque no arquivo anexado

    ![Abrir email](./media/vpn-gateway-howto-openvpn-clients/ios2.png)

9. Toque em **mais** se você não vir **a opção Copiar para OpenVPN**

    ![Copiar para OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios3.png)

10. Toque em **copiar para OpenVPN** 

    ![Copiar para OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios4.png)

11. Toque em **Adicionar** na página **importar perfil**

    ![Copiar para OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios5.png)

12. Toque em **Adicionar** na página de **perfil importado**

    ![Copiar para OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios6.png)

13. Inicie o aplicativo OpenVPN e deslize a opção na página **perfil** diretamente para conectar

    ![Ligar](./media/vpn-gateway-howto-openvpn-clients/ios8.png)


## <a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de terminal. Você pode abrir uma nova sessão pressionando ' Ctrl + Alt + t ' ao mesmo tempo.
2. Digite o seguinte comando para instalar os componentes necessários:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure.
4. [Exporte](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) o certificado de cliente P2S que você criou e carregou na configuração do P2S no gateway. 
5. Extraia a chave privada e a impressão digital base64 do. pfx. Existem múltiplas formas de efetuar este procedimento. O uso do OpenSSL em seu computador é de uma maneira.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   O arquivo *ProfileInfo. txt* conterá a chave privada e a impressão digital para a autoridade de certificação e o certificado do cliente. Certifique-se de usar a impressão digital do certificado do cliente.

6. Abra *ProfileInfo. txt* em um editor de texto. Para obter a impressão digital do certificado de cliente (filho), selecione o texto que inclui e entre "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" para o certificado filho e copie-o. Você pode identificar o certificado filho examinando o assunto =/linha.

7. Abra o arquivo *vpnconfig. ovpn* e localize a seção mostrada abaixo. Substitua tudo entre a e "CERT" e "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o ProfileInfo. txt em um editor de texto. Para obter a chave privada, selecione o texto que inclui e entre "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copie-o.

9. Abra o arquivo vpnconfig. ovpn em um editor de texto e localize esta seção. Cole a chave privada substituindo tudo entre "Key" e "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
11. Para se conectar usando a linha de comando, digite o seguinte comando:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Para se conectar usando a GUI, vá para configurações do sistema.
13. Clique em **+** para adicionar uma nova conexão VPN.
14. Em **Adicionar VPN**, escolha **Importar do arquivo...**
15. Navegue até o arquivo de perfil e clique duas vezes ou selecione **abrir**.
16. Clique em **Adicionar** na janela **Adicionar VPN** .
  
    ![Importar do ficheiro](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Você pode se **conectar ativando a VPN na** página **configurações de rede** ou sob o ícone rede na bandeja do sistema.

## <a name="next-steps"></a>Passos seguintes

Se você quiser que os clientes VPN possam acessar recursos em outra VNet, siga as instruções no artigo de [vnet para vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma conexão de vnet para vnet. Certifique-se de habilitar o BGP nos gateways e nas conexões; caso contrário, o tráfego não fluirá.

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
