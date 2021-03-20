---
title: Problemas de resolução de problemas Problemas de ligação ponto-a-local
titleSuffix: Azure VPN Gateway
description: Aprenda a resolver problemas e resolva problemas comuns de ligação ponto-a-local e outros erros e problemas de rede privada virtual.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994864"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Resolução de problemas: Problemas de ligação ponto-a-local azure

Este artigo lista problemas comuns de ligação ponto-a-local que poderá experimentar. Aborda igualmente possíveis causas e soluções para estes problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro do cliente VPN: Não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Quando tenta ligar-se a uma rede virtual Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que possa ser utilizado com este Protocolo de Autenticação Extensível. (Erro 798)**

### <a name="cause"></a>Causa

Este problema ocorre se o certificado do cliente faltar **aos Certificados - Utilizador Atual\Personal\Certificates**.

### <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Abra o Gestor de Certificados: Clique **em Iniciar,** **escreva para gerir certificados de computador** e, em seguida, clique em gerir **certificados de computador** no resultado da pesquisa.

2. Certifique-se de que os seguintes certificados estão no local correto:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utilizador atual\Personal\Certificados |
    | AzureRoot.cer    | Autoridades locais de certificação de raiz de computador\fidedignos|

3. Ir para C:\Utilizadores \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , instalar manualmente o certificado (*.cer ficheiro) na loja do utilizador e do computador.

Para obter mais informações sobre como instalar o certificado do cliente, consulte [Certificados de Geração e exportação para ligações ponto-a-local](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando importar o certificado de cliente, não selecione a opção **de proteção de chave privada forte.**

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>A ligação de rede entre o seu computador e o servidor VPN não foi possível ser estabelecida porque o servidor remoto não está a responder

### <a name="symptom"></a>Sintoma

Quando tenta ligar-se a um gateway de rede virtual Azure utilizando o IKEv2 no Windows, obtém a seguinte mensagem de erro:

**A ligação de rede entre o seu computador e o servidor VPN não foi possível ser estabelecida porque o servidor remoto não está a responder**

### <a name="cause"></a>Causa
 
 O problema ocorre se a versão do Windows não tiver suporte para fragmentação do IKE
 
### <a name="solution"></a>Solução

O IKEv2 é suportado no Windows 10 e Windows Server 2016. No entanto, para poder utilizar o IKEv2, tem de instalar as atualizações e definir uma chave de registo localmente. As versões de SO anteriores ao Windows 10 não são suportadas e só podem utilizar o SSTP.

Para preparar o Windows 10 ou o Windows Server 2016 para o IKEv2:

1. Instale a atualização.

   | Versão do SO | Data | Número/Ligação |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, Versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Versão 1709 do Windows 10 | 22 de março de 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Defina o valor da chave de registo. Criar ou definir `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD chave no registo para 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro do cliente VPN: A mensagem recebida foi inesperada ou mal formatada

### <a name="symptom"></a>Sintoma

Quando tenta ligar-se a uma rede virtual Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**A mensagem recebida foi inesperada ou mal formatada. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Este problema ocorre se uma das seguintes condições for verdadeira:

- A utilização de rotas definidas pelo utilizador (UDR) com rota predefinido na sub-rede Gateway é definida incorretamente.
- A chave pública do certificado de raiz não é enviada para o gateway Azure VPN. 
- A chave está corrompida ou expirada.

### <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Remova o UDR na sub-rede Gateway. Certifique-se de que o UDR encaminha todo o tráfego corretamente.
2. Verifique o estado do certificado de raiz no portal Azure para ver se foi revogado. Se não for revogado, tente eliminar o certificado de raiz e recarregar. Para mais informações, consulte [Criar certificados.](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro do cliente VPN: Uma cadeia de certificados processada mas encerrada 

### <a name="symptom"></a>Sintoma 

Quando tenta ligar-se a uma rede virtual Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**Uma cadeia de certificados processada mas encerrada num certificado de raiz que não é fidedigno pelo prestador fiduciário.**

### <a name="solution"></a>Solução

1. Certifique-se de que os seguintes certificados estão no local correto:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utilizador atual\Personal\Certificados |
    | Azuregateway -*GUID*.cloudapp.net  | As atuais autoridades de certificação de raiz fidedignas do utilizador\Fidedigna|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Autoridades locais de certificação de raiz de computador\fidedignos|

2. Se os certificados já se encontram no local, tente apagar os certificados e reinstalá-los. O certificado **azuregateway-*GUID*.cloudapp.net** está no pacote de configuração do cliente VPN que descarregou a partir do portal Azure. Pode utilizar os arquivos para extrair os ficheiros da embalagem.

## <a name="file-download-error-target-uri-is-not-specified"></a>Erro de descarregamento de ficheiros: O Target URI não é especificado

### <a name="symptom"></a>Sintoma

Recebe a seguinte mensagem de erro:

**Erro de descarregamento de ficheiros. O URI do alvo não está especificado.**

### <a name="cause"></a>Causa 

Este problema ocorre devido a um tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway VPN deve ser **VPN,** e o tipo VPN deve ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Erro do cliente VPN: O script personalizado Azure VPN falhou 

### <a name="symptom"></a>Sintoma

Quando tenta ligar-se a uma rede virtual Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**O script personalizado (para atualizar a tabela de encaminhamento) falhou. (Erro 8007026f)**

### <a name="cause"></a>Causa

Este problema pode ocorrer se estiver a tentar abrir a ligação VPN local-a-ponto utilizando um atalho.

### <a name="solution"></a>Solução 

Abra o pacote VPN diretamente em vez de o abrir a partir do atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

É necessário um certificado adicional para confiar na porta VPN para a sua rede virtual. O certificado está incluído no pacote de configuração do cliente VPN que é gerado a partir do portal Azure.

### <a name="solution"></a>Solução

Extraia o pacote de configuração do cliente VPN e encontre o ficheiro .cer. Para instalar o certificado, siga estes passos:

1. Abra mmc.exe.
2. Adicione o encaixe dos **certificados.**
3. Selecione a conta **de Computador** para o computador local.
4. Clique com o direito no nó **de Autoridades de Certificação de Raiz Fidedigna.** Clique **em All-Task**  >  **Import** e navegue no ficheiro .cer extraído do pacote de configuração do cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro do portal Azure: Falhou em salvar a porta de entrada VPN, e os dados são inválidos

### <a name="symptom"></a>Sintoma

Quando tenta guardar as alterações para o gateway VPN no portal Azure, recebe a seguinte mensagem de erro:

**Não conseguiu salvar o nome de gateway de gateway de rede virtual &lt;  &gt; . Os dados relativos ao &lt; *certificado de identificação são* &gt; inválidos.**

### <a name="cause"></a>Causa 

Este problema pode ocorrer se a chave pública do certificado de raiz que carregou contiver um caráter inválido, como um espaço.

### <a name="solution"></a>Solução

Certifique-se de que os dados constantes do certificado não contêm caracteres inválidos, tais como quebras de linha (devoluções de transporte). Todo o valor deve ser uma longa linhagem. O seguinte texto é uma amostra do certificado:

```text
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
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro do portal Azure: Falhou em salvar o gateway VPN, e o nome do recurso é inválido

### <a name="symptom"></a>Sintoma

Quando tenta guardar as alterações para o gateway VPN no portal Azure, recebe a seguinte mensagem de erro: 

**Não conseguiu salvar o nome de gateway de gateway de rede virtual &lt;  &gt; . O nome do certificado de recurso &lt; *que tenta carregar* é &gt; inválido**.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome do certificado contém um caráter inválido, como um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro do portal Azure: Erro de descarregamento de ficheiros VPN 503

### <a name="symptom"></a>Sintoma

Quando tenta descarregar o pacote de configuração do cliente VPN, recebe a seguinte mensagem de erro:

**Falhou em descarregar o ficheiro. Detalhes de erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Este erro pode ser causado por um problema temporário de rede. Tente baixar o pacote VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Atualização Azure VPN Gateway: Todos os clientes do Ponto para o Site não conseguem ligar

### <a name="cause"></a>Causa

Se o certificado for superior a 50% ao longo da sua vida útil, o certificado é revirado.

### <a name="solution"></a>Solução

Para resolver este problema, refirmisma e reimplante o pacote Ponto para Site em todos os clientes.

## <a name="too-many-vpn-clients-connected-at-once"></a>Demasiados clientes VPN ligados ao mesmo tempo

O número máximo de ligações admissíveis é atingido. Pode ver o número total de clientes conectados no portal Azure.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Cliente VPN não pode aceder a ações de ficheiros de rede

### <a name="symptom"></a>Sintoma

O cliente VPN ligou-se à rede virtual Azure. No entanto, o cliente não pode aceder a partilhas de rede.

### <a name="cause"></a>Causa

O protocolo SMB é utilizado para o acesso a partilha de ficheiros. Quando a ligação é iniciada, o cliente VPN adiciona as credenciais de sessão e a falha ocorre. Após a ligação ser estabelecida, o cliente é obrigado a usar as credenciais de cache para a autenticação Kerberos. Este processo inicia consultas ao Centro de Distribuição de Chaves (um controlador de domínio) para obter um token. Como o cliente se conecta a partir da Internet, pode não ser capaz de chegar ao controlador de domínio. Portanto, o cliente não pode falhar de Kerberos para NTLM. 

A única vez que o cliente é solicitado para uma credencial é quando tem um certificado válido (com SAN=UPN) emitido pelo domínio a que se junta. O cliente também deve estar fisicamente ligado à rede de domínio. Neste caso, o cliente tenta usar o certificado e contacta o controlador de domínio. Em seguida, o Centro de Distribuição de Chaves retorna um erro de "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a falhar com a NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desative o caching das credenciais de domínio a partir do seguinte sub-chave de registo: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível encontrar a ligação VPN ponto a local no Windows depois de reinstalar o cliente VPN

### <a name="symptom"></a>Sintoma

Remove a ligação VPN ponto-a-local e, em seguida, reinstala o cliente VPN. Nesta situação, a ligação VPN não é configurada com sucesso. Não vê a ligação VPN nas definições de **ligações de Rede** no Windows.

### <a name="solution"></a>Solução

Para resolver o problema, elimine os antigos ficheiros de configuração do cliente VPN de **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** e, em seguida, executar novamente o instalador de clientes VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Cliente VPN ponto a local não pode resolver o FQDN dos recursos no domínio local

### <a name="symptom"></a>Sintoma

Quando o cliente se conecta ao Azure utilizando a ligação VPN ponto-a-local, não pode resolver o FQDN dos recursos no seu domínio local.

### <a name="cause"></a>Causa

O cliente VPN ponto-a-local normalmente utiliza servidores Azure DNS que estão configurados na rede virtual Azure. Os servidores DNS do Azure têm precedência sobre os servidores DNS locais que estão configurados no cliente (a menos que a métrica da interface Ethernet seja menor), pelo que todas as consultas de DNS são enviadas para os servidores DNS do Azure. Se os servidores Azure DNS não tiverem os registos dos recursos locais, a consulta falha.

### <a name="solution"></a>Solução

Para resolver o problema, certifique-se de que os servidores Azure DNS utilizados na rede virtual Azure podem resolver os registos DNS para recursos locais. Para isso, pode utilizar os remetentes DNS ou os reencaminhadores condicional. Para mais informações, consulte [a resolução de nomes utilizando o seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A ligação VPN ponto-a-local está estabelecida, mas ainda não consegue ligar-se aos recursos do Azure 

### <a name="cause"></a>Causa

Este problema pode ocorrer se o cliente VPN não conseguir as rotas a partir do gateway Azure VPN.

### <a name="solution"></a>Solução

Para resolver este problema, [reinicie o gateway Azure VPN](./reset-gateway.md). Para garantir que as novas rotas estão a ser utilizadas, os clientes VPN Ponto-a-Local devem ser novamente descarregados após o espreitamento da rede virtual ter sido configurado com sucesso.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Erro: "A função de revogação não foi capaz de verificar a revogação porque o servidor de revogação estava offline. (Erro 0x80092013)"

### <a name="causes"></a>Causas
Esta mensagem de erro ocorre se o cliente não puder aceder http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl . .  A verificação de revogação requer acesso a estes dois sites.  Este problema normalmente acontece no cliente que tem o servidor proxy configurado. Em alguns ambientes, se os pedidos não estiverem a passar pelo servidor proxy, será negado no Edge Firewall.

### <a name="solution"></a>Solução

Verifique as definições do servidor proxy, certifique-se de que o cliente pode aceder http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Erro do Cliente VPN: A ligação foi impedida devido a uma política configurada no seu servidor RAS/VPN. (Erro 812)

### <a name="cause"></a>Causa

Este erro ocorre se o servidor RADIUS que usou para autenticar o cliente VPN tiver definições incorretas ou o Azure Gateway não conseguir chegar ao servidor Radius.

### <a name="solution"></a>Solução

Certifique-se de que o servidor RADIUS está configurado corretamente. Para obter mais informações, consulte [integrar a autenticação RADIUS com o Azure AD Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Erro 405" ao descarregar o certificado de raiz da VPN Gateway

### <a name="cause"></a>Causa

O certificado de raiz não tinha sido instalado. O certificado raiz está instalado na loja **de certificados Trusted** do cliente.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Erro do Cliente VPN: A ligação remota não foi feita porque os túneis VPN tentados falharam. (Erro 800) 

### <a name="cause"></a>Causa

O condutor da NIC está ultrapassado.

### <a name="solution"></a>Solução

Atualize o controlador NIC:

1. Clique **em Iniciar,** digite **o Gestor de Dispositivos** e selecione-o na lista de resultados. Se for solicitado uma senha ou confirmação do administrador, digite a palavra-passe ou forneça a confirmação.
2. Nas categorias **de adaptadores de rede,** encontre o NIC que pretende atualizar.  
3. Clique duas vezes no nome do dispositivo, selecione **'Actualizar' ('Actualizar' (Update driver),** selecione **Search automaticamente para obter software do controlador atualizado**.
4.  Se o Windows não localizar um novo controlador, pode experimentar procurar um no site do fabricante do dispositivo e seguir as suas instruções.
5. Reinicie o computador e tente novamente a ligação.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Erro do cliente VPN: Marcar ligação VPN, <VPN Connection Name> Estado = Plataforma VPN não desencadeou ligação

Pode também ver o seguinte erro no Visualizador de Eventos da RasClient: "O utilizador <User> marcou uma ligação com o nome <VPN Connection Name> que falhou. O código de erro devolvido na falha é 1460."

### <a name="cause"></a>Causa

O Cliente Azure VPN não tem a Permissão de aplicação "Background apps" ativada nas Definições de Aplicações para Windows.

### <a name="solution"></a>Solução

1. No Windows, aceda a Configurações -> aplicações de fundo de privacidade ->
2. Alternar o "Deixe as aplicações correrem em segundo plano" para On

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Erro: 'Erro de descarregamento de ficheiros O target URI não é especificado'

### <a name="cause"></a>Causa

Isto é causado por um tipo de gateway incorreto é configurado.

### <a name="solution"></a>Solução

O tipo de gateway Azure VPN deve ser VPN e o tipo VPN deve ser **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>O instalador de pacotes VPN não completa

### <a name="cause"></a>Causa

Este problema pode ser causado pelas instalações anteriores do cliente VPN. 

### <a name="solution"></a>Solução

Elimine os antigos ficheiros de configuração do cliente VPN de **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** e executar novamente o instalador de clientes VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>O cliente VPN hiberna ou dorme depois de algum tempo

### <a name="solution"></a>Solução

Verifique as definições de sono e hibernação no computador em que o cliente VPN está a funcionar.