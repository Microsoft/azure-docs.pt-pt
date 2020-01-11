---
title: Solucionar problemas de conexão de ponto a site do Azure
titleSuffix: Azure VPN Gateway
description: Saiba como solucionar problemas de conexão de ponto a site.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.author: genli
ms.openlocfilehash: 2c5e8b344cad6928ee586dc5a5b69095f0b14552
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863653"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Solução de problemas: problemas de conexão de ponto a site do Azure

Este artigo lista os problemas comuns de conexão de ponto a site que você pode enfrentar. Ele também aborda as possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Ao tentar se conectar a uma rede virtual do Azure usando o cliente VPN, você receberá a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que possa ser usado com esse protocolo de autenticação extensível. (Erro 798)**

### <a name="cause"></a>Causa

Esse problema ocorre se o certificado do cliente estiver ausente dos **certificados-User\Personal\Certificates atual**.

### <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Abra o Gerenciador de certificados: clique em **Iniciar**, digite **gerenciar certificados de computador**e clique em **gerenciar certificados de computador** no resultado da pesquisa.

2. Verifique se os seguintes certificados estão no local correto:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | AzureRoot. cer    | Autoridades de certificação raiz computador locais|

3. Vá para C:\Users\<UserName > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, instale manualmente o certificado (arquivo *. cer) no repositório do usuário e do computador.

Para obter mais informações sobre como instalar o certificado do cliente, consulte [gerar e exportar certificados para conexões ponto a site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Ao importar o certificado do cliente, não selecione a opção **habilitar proteção de chave privada forte** .

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>A conexão de rede entre o computador e o servidor VPN não pôde ser estabelecida porque o servidor remoto não está respondendo

### <a name="symptom"></a>Sintoma

Ao tentar e conectar-se a um gateway de rede virtual do Azure usando IKEv2 no Windows, você obtém a seguinte mensagem de erro:

**A conexão de rede entre o computador e o servidor VPN não pôde ser estabelecida porque o servidor remoto não está respondendo**

### <a name="cause"></a>Causa
 
 O problema ocorre se a versão do Windows não tiver suporte para fragmentação de IKE
 
### <a name="solution"></a>Solução

O IKEv2 é suportado no Windows 10 e Windows Server 2016. No entanto, para poder utilizar o IKEv2, tem de instalar as atualizações e definir uma chave de registo localmente. As versões de SO anteriores ao Windows 10 não são suportadas e só podem utilizar o SSTP.

Para preparar o Windows 10 ou o Windows Server 2016 para o IKEv2:

1. Instale a atualização.

   | Versão do SO | Data | Número/Ligação |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, Versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versão 1709 | 22 de março de 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Defina o valor da chave de registo. Crie ou defina `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` chave de REG_DWORD no registro como 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: a mensagem recebida era inesperada ou formatada incorretamente

### <a name="symptom"></a>Sintoma

Ao tentar se conectar a uma rede virtual do Azure usando o cliente VPN, você receberá a seguinte mensagem de erro:

**A mensagem recebida era inesperada ou formatada incorretamente. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Esse problema ocorre se uma das seguintes condições for verdadeira:

- O uso de rotas definidas pelo usuário (UDR) com rota padrão na sub-rede de gateway é definido incorretamente.
- A chave pública do certificado raiz não é carregada no gateway de VPN do Azure. 
- A chave está corrompida ou expirou.

### <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Remova UDR na sub-rede de gateway. Certifique-se de que UDR encaminhe todo o tráfego corretamente.
2. Verifique o status do certificado raiz no portal do Azure para ver se ele foi revogado. Se não for revogado, tente excluir o certificado raiz e recarregar. Para obter mais informações, consulte [criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: uma cadeia de certificados foi processada, mas terminada 

### <a name="symptom"></a>Sintoma 

Ao tentar se conectar a uma rede virtual do Azure usando o cliente VPN, você receberá a seguinte mensagem de erro:

**Uma cadeia de certificados foi processada, mas terminada em um certificado raiz que não é confiável pelo provedor de confiança.**

### <a name="solution"></a>Solução

1. Verifique se os seguintes certificados estão no local correto:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | Azuregateway-*GUID*.cloudapp.net  | Autoridades de certificação raiz User\Trusted atuais|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Autoridades de certificação raiz computador locais|

2. Se os certificados já estiverem no local, tente excluir os certificados e reinstalá-los. O certificado **azuregateway-*GUID*. cloudapp.net** está no pacote de configuração de cliente VPN que você baixou do portal do Azure. Você pode usar arquivadores de arquivos para extrair os arquivos do pacote.

## <a name="file-download-error-target-uri-is-not-specified"></a>Erro de download do arquivo: o URI de destino não foi especificado

### <a name="symptom"></a>Sintoma

Você receberá a seguinte mensagem de erro:

**Erro de download do arquivo. O URI de destino não foi especificado.**

### <a name="cause"></a>Causa 

Esse problema ocorre devido a um tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway de VPN deve ser **VPN**e o tipo de VPN deve ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Erro de cliente VPN: falha no script personalizado de VPN do Azure 

### <a name="symptom"></a>Sintoma

Ao tentar se conectar a uma rede virtual do Azure usando o cliente VPN, você receberá a seguinte mensagem de erro:

**Falha no script personalizado (para atualizar sua tabela de roteamento). (Erro 8007026f)**

### <a name="cause"></a>Causa

Esse problema pode ocorrer se você estiver tentando abrir a conexão VPN site a ponto usando um atalho.

### <a name="solution"></a>Solução 

Abra o pacote de VPN diretamente em vez de abri-lo a partir do atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

Um certificado adicional é necessário para confiar no gateway de VPN para sua rede virtual. O certificado é incluído no pacote de configuração do cliente VPN que é gerado a partir do portal do Azure.

### <a name="solution"></a>Solução

Extraia o pacote de configuração de cliente VPN e localize o arquivo. cer. Para instalar o certificado, siga estas etapas:

1. Abra o MMC. exe.
2. Adicione o snap-in de **certificados** .
3. Selecione a conta de **computador** do computador local.
4. Clique com o botão direito do mouse no nó **autoridades de certificação raiz confiáveis** . Clique em **tudo-tarefa** > **importar**e navegue até o arquivo. cer extraído do pacote de configuração do cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro de portal do Azure: falha ao salvar o gateway de VPN e os dados são inválidos

### <a name="symptom"></a>Sintoma

Ao tentar salvar as alterações para o gateway de VPN no portal do Azure, você receberá a seguinte mensagem de erro:

**Falha ao salvar o gateway de rede virtual &lt;*nome do gateway*&gt;. Os dados da *ID do certificado* de &lt;de certificado&gt; são inválidos.**

### <a name="cause"></a>Causa 

Esse problema pode ocorrer se a chave pública do certificado raiz que você carregou contiver um caractere inválido, como um espaço.

### <a name="solution"></a>Solução

Verifique se os dados no certificado não contêm caracteres inválidos, como quebras de linha (retornos de carro). O valor inteiro deve ser uma linha longa. O texto a seguir é um exemplo do certificado:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro de portal do Azure: falha ao salvar o gateway de VPN e o nome do recurso é inválido

### <a name="symptom"></a>Sintoma

Ao tentar salvar as alterações para o gateway de VPN no portal do Azure, você receberá a seguinte mensagem de erro: 

**Falha ao salvar o gateway de rede virtual &lt;*nome do gateway*&gt;. O nome do recurso &lt;*nome do certificado que você tentar carregar*&gt; é inválido**.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do certificado contém um caractere inválido, como um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro de portal do Azure: erro de download do arquivo do pacote VPN 503

### <a name="symptom"></a>Sintoma

Ao tentar baixar o pacote de configuração de cliente VPN, você receberá a seguinte mensagem de erro:

**Falha ao baixar o arquivo. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Esse erro pode ser causado por um problema de rede temporário. Tente baixar o pacote de VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Atualização do gateway de VPN do Azure: todos os clientes ponto a site não podem se conectar

### <a name="cause"></a>Causa

Se o certificado tiver mais de 50% em seu tempo de vida, o certificado será substituído.

### <a name="solution"></a>Solução

Para resolver esse problema, Baixe novamente e reimplante o pacote ponto a site em todos os clientes.

## <a name="too-many-vpn-clients-connected-at-once"></a>Muitos clientes VPN conectados ao mesmo tempo

O número máximo de conexões permitidas é atingido. Você pode ver o número total de clientes conectados no portal do Azure.

## <a name="vpn-client-cannot-access-network-file-shares"></a>O cliente VPN não pode acessar compartilhamentos de arquivos de rede

### <a name="symptom"></a>Sintoma

O cliente VPN se conectou à rede virtual do Azure. No entanto, o cliente não pode acessar os compartilhamentos de rede.

### <a name="cause"></a>Causa

O protocolo SMB é usado para acesso ao compartilhamento de arquivos. Quando a conexão é iniciada, o cliente VPN adiciona as credenciais da sessão e a falha ocorre. Depois que a conexão é estabelecida, o cliente é forçado a usar as credenciais de cache para a autenticação Kerberos. Esse processo inicia consultas para o centro de distribuição de chaves (um controlador de domínio) para obter um token. Como o cliente se conecta pela Internet, talvez não consiga acessar o controlador de domínio. Portanto, o cliente não pode fazer failover do Kerberos para NTLM. 

A única vez em que o cliente é solicitado a fornecer uma credencial é quando ele tem um certificado válido (com SAN = UPN) emitido pelo domínio ao qual ele está associado. O cliente também deve estar fisicamente conectado à rede de domínio. Nesse caso, o cliente tenta usar o certificado e chega ao controlador de domínio. Em seguida, o centro de distribuição de chaves retorna um erro "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a fazer failover para NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desabilite o cache de credenciais de domínio da seguinte subchave do registro: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível encontrar a conexão VPN ponto a site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Você remove a conexão VPN ponto a site e, em seguida, reinstala o cliente VPN. Nessa situação, a conexão VPN não está configurada com êxito. Você não vê a conexão VPN nas configurações de **conexões de rede** no Windows.

### <a name="solution"></a>Solução

Para resolver o problema, exclua os arquivos de configuração de cliente VPN antigos do **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >** e execute o instalador do cliente VPN novamente.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>O cliente VPN ponto a site não pode resolver o FQDN dos recursos no domínio local

### <a name="symptom"></a>Sintoma

Quando o cliente se conecta ao Azure usando a conexão VPN ponto a site, ele não pode resolver o FQDN dos recursos em seu domínio local.

### <a name="cause"></a>Causa

O cliente VPN ponto a site usa servidores DNS do Azure configurados na rede virtual do Azure. Os servidores DNS do Azure têm precedência sobre os servidores DNS locais configurados no cliente, portanto, todas as consultas DNS são enviadas para os servidores DNS do Azure. Se os servidores DNS do Azure não tiverem os registros para os recursos locais, a consulta falhará.

### <a name="solution"></a>Solução

Para resolver o problema, verifique se os servidores DNS do Azure usados na rede virtual do Azure podem resolver os registros DNS para recursos locais. Para fazer isso, você pode usar encaminhadores DNS ou encaminhadores condicionais. Para obter mais informações, consulte [resolução de nomes usando seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A conexão VPN ponto a site é estabelecida, mas você ainda não pode se conectar aos recursos do Azure 

### <a name="cause"></a>Causa

Esse problema pode ocorrer se o cliente VPN não obtiver as rotas do gateway de VPN do Azure.

### <a name="solution"></a>Solução

Para resolver esse problema, [redefina o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md). Para certificar-se de que as novas rotas estão sendo usadas, os clientes VPN ponto a site devem ser baixados novamente depois que o emparelhamento de rede virtual tiver sido configurado com êxito.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Erro: "a função de revogação não pôde verificar a revogação porque o servidor de revogação estava offline. (Erro 0x80092013) "

### <a name="causes"></a>Causas
Essa mensagem de erro ocorrerá se o cliente não puder acessar http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.  A verificação de revogação requer acesso a esses dois sites.  Esse problema normalmente ocorre no cliente que tem o servidor proxy configurado. Em alguns ambientes, se as solicitações não passarem pelo servidor proxy, elas serão negadas no firewall do Edge.

### <a name="solution"></a>Solução

Verifique as configurações do servidor proxy, certifique-se de que o cliente possa acessar http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Erro de cliente VPN: a conexão foi impedida devido a uma política configurada no servidor RAS/VPN. (Erro 812)

### <a name="cause"></a>Causa

Esse erro ocorre se o servidor RADIUS que você usou para autenticar o cliente VPN tiver configurações incorretas ou o gateway do Azure não puder acessar o servidor RADIUS.

### <a name="solution"></a>Solução

Verifique se o servidor RADIUS está configurado corretamente. Para obter mais informações, consulte [integrar a autenticação RADIUS com o Azure servidor de autenticação multifator](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Erro 405" ao baixar o certificado raiz do gateway de VPN

### <a name="cause"></a>Causa

O certificado raiz não foi instalado. O certificado raiz é instalado no repositório de **certificados confiáveis** do cliente.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Erro de cliente VPN: a conexão remota não foi feita porque houve falha na tentativa de túneis VPN. (Erro 800) 

### <a name="cause"></a>Causa

O driver NIC está desatualizado.

### <a name="solution"></a>Solução

Atualize o driver NIC:

1. Clique em **Iniciar**, digite **Device Manager**e selecione-o na lista de resultados. Se lhe for pedida uma palavra-passe de administrador ou uma confirmação, escreva a palavra-passe ou confirme.
2. Nas categorias **adaptadores de rede** , localize a NIC que você deseja atualizar.  
3. Clique duas vezes no nome do dispositivo, selecione **Atualizar driver**e selecione **Pesquisar automaticamente software de driver atualizado**.
4. Se o Windows não localizar um novo controlador, pode experimentar procurar um no site do fabricante do dispositivo e seguir as suas instruções.
5. Reinicie o computador e tente a conexão novamente.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Erro: ' o URI de destino do erro de download do arquivo não foi especificado '

### <a name="cause"></a>Causa

Isso é causado por um tipo de gateway incorreto configurado.

### <a name="solution"></a>Solução

O tipo de gateway de VPN do Azure deve ser VPN e o tipo de VPN deve ser **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>O instalador do pacote VPN não foi concluído

### <a name="cause"></a>Causa

Esse problema pode ser causado pelas instalações anteriores do cliente VPN. 

### <a name="solution"></a>Solução

Exclua os arquivos de configuração de cliente VPN antigos de **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >** e execute o instalador do cliente VPN novamente. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>O cliente VPN hiberna ou suspende após algum tempo

### <a name="solution"></a>Solução

Verifique as configurações de suspensão e hibernação no computador em que o cliente VPN está sendo executado.
