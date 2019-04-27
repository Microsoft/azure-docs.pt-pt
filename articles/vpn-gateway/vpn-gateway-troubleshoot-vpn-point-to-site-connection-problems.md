---
title: Resolver problemas de ligação de ponto a site do Azure | Documentos da Microsoft
description: Saiba como resolver problemas de ligação de ponto a site.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2018
ms.author: genli
ms.openlocfilehash: 174bc4895bbad4546392581c2c769aac762d6106
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60457481"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Resolução de problemas: Problemas de ligação de ponto a site do Azure

Este artigo lista problemas de ligação de ponto a site comuns que podem ocorrer. Ele também aborda possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: Não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure com o cliente VPN, receberá a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que podem ser utilizadas com este protocolo de autenticação extensível. (Erro 798)**

### <a name="cause"></a>Causa

Este problema ocorre se o certificado de cliente está em falta **Certificates - user\personal\certificates ' atual**.

### <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Abra o Gestor de certificados: Clique em **começar**, tipo **gerir os certificados de computador**e, em seguida, clique em **gerir certificados de computador** no resultado da pesquisa.

2. Certifique-se de que os seguintes certificados estão no local correto:

    | Certificado | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | Azuregateway-*GUID*.cloudapp.net  | Autoridades de certificação de raiz User\Trusted atual|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Autoridades de certificação de raiz de local Computer\Trusted|

3. Aceda a C:\Users\<nome de utilizador > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, manualmente instalar o certificado (ficheiro *. cer) no armazenamento do computador e o utilizador.

Para obter mais informações sobre como instalar o certificado de cliente, consulte [gerar e exportar certificados para ligações ponto a site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Ao importar o certificado de cliente, não selecione a **ativar a proteção forte por chave privada** opção.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Não foi possível estabelecer a ligação de rede entre o computador e o servidor VPN porque o servidor remoto não está a responder

### <a name="symptom"></a>Sintoma

Quando tenta e ligar a um gteway de rede virtual do Azure com o IKEv2 no Windows, obterá a seguinte mensagem de erro:

**Não foi possível estabelecer a ligação de rede entre o computador e o servidor VPN porque o servidor remoto não está a responder**

### <a name="cause"></a>Causa
 
 O problema ocorre se a versão do Windows não tem suporte para a fragmentação do IKE
 
### <a name="solution"></a>Solução

O IKEv2 é suportado no Windows 10 e Windows Server 2016. No entanto, para poder utilizar o IKEv2, tem de instalar as atualizações e definir uma chave de registo localmente. As versões de SO anteriores ao Windows 10 não são suportadas e só podem utilizar o SSTP.

Para preparar o Windows 10 ou o Windows Server 2016 para o IKEv2:

1. Instale a atualização.

   | Versão do SO | Date | Número/Ligação |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, Versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versão 1709 | 22 de Março de 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Defina o valor da chave de registo. Crie ou defina a chave "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload” REG_DWORD no registo como 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: A mensagem recebida era inesperada ou formatado incorretamente

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure com o cliente VPN, receberá a seguinte mensagem de erro:

**A mensagem recebida era inesperada ou incorretamente formatado. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Este problema ocorre se uma das seguintes condições for verdadeira:

- As utilização definido pelo utilizador rotas (UDR com a rota predefinida na sub-rede de Gateway) é definida incorretamente.
- A chave pública do certificado de raiz não é carregada para o gateway de VPN do Azure. 
- A chave está danificada ou expirou.

### <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Remova o UDR na sub-rede de Gateway. Certifique-se de que UDR encaminha todo o tráfego corretamente.
2. Verificar o estado do certificado de raiz no portal do Azure para ver se ele foi revogado. Se não tiver sido revogado, tente eliminar o certificado de raiz e reupload. Para obter mais informações, consulte [criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: Uma cadeia de certificados processado, mas terminada 

### <a name="symptom"></a>Sintoma 

Ao tentar ligar a uma rede virtual do Azure com o cliente VPN, receberá a seguinte mensagem de erro:

**Uma cadeia de certificados processado, mas encerrado de um certificado de raiz que não é considerado fidedigno pelo fornecedor de confiança.**

### <a name="solution"></a>Solução

1. Certifique-se de que os seguintes certificados estão no local correto:

    | Certificado | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | Azuregateway-*GUID*.cloudapp.net  | Autoridades de certificação de raiz User\Trusted atual|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Autoridades de certificação de raiz de local Computer\Trusted|

2. Se os certificados já estão na localização, tente eliminar os certificados e reinstalá-los. O **azuregateway -*GUID*. cloudapp.net** certificado está no pacote de configuração de cliente VPN que transferiu a partir do portal do Azure. Pode usar archivers de ficheiro para extrair os ficheiros do pacote.

## <a name="file-download-error-target-uri-is-not-specified"></a>Erro de transferência de ficheiro: URI de destino não especificado

### <a name="symptom"></a>Sintoma

Receberá a seguinte mensagem de erro:

**Erro de transferência de ficheiros. URI de destino não está especificado.**

### <a name="cause"></a>Causa 

Este problema ocorre devido a um tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway VPN tem de ser **VPN**, e o tipo de VPN tem de ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Erro de cliente VPN: Script personalizado de VPN do Azure falhou 

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure com o cliente VPN, receberá a seguinte mensagem de erro:

**Falha de script personalizado (para atualizar a sua tabela de roteamento). (Erro 8007026f)**

### <a name="cause"></a>Causa

Esse problema pode ocorrer se estiver a tentar abrir a ligação de VPN ponto a site com um atalho.

### <a name="solution"></a>Solução 

Abra o pacote VPN diretamente em vez de abri-lo a partir do atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

Um certificado adicional é necessário para o gateway de VPN da rede virtual de confiança. O certificado está incluído no pacote de configuração de cliente VPN que é gerado a partir do portal do Azure.

### <a name="solution"></a>Solução

Extrair o pacote de configuração de cliente VPN e localize o ficheiro. cer. Para instalar o certificado, siga estes passos:

1. Abra mmc.exe.
2. Adicionar a **certificados** snap-in.
3. Selecione o **computador** de conta para o computador local.
4. Com o botão direito a **autoridades de certificação de raiz fidedigna** nó. Clique em **todas as tarefas** > **importação**e procure o ficheiro. cer extraído do pacote de configuração de cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro de portal do Azure: Falha ao guardar o gateway de VPN e os dados são inválidos

### <a name="symptom"></a>Sintoma

Quando tentar guardar as alterações para o gateway VPN no portal do Azure, recebe a seguinte mensagem de erro:

**Falha ao guardar o gateway de rede virtual &lt; *nome do gateway*&gt;. Dados do certificado &lt; *ID de certificado* &gt; é inválido.**

### <a name="cause"></a>Causa 

Esse problema pode ocorrer se a chave pública o certificado de raiz que carregou contiver um caráter inválido, por exemplo, um espaço.

### <a name="solution"></a>Solução

Certifique-se de que os dados no certificado não contém carateres inválidos, tais como quebras de linha (retornos de carro). O valor completo deve ser uma linha muito longa. O texto a seguir está um exemplo do certificado:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro de portal do Azure: Falha ao guardar o gateway de VPN e o nome de recurso é inválido

### <a name="symptom"></a>Sintoma

Quando tentar guardar as alterações para o gateway VPN no portal do Azure, recebe a seguinte mensagem de erro: 

**Falha ao guardar o gateway de rede virtual &lt; *nome do gateway*&gt;. Nome do recurso &lt; *tentar carregar o nome do certificado* &gt; é inválido**.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome do certificado contém um caráter inválido, por exemplo, um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro de portal do Azure: Erro de transferência de ficheiros de pacotes VPN 503

### <a name="symptom"></a>Sintoma

Ao tentar transferir o pacote de configuração de cliente VPN, receberá a seguinte mensagem de erro:

**Falha ao transferir o ficheiro. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Este erro pode ser causado por um problema de rede temporário. Tente transferir o pacote de VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Atualização de Gateway de VPN do Azure: Todos os ponto a Site os clientes não conseguem ligar

### <a name="cause"></a>Causa

Se o certificado for mais de 50 por cento por meio de sua vida, será transferido para o certificado.

### <a name="solution"></a>Solução

Para resolver este problema, transfira novamente e voltar a implementar o ponto de pacote de Site em todos os clientes.

## <a name="too-many-vpn-clients-connected-at-once"></a>Muitos clientes VPN ligado ao mesmo tempo

Foi atingido o número máximo de conexões permitidas. Pode ver o número total de clientes ligados no portal do Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>VPN Point-to-site incorretamente adiciona uma rota para 10.0.0.0/8 à tabela de rotas

### <a name="symptom"></a>Sintoma

Quando marcar a ligação de VPN no cliente ponto a site, o cliente VPN deve adicionar uma rota para a rede virtual do Azure. O serviço de programa auxiliar IP deve adicionar uma rota para a sub-rede de clientes VPN. 

O intervalo de cliente VPN pertence a uma sub-rede mais pequena do 10.0.0.0/8, tais como 10.0.12.0/24. Em vez de uma rota para 10.0.12.0/24, é adicionada uma rota para 10.0.0.0/8 que tem a prioridade mais alta. 

Esta rota incorreta quebras de conectividade com outras redes de locais que talvez pertencem a outra sub-rede dentro do intervalo de 10.0.0.0/8, tais como 10.50.0.0/24, que não têm uma rota específica definida. 

### <a name="cause"></a>Causa

Este comportamento é predefinido para clientes do Windows. Quando o cliente utiliza o protocolo PPP IPCP, obtém o endereço IP para a interface de túnel do servidor (o gateway de VPN neste caso). No entanto, devido a uma limitação no protocolo, o cliente não tem a máscara de sub-rede. Porque não existe nenhuma outra forma de fazê-lo, o cliente tenta adivinhar a máscara de sub-rede com base na classe do endereço IP da interface de túnel. 

Por conseguinte, é adicionada uma rota com base no seguinte mapeamento estático: 

Se o endereço pertence a classe A--> aplicar /8

Se o endereço pertence à classe B--> aplicar /16

Se o endereço pertence à classe C--> aplicar /24

### <a name="solution"></a>Solução

Ter as rotas para outras redes seja injetado na tabela de roteamento com correspondência de prefixo mais longa ou métrica menor (, por conseguinte, a maior prioridade) que o ponto a Site. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>Cliente VPN não é possível aceder a partilhas de ficheiros de rede

### <a name="symptom"></a>Sintoma

O cliente VPN tem ligados à rede virtual do Azure. No entanto, o cliente não é possível acessar compartilhamentos de rede.

### <a name="cause"></a>Causa

O protocolo SMB é utilizado para acesso de partilha de ficheiros. Quando a ligação é iniciada, o cliente VPN adiciona as credenciais de sessão e a falha ocorre. Depois da ligação é estabelecida, o cliente é forçado a utilizar as credenciais de cache para a autenticação Kerberos. Este processo inicia consultas para o Centro de distribuição de chaves (um controlador de domínio) para obter um token. Uma vez que o cliente se liga a partir da Internet, poderá não conseguir contactar o controlador de domínio. Por conseguinte, o cliente não é possível efetuar a ativação pós-falha do Kerberos para NTLM. 

O único momento em que é solicitado ao cliente para uma credencial é quando tem um certificado válido (com SAN = UPN) emitidos pelo domínio ao qual está associado. O cliente também tem de estar fisicamente ligado à rede de domínio. Neste caso, o cliente tenta utilizar o certificado e acede ao controlador de domínio. Em seguida, o Centro de distribuição de chaves devolve um erro de "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a efetuar a ativação pós-falha para NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desative a colocação em cache de credenciais de domínio na seguinte subchave do registo: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível localizar a ligação de VPN ponto a site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Remova a ligação de VPN ponto a site e, em seguida, reinstalar o cliente VPN. Nesta situação, a ligação VPN não está configurada com êxito. Não vir a ligação VPN na **ligações de rede** definições no Windows.

### <a name="solution"></a>Solução

Para resolver o problema, elimine os ficheiros de configuração de cliente VPN antigos partir **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >**, e, em seguida, volte a executar o instalador do cliente VPN .

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Cliente VPN ponto a site não é possível resolver o FQDN dos recursos no domínio local

### <a name="symptom"></a>Sintoma

Quando o cliente liga-se para o Azure utilizando a ligação de VPN ponto a site, ele não é possível resolver o FQDN dos recursos no seu domínio local.

### <a name="cause"></a>Causa

Cliente VPN ponto a site utiliza servidores de DNS do Azure que estão configurados na rede virtual do Azure. Os servidores de DNS do Azure têm precedência sobre os servidores DNS locais que estão configurados no cliente, para que todas as consultas DNS são enviadas para os servidores de DNS do Azure. Se os servidores de DNS do Azure não tiver os registos para os recursos locais, a consulta falha.

### <a name="solution"></a>Solução

Para resolver o problema, certifique-se de que os servidores de DNS do Azure utilizado na rede virtual do Azure, pode resolver os registos DNS para recursos locais. Para fazer isso, pode usar reencaminhadores de DNS ou reencaminhadores condicionais. Para obter mais informações, consulte [resolução de nomes através de seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>É estabelecida a ligação de VPN ponto a site, mas ainda não é possível ligar aos recursos do Azure 

### <a name="cause"></a>Causa

Esse problema pode ocorrer se o cliente VPN não obter as rotas do gateway de VPN do Azure.

### <a name="solution"></a>Solução

Para resolver este problema, [repor o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md). Para certificar-se de que as rotas novo estão a ser utilizadas, os clientes VPN ponto a Site tem de ser transferidos novamente depois de peering de rede virtual tiver sido configurado com êxito.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Erro: "A função de revogação não foi possível verificar revogação porque o servidor de revogação estava offline. (Erro 0x80092013)"

### <a name="causes"></a>Causas
Esta mensagem de erro ocorre se o cliente não conseguir aceder http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.  A verificação de revogação requer acesso a estes dois sites.  Este problema ocorre normalmente no cliente que tenha o servidor proxy configurado. Em alguns ambientes, se os pedidos serão através do servidor de proxy, ele será negado na Firewall de limite.

### <a name="solution"></a>Solução

Verifique as definições do servidor proxy, certifique-se de que o cliente pode aceder http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Erro de cliente VPN: A ligação foi impedida devido a uma política configurada no seu servidor RAS/VPN. (Erro 812)

### <a name="cause"></a>Causa

Este erro ocorre se o servidor RADIUS que utilizou para a autenticação de cliente VPN tem definições incorretas ou Gateway do Azure não é possível alcançar o servidor de Radius.

### <a name="solution"></a>Solução

Certifique-se de que o servidor RADIUS está configurado corretamente. Para obter mais informações, consulte [autenticação RADIUS integrar com o servidor do Azure multi-factor Authentication](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Erro 405" quando baixar o certificado de raiz do Gateway de VPN

### <a name="cause"></a>Causa

Certificado de raiz não tinha sido instalado. O certificado de raiz está instalado no cliente do **certificados confiáveis** armazenar.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Erro de cliente VPN: A ligação remota não foi efetuada uma vez que os túneis VPN tentativas falha. (Erro 800) 

### <a name="cause"></a>Causa

O driver NIC está desatualizado.

### <a name="solution"></a>Solução

Atualize o driver NIC:

1. Clique em **começar**, tipo **Gestor de dispositivos**e selecione-o na lista de resultados. Se lhe for pedida uma palavra-passe de administrador ou confirmação, escreva a palavra-passe ou indique confirmação.
2. Na **adaptadores de rede** categorias, localizar o NIC que pretende atualizar.  
3. Faça duplo clique o nome do dispositivo, selecione **atualizar driver**, selecione **pesquisar automaticamente software de driver atualizado**.
4. Se o Windows não encontrar um novo driver, pode tentar procurar um no site do fabricante do dispositivo e siga as instruções.
5. Reinicie o computador e tente novamente a ligação.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Erro: URI de destino não for especificado de erro de transferência de ficheiros

### <a name="cause"></a>Causa

Esta situação é provocada por um gateway incorreto tipo está configurado.

### <a name="solution"></a>Solução

O tipo de gateway de VPN do Azure tem de ser VPN e o tipo de VPN tem de ser **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalador do pacote de VPN não é concluído

### <a name="cause"></a>Causa

Esse problema pode dever-se se as instalações de cliente VPN anteriores. 

### <a name="solution"></a>Solução

Eliminar os ficheiros de configuração de cliente VPN antigos partir **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >** e volte a executar o instalador do cliente VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>O cliente VPN hiberna ou entra em suspensão após algum tempo

### <a name="solution"></a>Solução

Verifique o modo de suspensão e hibernação definições no computador que o cliente VPN está a ser executada.
