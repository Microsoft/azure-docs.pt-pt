---
title: Problemas de resolução de problemas de saúde rebatem problemas de saúde no Portal de Aplicação Azure
description: Descreve como resolver problemas de saúde para o Portal de Aplicação Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80983845"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemas de resolução de problemas de saúde rebatem problemas de saúde no Application Gateway
==================================================

<a name="overview"></a>Descrição geral
--------

Por padrão, o Portal de Aplicações Azure sonda os servidores de backend para verificar o seu estado de saúde e verificar se estão prontos para atender pedidos. Os utilizadores também podem criar sondas personalizadas para mencionar o nome do anfitrião, o caminho a ser sondado, e os códigos de estado a serem aceites como Saudáveis. Em cada caso, se o servidor backend não responder com sucesso, o Gateway de aplicação marca o servidor como Insalubre e deixa de encaminhar pedidos para o servidor. Depois de o servidor começar a responder com sucesso, o Application Gateway retoma a reencaminhamento dos pedidos.

### <a name="how-to-check-backend-health"></a>Como verificar a saúde de backend

Para verificar a saúde da sua piscina de backend, pode utilizar a página **Backend Health** no portal Azure. Ou, pode utilizar [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)ou [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

O estado recuperado por qualquer um destes métodos pode ser qualquer um dos seguintes métodos:

- Bom estado de funcionamento

- Mau estado de funcionamento

- Desconhecido

Se o estado de saúde de backend para um servidor for saudável, significa que o Gateway da Aplicação irá encaminhar os pedidos para esse servidor. Mas se a saúde de backend para todos os servidores de uma piscina de backend é insalubre ou desconhecida, você pode encontrar problemas quando você tenta aceder a aplicações. Este artigo descreve os sintomas, a causa e a resolução para cada um dos erros mostrados.

<a name="backend-health-status-unhealthy"></a>Estado de saúde de backend: Insalubre
-------------------------------

Se o estado de saúde do backend não for saudável, a visão do portal assemelhar-se-á à seguinte imagem:

![Aplicação Gateway backend health - Unhealthy](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou se estiver a usar uma consulta de API Azure PowerShell, CLI ou Azure REST, terá uma resposta que se assemelha ao seguinte:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Depois de receber um estado de servidor de backend não saudável para todos os servidores numa piscina de backend, os pedidos não são encaminhados para os servidores, e o Application Gateway devolve um erro "502 Bad Gateway" ao cliente que solicita. Para resolver este problema, consulte a coluna **Details** no separador **Backend Health.**

A mensagem exibida na coluna **Details** fornece informações mais detalhadas sobre o assunto, e com base nesses, pode começar a resolver problemas com o problema.

> [!NOTE]
> O pedido de sonda predefinido \<\>é enviado no formato do\<protocolo\>://127.0.0.1: porta /. Por exemplo, http://127.0.0.1:80 para uma sonda http na porta 80. Apenas códigos de estado HTTP de 200 a 399 são considerados saudáveis. O protocolo e a porta de destino são herdados das definições http. Se quiser que o Application Gateway sondar um protocolo, nome ou caminho diferente e reconhecer um código de estado diferente como Saudável, configure uma sonda personalizada e associe-a às definições http.

<a name="error-messages"></a>Mensagens de erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo de tempo de tempo do servidor backend

**Mensagem:** O tempo tomado pelo backend\'para responder à sonda de saúde do gateway de aplicação é mais do que o limite de tempo limite na configuração da sonda.

**Causa:** Depois de o Application Gateway enviar um pedido de sonda HTTP(S) para o servidor backend, aguarda uma resposta do servidor backend por um período configurado. Se o servidor backend não responder dentro do período configurado (o valor de tempo de tempo), é marcado como Insalubre até que volte a responder dentro do período de tempo configurado.

**Resolução:** Verifique por que razão o servidor ou aplicação de backend não está a responder dentro do período de tempo configurado e verifique também as dependências da aplicação. Por exemplo, verifique se a base de dados tem algum problema que possa desencadear um atraso na resposta. Se tiver conhecimento do comportamento da aplicação e ela só responder após o valor do tempo de paragem, aumente o valor do tempo de tempo a partir das definições de sonda personalizada. Deve ter uma sonda personalizada para alterar o valor do tempo. Para obter informações sobre como configurar uma sonda personalizada, [consulte a página de documentação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Para aumentar o valor do tempo, siga estes passos:

1.  Aceda diretamente ao servidor backend e verifique o tempo devida para que o servidor responda nessa página. Pode utilizar qualquer ferramenta para aceder ao servidor backend, incluindo um browser utilizando ferramentas de desenvolvimento.

1.  Depois de ter descoberto o tempo necessário para a aplicação responder, selecione o separador **Health Probes** e, em seguida, selecione a sonda que está associada às definições do HTTP.

1.  Introduza qualquer valor de tempo-tempo maior do que o tempo de resposta da aplicação, em segundos.

1.  Guarde as definições de sonda personalizadas e verifique se a saúde de backend mostra agora como Saudável.

#### <a name="dns-resolution-error"></a>Erro de resolução dNS

**Mensagem:** O Gateway de aplicação não podia criar uma sonda para este backend. Normalmente, esta situação acontece quando o FQDN do back-end não foi inserido corretamente. 

**Causa:** Se o pool backend for de endereço IP tipo/FQDN ou App Service, o Gateway de aplicação resolve-se no endereço IP do FQDN introduzido através do Sistema de Nome de Domínio (DNS) (padrão personalizado ou Azure) e tenta ligar-se ao servidor na porta TCP mencionada nas Definições HTTP. Mas se esta mensagem for exibida, sugere que o Application Gateway não conseguiu resolver com sucesso o endereço IP do FQDN introduzido.

**Resolução:**

1.  Verifique se o FQDN entrou na piscina de backend está correto e que é um domínio público, e depois tente resolvê-lo a partir da sua máquina local.

1.  Se conseguir resolver o endereço IP, pode haver algo de errado com a configuração dNS na rede virtual.

1.  Verifique se a rede virtual está configurada com um servidor DNS personalizado. Se for, verifique o servidor DNS sobre o porquê de não conseguir resolver o endereço IP do FQDN especificado.

1.  Se estiver a utilizar dNS padrão Azure, verifique com o registo do seu nome de domínio se um registo adequado ou um mapeamento de registo CNAME foi concluído.

1.  Se o domínio for privado ou interno, tente resolvê-lo a partir de um VM na mesma rede virtual. Se conseguir resolvê-lo, reinicie o Gateway da Aplicação e verifique novamente. Para reiniciar o Gateway da Aplicação, tem de [parar](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [começar](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) por utilizar os comandos PowerShell descritos nestes recursos ligados.

#### <a name="tcp-connect-error"></a>Erro de ligação TCP

**Mensagem:** O Gateway da aplicação não podia ligar-se ao backend.
Por favor, verifique se o backend responde na porta utilizada para a sonda.
Verifique também se algum NSG/UDR/Firewall está a bloquear o acesso ao Ip e à porta deste backend

**Causa:** Após a fase de resolução dNS, o Application Gateway tenta ligar-se ao servidor backend na porta TCP que está configurado nas definições http. Se o Application Gateway não conseguir estabelecer uma sessão de TCP na porta especificada, a sonda é marcada como insalubre com esta mensagem.

**Solução:** Se receber este erro, siga estes passos:

1.  Verifique se pode ligar-se ao servidor backend na porta mencionada nas definições http, utilizando um navegador ou PowerShell. Por exemplo, executar o seguinte comando:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se a porta mencionada não for a porta desejada, introduza o número de porta correto para o Gateway da Aplicação para ligar ao servidor backend

1.  Se também não consegue ligar a porta da sua máquina local, então:

    a.  Verifique as definições do grupo de segurança da rede (NSG) do adaptador e sub-rede do servidor de backend e se são permitidas ligações de entrada à porta configurada. Se não forem, crie uma nova regra para permitir as ligações. Para aprender a criar regras de NSG, [consulte a página de documentação](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verifique se as definições de NSG da subnet Application Gateway permitem tráfego público e privado de saída, de modo a que uma ligação possa ser feita. Consulte a página de documentos que é fornecida no passo 3a para saber mais sobre como criar regras de NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as definições de rotas definidas pelo utilizador (UDR) do Gateway de Aplicação e a subnet do servidor de backend para obter quaisquer anomalias de encaminhamento. Certifique-se de que o UDR não está a direcionar o tráfego para longe da sub-rede de backend. Por exemplo, verifique se estão a ser publicitadas rotas para rede de aparelhos virtuais ou rotas predefinidas para a subnet Application Gateway via Azure ExpressRoute e/ou VPN.

    d.  Para verificar as rotas e regras eficazes para um adaptador de rede, pode utilizar os seguintes comandos PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se não encontrar problemas com NSG ou UDR, verifique o seu servidor de backend para obter problemas relacionados com aplicações que estão a impedir os clientes de estabelecer uma sessão de TCP nas portas configuradas. Algumas coisas para verificar:

    a.  Abra um pedido de comando\> (Win+R - cmd), introduza, `netstat`e selecione Enter.

    b.  Verifique se o servidor está a ouvir a porta configurada. Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se não estiver a ouvir a porta configurada, verifique as definições do servidor web. Por exemplo: encadernações de site no IIS, bloco de servidores em NGINX e hospedeiro virtual em Apache.

    d.  Verifique as definições de firewall do SISTEMA PARA se certificar de que o tráfego de entrada para a porta é permitido.

#### <a name="http-status-code-mismatch"></a>Desajuste do código de estado HTTP

**Mensagem:** O código de\'estado da resposta HTTP do backend não corresponde à definição da sonda. Esperado:{HTTPStatusCode0} Recebido:{HTTPStatusCode1}.

**Causa:** Depois de a ligação TCP ter sido estabelecida e de ser feito um aperto de mão TLS (se o TLS estiver ativado), o Application Gateway enviará a sonda como um pedido HTTP GET para o servidor backend. Como descrito anteriormente, a sonda \<\>padrão será para o protocolo ://127.0.0.1:\<porta\>/, e considera os códigos de estado de resposta na raiva 200 a 399 como Saudável. Se o servidor devolver qualquer outro código de estado, será marcado como Insalubre com esta mensagem.

**Solução:** Dependendo do código de resposta do servidor de backend, pode tomar os seguintes passos. Alguns dos códigos de estatuto comuns estão listados aqui:

| **Erro** | **Ações** |
| --- | --- |
| Desajuste do código de estado da sonda: Recebido 401 | Verifique se o servidor backend necessita de autenticação. Aplicação As sondas Gateway não podem passar credenciais para autenticação neste momento. Ou \"permite http\" 401 numa correspondência de código de estado da sonda ou sonda para um caminho onde o servidor não requer autenticação. | |
| Desajuste do código de estado da sonda: Recebido 403 | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor de backend. | |
| Desajuste do código de estado da sonda: Recebido 404 | Página não encontrada. Verifique se o caminho do nome do anfitrião está acessível no servidor backend. Mude o nome ou parâmetro do caminho para um valor acessível. | |
| Desajuste do código de estado da sonda: Recebido 405 | Os pedidos de sonda para application Gateway utilizam o método HTTP GET. Verifique se o seu servidor permite este método. | |
| Desajuste do código de estado da sonda: Recebido 500 | Erro interno do servidor. Verifique a saúde do servidor de backend e se os serviços estão em funcionamento. | |
| Desajuste do código de estado da sonda: Recebido 503 | Serviço indisponível. Verifique a saúde do servidor de backend e se os serviços estão em funcionamento. | |

Ou, se acha que a resposta é legítima e quer que o Application Gateway aceite outros códigos de estado como Saudáveis, pode criar uma sonda personalizada. Esta abordagem é útil em situações em que o site backend precisa de autenticação. Como os pedidos da sonda não têm credenciais de utilizador, falharão e um código de estado HTTP 401 será devolvido pelo servidor backend.

Para criar uma sonda personalizada, siga [estes passos.](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)

#### <a name="http-response-body-mismatch"></a>Desfasamento do corpo de resposta HTTP

**Mensagem:** O corpo da\'resposta HTTP do backend não corresponde à configuração da sonda. O corpo de resposta recebido não contém {string}.

**Causa:** Ao criar uma sonda personalizada, tem a opção de marcar um servidor de backend como Saudável, combinando uma corda do corpo de resposta. Por exemplo, pode configurar o Application Gateway para aceitar "não autorizado" como uma corda para combinar. Se a resposta do servidor de backend para o pedido de sonda contiver a corda **não autorizada,** será marcada como Saudável. Caso contrário, será marcado como Insalubre com esta mensagem.

**Solução:** Para resolver esta questão, siga estes passos:

1.  Aceda ao servidor backend localmente ou a partir de uma máquina de cliente no caminho da sonda, e verifique o corpo de resposta.

1.  Verifique se o corpo de resposta na configuração personalizada da sonda Application Gateway corresponde ao que está configurado.

1.  Se não coincidirem, altere a configuração da sonda de modo a que tenha o valor de corda correto para aceitar.

Saiba mais sobre a [correspondência da sonda Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Certificado de servidor backend inválido CA

**Mensagem:** O certificado de servidor utilizado pelo backend não é assinado por uma conhecida Autoridade de Certificados (CA). Whitelist o backend no Gateway de aplicação, carregando o certificado de raiz do certificado de servidor utilizado pelo backend.

**Causa:** O SSL de ponta a ponta com o Gateway de aplicação v2 requer que o certificado do servidor de backend seja verificado de modo a parecer o servidor Saudável.
Para que seja de confiança um certificado TLS/SSL, esse certificado do servidor backend deve ser emitido por um CA incluído na loja fidedigna do Application Gateway. Se o certificado não foi emitido por um CA de confiança (por exemplo, se um certificado auto-assinado foi usado), os utilizadores devem enviar o certificado do emitente para o Gateway de Aplicação.

**Solução:** Siga estes passos para exportar e faça upload do certificado de raiz fidedigno para O Gateway de Aplicação. (Estes passos são para clientes Windows.)

1.  Inscreva-se na máquina onde a sua aplicação está hospedada.

1.  Selecione Win+R ou clique à direita no botão **Iniciar** e, em seguida, selecione **Executar**.

1.  Introduza `certmgr.msc` e selecione Entrar. Também pode pesquisar o Gestor de Certificados no menu **Iniciar.**

1.  Localize o certificado, `\Certificates - Current User\\Personal\\Certificates\`normalmente dentro, e abra-o.

1.  Selecione o certificado raiz e, em seguida, selecione **'Ver Certificado**'.

1.  Nas propriedades do Certificado, selecione o separador **Detalhes.**

1.  No separador **Detalhes,** selecione a opção **Copiar para Ficheiro** e guarde o ficheiro na Base-64 codificada X.509 (. Formato CER).

1.  Abra a página de **definições** do Gateway HTTP no portal Azure.

1. Abra as definições http, selecione **Adicionar Certificado,** e localize o ficheiro de certificado que acabou de guardar.

1. Selecione **Guardar** para guardar as definições http.

Em alternativa, pode exportar o certificado raiz de uma máquina cliente acedendo diretamente ao servidor (contornando o Application Gateway) através do navegador e exportando o certificado raiz do navegador.

Para obter mais informações sobre como extrair e carregar Certificados de Raiz Fidedignos no Gateway de Aplicação, consulte [Certificado de raiz fidedigno de exportação (v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Desajuste de certificado de raiz fidedigno

**Mensagem:** O certificado de raiz do certificado de servidor utilizado pelo backend não corresponde ao certificado de raiz fidedigno adicionado ao gateway da aplicação. Certifique-se de que adiciona o certificado de raiz correto ao whitelist do backend

**Causa:** O SSL de ponta a ponta com o Gateway de aplicação v2 requer que o certificado do servidor de backend seja verificado de modo a parecer o servidor Saudável.
Para que seja de confiança um certificado TLS/SSL, o certificado de servidor backend deve ser emitido por um CA incluído na loja fidedigna do Application Gateway. Se o certificado não foi emitido por um CA de confiança (por exemplo, foi utilizado um certificado auto-assinado), os utilizadores devem enviar o certificado do emitente para o Application Gateway.

O certificado que foi enviado para as definições do Gateway HTTP de aplicação deve corresponder ao certificado raiz do certificado de servidor backend.

**Solução:** Se receber esta mensagem de erro, há uma incompatibilidade entre o certificado que foi enviado para o Gateway de Aplicação e o que foi enviado para o servidor backend.

Siga os passos 1-11 no método anterior para carregar o certificado de raiz fidedigno correto para o Gateway de Aplicação.

Para obter mais informações sobre como extrair e carregar Certificados de Raiz Fidedignos no Gateway de Aplicação, consulte [Certificado de raiz fidedigno de exportação (v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Este erro também pode ocorrer se o servidor backend não trocar a cadeia completa do certificado, incluindo o Root > Intermediate (se aplicável) > Leaf durante o aperto de mão TLS. Para verificar, pode utilizar comandos OpenSSL de qualquer cliente e ligar-se ao servidor backend utilizando as definições configuradas na sonda Gateway da aplicação.

Por exemplo:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar que a cadeia completa do certificado seja devolvida, exporte novamente o certificado com a cadeia completa, incluindo o certificado de raiz. Configure esse certificado no seu servidor de backend. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Certificado de backend inválido nome comum (CN)

**Mensagem:** O Nome Comum (CN) do certificado de backend não corresponde ao cabeçalho hospedeiro da sonda.

**Causa:** O Gateway de aplicação verifica se o nome do anfitrião especificado nas definições de HTTP de backend corresponde ao do NC apresentado pelo certificado TLS/SSL do servidor de backend. Isto é Standard_v2 e WAF_v2 comportamento SKU. A indicação de nome de servidor (SNI) do Standard e WAF SKU é definida como fQDN no endereço de piscina de backend.

No V2 SKU, se houver uma sonda padrão (nenhuma sonda personalizada foi configurada e associada), o SNI será definido a partir do nome do anfitrião mencionado nas definições HTTP. Ou, se "Escolher o nome do anfitrião do endereço backend" é mencionado nas definições http, onde a piscina de endereços de backend contém um FQDN válido, esta definição será aplicada.

Se houver uma sonda personalizada associada às definições http, o SNI será definido a partir do nome do anfitrião mencionado na configuração da sonda personalizada. Ou, se **o nome de anfitrião Pick das definições de BACKEnd HTTP** for selecionado na sonda personalizada, o SNI será definido a partir do nome do anfitrião mencionado nas definições http.

Se **o nome de anfitrião Pick do endereço backend** estiver definido nas definições http, o conjunto de endereços de backend deve conter um FQDN válido.

Se receber esta mensagem de erro, o NC do certificado backend não corresponde ao nome do anfitrião configurado na sonda personalizada ou nas definições HTTP (se escolher o **nome de anfitrião das definições DE BACKEnd HTTP).** Se estiver a utilizar uma sonda predefinida, o nome do anfitrião será definido como **127.0.0.1**. Se não for um valor desejado, deverá criar uma sonda personalizada e associá-la às definições http.

**Solução:**

Para resolver o problema, siga estes passos.

Para Windows:

1.  Inscreva-se na máquina onde a sua aplicação está hospedada.

1.  Selecione Win+R ou clique à direita no botão **Iniciar** e selecione **Executar**.

1.  Introduza **certmgr.msc** e selecione Enter. Também pode pesquisar o Gestor de Certificados no menu **Iniciar.**

1.  Localize o certificado `\Certificates - Current User\\Personal\\Certificates`(normalmente dentro) e abra o certificado.

1.  No separador **Detalhes,** verifique o **assunto**do certificado .

1.  Verifique o NC do certificado a partir dos detalhes e introduza o mesmo no campo de nome do anfitrião da sonda personalizada ou nas definições http (se escolher o **nome de anfitrião das definições** de HTTP de backend é selecionado). Se este não for o nome de anfitrião desejado para o seu website, deve obter um certificado para esse domínio ou introduzir o nome de anfitrião correto na sonda personalizada ou configuração de definição HTTP.

Para linux usando OpenSSL:

1.  Executar este comando no OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A partir das propriedades apresentadas, encontre o NC do certificado e introduza o mesmo no campo de nome do anfitrião das definições de http. Se este não for o nome de anfitrião desejado para o seu website, deve obter um certificado para esse domínio ou introduzir o nome de anfitrião correto na sonda personalizada ou configuração de definição HTTP.

#### <a name="backend-certificate-is-invalid"></a>O certificado backend é inválido

**Mensagem:** O certificado de backend é inválido. A data atual \"não\" está \"dentro\" do intervalo Válido e Válido até à data no certificado.

**Causa:** Cada certificado vem com um intervalo de validade, e a ligação HTTPS não será segura a menos que o certificado TLS/SSL do servidor seja válido. Os dados atuais devem estar dentro **do válido** e **válido ao** alcance. Caso contrário, o certificado é considerado inválido, e isso criará um problema de segurança no qual o Application Gateway marca o servidor backend como Insalubre.

**Solução:** Se o seu certificado TLS/SSL tiver expirado, renove o certificado com o seu fornecedor e atualize as definições do servidor com o novo certificado. Se for um certificado auto-assinado, deve gerar um certificado válido e fazer o upload do certificado de raiz para as definições do Gateway HTTP da aplicação. Para tal, siga estes passos:

1.  Abra as definições do Gateway HTTP da aplicação no portal.

1.  Selecione a definição que tem o certificado expirado, selecione **Adicionar Certificado,** e abra o novo ficheiro de certificado.

1.  Retire o certificado antigo utilizando o ícone **Delete** junto ao certificado e, em seguida, selecione **Guardar**.

#### <a name="certificate-verification-failed"></a>Verificação de certificado falhou

**Mensagem:** A validade do certificado de backend não pôde ser verificada. Para descobrir a razão, verifique os diagnósticos openSSL para a mensagem associada ao código de erro {errorCode}

**Causa:** Este erro ocorre quando o Gateway de Aplicação não pode verificar a validade do certificado.

**Solução:** Para resolver este problema, verifique se o certificado no seu servidor foi criado corretamente. Por exemplo, pode utilizar o [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e as suas propriedades e, em seguida, tentar recarregar o certificado para as definições do Gateway HTTP da aplicação.

<a name="backend-health-status-unknown"></a>Estado de saúde de backend: desconhecido
-------------------------------
Se a saúde do backend for mostrada como Desconhecida, a vista do portal assemelhar-se-á à seguinte imagem:

![Aplicação Gateway backend health - Desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Este comportamento pode ocorrer por uma ou mais das seguintes razões:

1.  O NSG na subnet Application Gateway está a bloquear o acesso de entrada às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) de "Internet".
1.  O UDR na subnet Application Gateway está definido para a rota padrão (0.0.0.0/0) e o próximo salto não é especificado como "Internet".
1.  A rota padrão é anunciada por uma ligação ExpressRoute/VPN a uma rede virtual sobre bGP.
1.  O servidor DNS personalizado está configurado numa rede virtual que não consegue resolver nomes de domínio público.
1.  A aplicação Gateway está num estado pouco saudável.

**Solução:**

1.  Verifique se o seu NSG está a bloquear o acesso às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da **Internet:**

    a.  No separador visão **geral** do Gateway da aplicação, selecione o link **Rede Virtual/Subnet.**

    b.  No separador **Subnets** da sua rede virtual, selecione a subnet onde o Application Gateway foi implementado.

    c.  Verifique se algum NSG está configurado.

    d.  Se um NSG estiver configurado, procure esse recurso NSG no separador **Search** ou em **todos os recursos**.

    e.  Na secção **Regras de Entrada,** adicione uma regra de entrada para permitir a gama de porta de destino 65503-65534 para v1 SKU ou 65200-65535 v2 SKU com a **Fonte** definida como **Qualquer** ou **Internet**.

    f.  Selecione **Guardar** e verificar se pode ver o backend como Saudável. Em alternativa, pode fazê-lo através [do PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Verifique se o seu UDR tem uma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet:**
    
    a.  Siga os passos 1a e 1b para determinar a sua sub-rede.

    b.  Verifique se há algum UDR configurado. Se houver, procure o recurso na barra de pesquisa ou em **todos os recursos.**

    c.  Verifique se existem rotas predefinidas (0.0.0.0/0) com o próximo salto não definido como **Internet**. Se a definição for **virtual Appliance** ou **Virtual Network Gateway,** certifique-se de que o seu aparelho virtual ou o dispositivo no local podem encaminhar corretamente o pacote de volta para o destino da Internet sem modificar o pacote.

    d.  Caso contrário, altere o próximo salto para a **Internet**, selecione **Save**, e verifique a saúde do backend.

1.  Rota padrão anunciada pela ligação ExpressRoute/VPN à rede virtual através do BGP:

    a.  Se tiver uma ligação ExpressRoute/VPN à rede virtual através do BGP, e se estiver a publicitar uma rota predefinida, deve certificar-se de que o pacote é encaminhado de volta para o destino da Internet sem o modificar. Pode verificar utilizando a opção **Connection Troubleshoot** no portal Application Gateway.

    b.  Escolha o destino manualmente como qualquer endereço IP de saída da Internet como 1.1.1.1. Desloque a porta de destino como qualquer coisa e verifique a conectividade.

    c.  Se o próximo salto for o gateway da rede virtual, pode haver uma rota padrão anunciada através do ExpressRoute ou VPN.

1.  Se houver um servidor DNS personalizado configurado na rede virtual, verifique se o servidor (ou servidores) pode resolver domínios públicos. A resolução de nomes de domínio público pode ser exigida em cenários em que o Gateway de Aplicação deve chegar a domínios externos como servidores OCSP ou para verificar o estado de revogação do certificado.

1.  Para verificar se o Application Gateway é saudável e em execução, vá à opção **DeSaúde** de Recursos no portal e verifique se o estado é **saudável.** Se vir um estado **pouco saudável** ou **degradado,** [suporte de contato](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Passos seguintes
----------

Saiba mais sobre [diagnósticos de Gateway de aplicação e exploração madeireira.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
