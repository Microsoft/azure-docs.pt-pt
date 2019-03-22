---
title: Como configurar clientes OpenVPN para o Gateway de VPN do Azure | Documentos da Microsoft
description: Passos para configurar clientes OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 1/15/2019
ms.author: cherylmc
ms.openlocfilehash: f7288202eb6dd66aee0bb38bb3611e8b319b50f2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010551"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Configurar clientes OpenVPN para o Gateway de VPN do Azure (pré-visualização)

Este artigo ajuda-o a configurar OpenVPN clientes.

> [!IMPORTANT]
> Esta Pré-visualização Pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Certifique-se de que concluiu os passos para configurar OpenVPN para o seu gateway VPN. Para obter detalhes, consulte [OpenVPN configurar para o Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Clientes do Windows

1. Baixe e instale o cliente de OpenVPN do official [OpenVPN site](https://openvpn.net/index.php/open-source/downloads.html).
2. Transfira o perfil VPN para o gateway. Isso pode ser feito a partir do separador de configuração de ponto a site no portal do Azure ou "New-AzVpnClientConfiguration" no PowerShell.
3. Deszipe o perfil. Em seguida, abra a *vpnconfig.ovpn* ficheiro de configuração a partir da pasta de OpenVPN usando o bloco de notas.
4. [Exportar](vpn-gateway-certificates-point-to-site.md#clientexport) o cliente de P2S de certificado criado e carregado para a configuração de P2S no gateway.
5. Extrair a chave privada e o thumbprint de base64 a partir da *. pfx*. Existem múltiplas formas de efetuar este procedimento. Com OpenSSL no seu computador é uma forma. O *profileinfo.txt* arquivo contém a chave privada e o thumbprint da AC e o certificado de cliente. Certifique-se de que utiliza o thumbprint do certificado de cliente.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Open *profileinfo.txt* no bloco de notas. Para obter o thumbprint do certificado de cliente (filho), selecione o texto (incluindo e entre) "---BEGIN CERTIFICATE---" e "---certificado END---" para o filho de certificado e copiá-lo. Pode identificar o certificado de subordinado ao observar o assunto = / linha.
7. Mude para o *vpnconfig.ovpn* arquivo aberto no bloco de notas do passo 3. A secção mostrada abaixo de localizar e Substituir tudo entre "cert" e "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o *profileinfo.txt* no bloco de notas. Para obter a chave privada, selecione o texto (incluindo e entre) "---BEGIN PRIVATE KEY---" e "---BEGIN PRIVATE KEY---" e copie-o.
9. Volte para o ficheiro de vpnconfig.ovpn no bloco de notas e encontrar nesta secção. Cole a chave privada Substituir tudo entre e a "chave" e "/ chave".

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

1. Transferir e instalar um cliente OpenVPN, tal como [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure ou com "New-AzVpnClientConfiguration" no PowerShell.
3. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn pasta OpenVPN no bloco de notas.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Ver [exportar a chave pública](vpn-gateway-certificates-point-to-site.md#cer) para obter informações sobre como exportar um certificado para obter a chave pública codificada.
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Ver [exportar a chave privada](https://openvpn.net/community-resources/how-to/#pki) para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7. Clique duas vezes o ficheiro de perfil para criar o perfil no tunnelblik.
8. Inicie Tunnelblik a partir da pasta de aplicativos.
9. Clique no ícone na Bandeja do sistema Tunnelblik e escolher ligar.

> [!IMPORTANT]
>Apenas iOS 11.0 e posterior e o MacOS 10.13 e superior, são suportados com o protocolo de OpenVPN.
>

## <a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de Terminal. Pode abrir uma nova sessão, pressionar "Ctrl + Alt + t" ao mesmo tempo.
2. Introduza o seguinte comando para instalar os componentes necessários:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia configuração ponto a site no portal do Azure.
4. [Exportar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) o cliente de P2S de certificado criado e carregado para a configuração de P2S no gateway. 
5. Extrair a chave privada e o thumbprint de base64 da. pfx. Existem múltiplas formas de efetuar este procedimento. Com OpenSSL no seu computador é uma forma.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   O *profileinfo.txt* ficheiro irá conter a chave privada e o thumbprint da AC e o certificado de cliente. Certifique-se de que utiliza o thumbprint do certificado de cliente.

6. Open *profileinfo.txt* num editor de texto. Para obter o thumbprint do certificado de cliente (filho), selecione o texto, inclusive e entre "---BEGIN certificado---" e "---certificado END---" para o filho de certificado e copiá-lo. Pode identificar o certificado de subordinado ao observar o assunto = / linha.

7. Abra o *vpnconfig.ovpn* de ficheiros e localize a secção mostrada abaixo. Substituir tudo entre a e "cert" e "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o profileinfo.txt num editor de texto. Para obter a chave privada, selecione o texto, inclusive e entre "---BEGIN chave privada---" e "---BEGIN PRIVATE KEY---" e copie-o.

9. Abra o ficheiro de vpnconfig.ovpn num editor de texto e encontrar nesta secção. Cole a chave privada Substituir tudo entre e a "chave" e "/ chave".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
11. Para ligar utilizando a linha de comandos, escreva o seguinte comando:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Para ligar através da GUI, aceda às definições de sistema.
13. Clique em **+** para adicionar uma nova ligação VPN.
14. Sob **adicionar VPN**, escolha **importar do ficheiro...**
15. Navegue para o ficheiro de perfil e clique duas vezes ou pick **aberto**.
16. Clique em **Add** sobre o **VPN adicionar** janela.
  
    ![Importar do ficheiro](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Pode ligar ao ativar a VPN **ON** no **as definições de rede** página, ou no ícone de rede na Bandeja do sistema.

## <a name="next-steps"></a>Passos Seguintes

Se pretender que os clientes VPN para conseguir aceder a recursos na outra vnet (produção), em seguida, siga as instruções sobre a [VNet a VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artigo para configurar uma ligação vnet a vnet. Certifique-se de que ativar o BGP nos gateways e as ligações, caso contrário, o tráfego não irá fluir.
