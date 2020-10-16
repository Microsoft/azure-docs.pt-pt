---
title: Problemas de saúde na Azure Application Gateway
description: Descreve como resolver problemas de saúde para a Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 531a7fd8547130b4897f3dad0900e1c27fb7fe9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132046"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemas de saúde na resolução de problemas de saúde no Gateway de Aplicação
==================================================

<a name="overview"></a>Descrição geral
--------

Por predefinição, o Azure Application Gateway sonda servidores backend para verificar o seu estado de saúde e para verificar se estão prontos para atender pedidos. Os utilizadores também podem criar sondas personalizadas para mencionar o nome do anfitrião, o caminho a ser sondado e os códigos de estado a serem aceites como Saudáveis. Em cada caso, se o servidor backend não responder com sucesso, o Gateway de Aplicação marca o servidor como Insalubre e deixa de encaminhar pedidos para o servidor. Depois de o servidor começar a responder com sucesso, o Application Gateway volta a encaminhar os pedidos.

### <a name="how-to-check-backend-health"></a>Como verificar a saúde backend

Para verificar a saúde da sua piscina de backend, pode utilizar a página **Backend Health** no portal Azure. Ou, pode utilizar [a Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)ou [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

O estado recuperado por qualquer um destes métodos pode ser qualquer um dos seguintes:

- Bom estado de funcionamento

- Mau estado de funcionamento

- Desconhecido

Se o estado de saúde de backend para um servidor for saudável, significa que o Application Gateway irá reencaminhar os pedidos para esse servidor. Mas se a saúde de backend para todos os servidores de um pool de backend não é saudável ou desconhecida, você pode encontrar problemas quando você tentar aceder a aplicações. Este artigo descreve os sintomas, a causa e a resolução de cada um dos erros mostrados.

<a name="backend-health-status-unhealthy"></a>Estado de saúde de backend: Insalubre
-------------------------------

Se o estado de saúde do backend não for saudável, a vista do portal assemelha-se à seguinte imagem:

![Saúde backend Application Gateway - Insalubre](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou se estiver a usar uma consulta Azure PowerShell, CLI ou Azure REST API, terá uma resposta que se assemelha ao seguinte:
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
Depois de receber um estado de backend insalubre para todos os servidores num pool de backend, os pedidos não são reencaminhados para os servidores, e o Application Gateway devolve um erro de "502 Bad Gateway" ao cliente que solicita. Para resolver este problema, consulte a coluna **Detalhes** no **separador Backend Health.**

A mensagem exibida na coluna **Detalhes** fornece informações mais detalhadas sobre o problema e, com base nelas, pode começar a resolver problemas.

> [!NOTE]
> O pedido de sonda predefinido é enviado no formato de \<protocol\> :127.0.0.1: \<port\> /. Por exemplo, http://127.0.0.1:80 para uma sonda http na porta 80. Apenas os códigos de estado HTTP de 200 a 399 são considerados saudáveis. O protocolo e a porta de destino são herdados a partir das definições HTTP. Se quiser que o Application Gateway sossegu um protocolo, nome de anfitrião ou caminho diferente e reconheça um código de estado diferente como Healthy, configufique uma sonda personalizada e associe-a às definições HTTP.

<a name="error-messages"></a>Mensagens de erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo limite de tempo do servidor backend

**Mensagem:** O tempo tomado pelo backend para responder à sonda de saúde do gateway de aplicação \' é mais do que o limiar de tempo na definição da sonda.

**Causa:** Depois de o Application Gateway enviar um pedido de sonda HTTP(S) para o servidor backend, aguarda uma resposta do servidor backend por um período configurado. Se o servidor backend não responder dentro do período configurado (o valor de tempo limite), está marcado como Insalubre até que comece a responder dentro do período de tempo configurado novamente.

**Resolução:** Verifique por que razão o servidor ou aplicação de backend não está a responder dentro do período de tempo configurado e verifique também as dependências da aplicação. Por exemplo, verifique se a base de dados tem algum problema que possa desencadear um atraso na resposta. Se tiver conhecimento do comportamento da aplicação e só responder após o valor do tempo limite, aumente o valor de tempo limite das definições personalizadas da sonda. Deve ter uma sonda personalizada para alterar o valor do tempo limite. Para obter informações sobre como configurar uma sonda personalizada, [consulte a página de documentação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Para aumentar o valor de tempo limite, siga estes passos:

1.  Aceda diretamente ao servidor backend e verifique o tempo necessário para que o servidor responda nessa página. Pode utilizar qualquer ferramenta para aceder ao servidor backend, incluindo um navegador utilizando ferramentas de desenvolvimento.

1.  Depois de ter descoberto o tempo necessário para a aplicação responder, selecione o separador **Sondas de Saúde** e, em seguida, selecione a sonda que está associada às definições HTTP.

1.  Introduza qualquer valor de tempo limite superior ao tempo de resposta da aplicação, em segundos.

1.  Guarde as definições personalizadas da sonda e verifique se a saúde do backend mostra-se saudável agora.

#### <a name="dns-resolution-error"></a>Erro de resolução do DNS

**Mensagem:** O Application Gateway não conseguiu criar uma sonda para este backend. Normalmente, esta situação acontece quando o FQDN do back-end não foi inserido corretamente. 

**Causa:** Se o pool de backend for do tipo IP Address/FQDN ou Serviço de Aplicações, o Gateway de aplicações resolve-se para o endereço IP do FQDN introduzido através do Sistema de Nome de Domínio (DNS) (padrão personalizado ou azure) e tenta ligar-se ao servidor na porta TCP mencionada nas Definições HTTP. Mas se esta mensagem for exibida, sugere que o Application Gateway não conseguiu resolver com sucesso o endereço IP do FQDN introduzido.

**Resolução:**

1.  Verifique se o FQDN introduzido na piscina de backend está correto e que é um domínio público e, em seguida, tente resolvê-lo a partir da sua máquina local.

1.  Se conseguir resolver o endereço IP, pode haver algo de errado com a configuração do DNS na rede virtual.

1.  Verifique se a rede virtual está configurada com um servidor DNS personalizado. Se for, verifique o servidor DNS sobre o porquê de não conseguir resolver o endereço IP do FQDN especificado.

1.  Se estiver a utilizar o DNS predefinido do Azure, verifique com o seu registo de nome de domínio se foi concluído um registo adequado ou um mapeamento de registo CNAME.

1.  Se o domínio for privado ou interno, tente resolvê-lo a partir de um VM na mesma rede virtual. Se conseguir resolvê-lo, reinicie o Gateway de Aplicação e verifique novamente. Para reiniciar o Gateway de Aplicações, tem de [parar](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [começar](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) a utilizar os comandos PowerShell descritos nestes recursos ligados.

#### <a name="tcp-connect-error"></a>Erro de ligação TCP

**Mensagem:** O Gateway de Aplicação não conseguiu ligar-se ao backend.
Verifique se o backend responde na porta utilizada para a sonda.
Verifique também se algum NSG/UDR/Firewall está bloqueando o acesso ao Ip e à porta deste backend

**Causa:** Após a fase de resolução do DNS, o Application Gateway tenta ligar-se ao servidor backend na porta TCP que está configurado nas definições HTTP. Se o Application Gateway não conseguir estabelecer uma sessão de TCP na porta especificada, a sonda está marcada como Insalubre com esta mensagem.

**Solução:** Se receber este erro, siga estes passos:

1.  Verifique se pode ligar-se ao servidor backend na porta mencionada nas definições HTTP utilizando um browser ou PowerShell. Por exemplo, executar o seguinte comando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se a porta mencionada não for a porta desejada, insira o número de porta correto para o Gateway de aplicação para ligar ao servidor backend

1.  Se também não conseguir ligar a porta da sua máquina local, então:

    a.  Verifique as definições do grupo de segurança da rede (NSG) do adaptador de rede e sub-rede do servidor de backend e se são permitidas ligações de entrada à porta configurada. Se não estiverem, crie uma nova regra para permitir as ligações. Para aprender a criar regras NSG, [consulte a página de documentação](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verifique se as definições NSG da sub-rede Application Gateway permitem tráfego público e privado de saída, para que possa ser feita uma ligação. Consulte a página de documentos fornecida no passo 3a para saber mais sobre como criar regras NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as definições de rotas definidas pelo utilizador (UDR) do Gateway de Aplicação e da sub-rede do servidor de backend para eventuais anomalias de encaminhamento. Certifique-se de que o UDR não está a direcionar o tráfego para longe da sub-rede de backend. Por exemplo, verifique se as rotas para aparelhos virtuais de rede ou rotas predefinidas estão a ser publicitadas na sub-rede Do Gateway de Aplicações via Azure ExpressRoute e/ou VPN.

    d.  Para verificar as rotas e regras eficazes de um adaptador de rede, pode utilizar os seguintes comandos PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se não encontrar problemas com NSG ou UDR, verifique o seu servidor de backend para problemas relacionados com a aplicação que estão a impedir os clientes de estabelecer uma sessão de TCP nas portas configuradas. Algumas coisas para verificar:

    a.  Abra uma solicitação de comando (Win+R - \> cmd), insira `netstat` e selecione Enter.

    b.  Verifique se o servidor está a ouvir na porta que está configurada. Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se não estiver a ouvir na porta configurada, verifique as definições do servidor web. Por exemplo: encadernações do site no IIS, bloco de servidor em NGINX e hospedeiro virtual em Apache.

    d.  Verifique as definições da firewall do SO para se certificar de que é permitido o tráfego de entrada na porta.

#### <a name="http-status-code-mismatch"></a>Desajuste do código de estado HTTP

**Mensagem:** O código de estado da resposta HTTP do backend \' não corresponde à definição da sonda. Esperado:{HTTPStatusCode0} Recebido:{HTTPStatusCode1}.

**Causa:** Depois de estabelecida a ligação TCP e de um aperto de mão TLS (se o TLS estiver ativado), o Application Gateway enviará a sonda como um pedido HTTP GET para o servidor backend. Como descrito anteriormente, a sonda padrão será de \<protocol\> 127.0.0.1: \<port\> /, e considera os códigos de estado de resposta na raiva 200 a 399 como Saudável. Se o servidor devolver qualquer outro código de estado, será marcado como Insalubre com esta mensagem.

**Solução:** Dependendo do código de resposta do servidor de backend, pode dar os seguintes passos. Alguns dos códigos de estado comum estão listados aqui:

| **Erro** | **Ações** |
| --- | --- |
| Desajuste do código da sonda: Recebido 401 | Verifique se o servidor backend requer autenticação. As sondas Application Gateway não podem passar credenciais para autenticação. Ou permite \" HTTP 401 \" numa correspondência de código de sonda ou sonda para um caminho onde o servidor não necessita de autenticação. | |
| Desajuste do código da sonda: Recebido 403 | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor backend. | |
| Desajuste do código da sonda: Recebido 404 | Página não encontrada. Verifique se o caminho do nome do anfitrião está acessível no servidor backend. Altere o nome do anfitrião ou o parâmetro do caminho para um valor acessível. | |
| Desajuste do código da sonda: Recebido 405 | Os pedidos de sonda para o Gateway de Aplicação utilizam o método HTTP GET. Verifique se o seu servidor permite este método. | |
| Desajuste do código da sonda: Recebido 500 | Erro interno do servidor. Verifique a saúde do servidor de backend e se os serviços estão em funcionamento. | |
| Desajuste do código da sonda: Recebido 503 | Serviço indisponível. Verifique a saúde do servidor de backend e se os serviços estão em funcionamento. | |

Ou, se acha que a resposta é legítima e quer que o Application Gateway aceite outros códigos de estado como Healthy, pode criar uma sonda personalizada. Esta abordagem é útil em situações em que o site backend precisa de autenticação. Como os pedidos da sonda não têm credenciais de utilizador, falharão e um código de estado HTTP 401 será devolvido pelo servidor backend.

Para criar uma sonda personalizada, siga [estes passos.](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)

#### <a name="http-response-body-mismatch"></a>Incompatibilidade do corpo de resposta HTTP

**Mensagem:** O corpo da resposta HTTP do backend \' não corresponde à definição da sonda. O corpo de resposta recebido não contém {string}.

**Causa:** Quando cria uma sonda personalizada, tem a opção de marcar um servidor de backend como Healthy, combinando uma corda do corpo de resposta. Por exemplo, pode configurar o Application Gateway para aceitar "não autorizado" como uma corda para combinar. Se a resposta do servidor de backend para o pedido da sonda contiver o fio **não autorizado,** será marcado como Saudável. Caso contrário, será marcado como Insalubre com esta mensagem.

**Solução:** Para resolver esta questão, siga estes passos:

1.  Aceda ao servidor de backend localmente ou a partir de uma máquina de cliente no caminho da sonda, e verifique o corpo de resposta.

1.  Verifique se o corpo de resposta na configuração da sonda personalizada Application Gateway corresponde ao que está configurado.

1.  Se não corresponderem, altere a configuração da sonda de modo a que tenha o valor correto da cadeia a aceitar.

Saiba mais sobre [a correspondência da sonda Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

>[!NOTE]
> Para todas as mensagens de erro relacionadas com TLS, para saber mais sobre o comportamento do SNI e as diferenças entre o V1 e v2 SKU, consulte a página geral do [TLS.](ssl-overview.md)


#### <a name="backend-server-certificate-invalid-ca"></a>Certificado de servidor de backend inválido CA

**Mensagem:** O certificado de servidor utilizado pelo backend não é assinado por uma conhecida Autoridade de Certificados (CA). Permitir o backend no Gateway de aplicações carregando o certificado raiz do certificado de servidor utilizado pelo backend.

**Causa:** SSL de ponta a ponta com Application Gateway v2 requer que o certificado do servidor backend seja verificado de forma a considerar o servidor saudável.
Para que seja confiável um certificado TLS/SSL, esse certificado do servidor backend deve ser emitido por um CA incluído na loja fidedigna do Application Gateway. Se o certificado não foi emitido por uma AC fidedigna (por exemplo, se um certificado auto-assinado foi usado), os utilizadores devem carregar o certificado do emitente para o Gateway de Aplicação.

**Solução:** Siga estes passos para exportar e carreça o certificado de raiz fidedigno para o Application Gateway. (Estes passos são para clientes Windows.)

1.  Inscreva-se na máquina onde a sua aplicação está hospedada.

1.  Selecione Win+R ou clique à direita no botão **Iniciar** e, em seguida, selecione **Executar**.

1.  `certmgr.msc`Insira e selecione Entrar. Também pode pesquisar por Certificate Manager no menu **Iniciar.**

1.  Localize o certificado, normalmente `\Certificates - Current User\\Personal\\Certificates\` em, e abra-o.

1.  Selecione o certificado de raiz e, em seguida, **selecione Ver Certificado**.

1.  Nas propriedades do Certificado, selecione o separador **Detalhes.**

1.  No separador **Detalhes,** selecione a opção **Copiar para Arquivar** e guarde o ficheiro no X.509 codificado base-64 (. Formato CER).

1.  Abra a página **de definições** http do Gateway de Aplicação no portal Azure.

1. Abra as definições HTTP, **selecione Adicionar Certificado**e localize o ficheiro de certificado que acabou de guardar.

1. **Selecione Guardar** para guardar as definições HTTP.

Em alternativa, pode exportar o certificado raiz de uma máquina cliente, acedendo diretamente ao servidor (bypass Application Gateway) através do navegador e exportando o certificado raiz do navegador.

Para obter mais informações sobre como extrair e carregar certificados de raiz fidedignos no Gateway de aplicação, consulte [o certificado de raiz fidedigno de exportação (para v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Incompatibilidade de certificado de raiz fidedigno

**Mensagem:** O certificado de raiz do certificado de servidor utilizado pelo backend não corresponde ao certificado raiz fidedigno adicionado ao gateway de aplicação. Certifique-se de que adiciona o certificado de raiz correto à lista de backend

**Causa:** SSL de ponta a ponta com Application Gateway v2 requer que o certificado do servidor backend seja verificado de forma a considerar o servidor saudável.
Para que seja confiável um certificado TLS/SSL, o certificado de backend servidor deve ser emitido por um CA incluído na loja fidedigna do Gateway de Aplicações. Se o certificado não foi emitido por uma AC fidedigna (por exemplo, foi utilizado um certificado auto-assinado), os utilizadores devem enviar o certificado do emitente para o Gateway de Aplicação.

O certificado que foi enviado para as definições do Gateway HTTP da Aplicação deve corresponder ao certificado raiz do certificado do servidor backend.

**Solução:** Se receber esta mensagem de erro, existe uma incompatibilidade entre o certificado que foi enviado para o Application Gateway e o que foi enviado para o servidor de backend.

Siga os passos 1-11 no método anterior para carregar o certificado de raiz de confiança correto para o Gateway de Aplicação.

Para obter mais informações sobre como extrair e carregar certificados de raiz fidedignos no Gateway de aplicação, consulte [o certificado de raiz fidedigno de exportação (para v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Este erro também pode ocorrer se o servidor backend não trocar a cadeia completa do certificado, incluindo o Root > Intermediate (se aplicável) > Leaf durante o aperto de mão TLS. Para verificar, pode utilizar comandos OpenSSL a partir de qualquer cliente e ligar-se ao servidor backend utilizando as definições configuradas na sonda 'Gateway' de aplicação.

Por exemplo:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar a cadeia completa do certificado a ser devolvida, exporte novamente o certificado com a cadeia completa, incluindo o certificado raiz. Configure esse certificado no seu servidor backend. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Certificado de backend nome comum inválido (CN)

**Mensagem:** O Nome Comum (CN) do certificado de backend não corresponde ao cabeçalho do anfitrião da sonda.

**Causa:** O Gateway de Aplicação verifica se o nome do anfitrião especificado nas definições HTTP de backend corresponde à do CN apresentado pelo certificado TLS/SSL do servidor de backend. Este é Standard_v2 e WAF_v2 comportamento SKU (V2). A indicação de nome do servidor (SNI) standard e waf SKU (v1) é definida como FQDN no endereço de pool backend. Para obter mais informações sobre o comportamento do SNI e as diferenças entre v1 e v2 SKU, consulte [a visão geral da rescisão de TLS e o fim do TLS com o Gateway de aplicação](ssl-overview.md).

No V2 SKU, se houver uma sonda padrão (nenhuma sonda personalizada foi configurada e associada), o SNI será definido a partir do nome de anfitrião mencionado nas definições HTTP. Ou, se "Escolher o nome do anfitrião do endereço de backend" for mencionado nas definições HTTP, onde o pool de endereços de backend contém um FQDN válido, esta definição será aplicada.

Se houver uma sonda personalizada associada às definições HTTP, o SNI será definido a partir do nome de anfitrião mencionado na configuração da sonda personalizada. Ou, se **escolher o nome de anfitrião das definições HTTP de backend** é selecionado na sonda personalizada, o SNI será definido a partir do nome de anfitrião mencionado nas definições HTTP.

Se **o nome de anfitrião da escolha do endereço de backend** estiver definido nas definições HTTP, o pool de endereços de backend deve conter um FQDN válido.

Se receber esta mensagem de erro, o CN do certificado de backend não corresponde ao nome de anfitrião configurado na sonda personalizada ou nas definições HTTP (se **escolher o nome de anfitrião das definições HTTP backend).** Se estiver a utilizar uma sonda predefinida, o nome de anfitrião será definido como **127.0.0.1**. Se não for um valor desejado, deverá criar uma sonda personalizada e associá-la às definições HTTP.

**Solução:**

Para resolver o problema, siga estes passos.

Para Windows:

1.  Inscreva-se na máquina onde a sua aplicação está hospedada.

1.  Selecione Win+R ou clique no botão **Iniciar** e selecione **Executar**.

1.  Introduza **certmgr.msc** e selecione Enter. Também pode pesquisar por Certificate Manager no menu **Iniciar.**

1.  Localize o certificado (normalmente `\Certificates - Current User\\Personal\\Certificates` em), e abra o certificado.

1.  No separador **Detalhes,** consulte o **certificado Subject .**

1.  Verifique o CN do certificado a partir dos detalhes e introduza o mesmo no campo de nome de anfitrião da sonda personalizada ou nas definições HTTP (se escolher o **nome de anfitrião das definições HTTP backend).** Se este não for o nome de anfitrião pretendido para o seu website, tem de obter um certificado para esse domínio ou introduzir o nome de anfitrião correto na configuração de configuração de sonda personalizada ou HTTP.

Para o Linux utilizar o OpenSSL:

1.  Executar este comando em OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A partir das propriedades apresentadas, encontre o CN do certificado e introduza o mesmo no campo de nome de anfitrião das definições http. Se este não for o nome de anfitrião pretendido para o seu website, tem de obter um certificado para esse domínio ou introduzir o nome de anfitrião correto na configuração de configuração de sonda personalizada ou HTTP.

#### <a name="backend-certificate-is-invalid"></a>O certificado de backend é inválido

**Mensagem:** O certificado de backend é inválido. A data atual não está dentro \" do intervalo Válido de e Válido até à data no \" \" \" certificado.

**Causa:** Cada certificado vem com um intervalo de validade, e a ligação HTTPS não será segura a menos que o certificado TLS/SSL do servidor seja válido. Os dados atuais devem estar dentro **do válido e** válido **ao** intervalo. Se não for, o certificado é considerado inválido, e isso criará um problema de segurança no qual o Application Gateway marca o servidor backend como Insalubre.

**Solução:** Se o seu certificado TLS/SSL tiver expirado, renove o certificado com o seu fornecedor e atualize as definições do servidor com o novo certificado. Se for um certificado auto-assinado, deve gerar um certificado válido e carregar o certificado raiz para as definições de Gateway HTTP. Para tal, siga estes passos:

1.  Abra as definições de Gateway HTTP no portal.

1.  Selecione a definição que tem o certificado caducado, **selecione Add Certificate**e abra o novo ficheiro de certificado.

1.  Remova o certificado antigo utilizando o ícone **Eliminar** ao lado do certificado e, em seguida, selecione **Guardar**.

#### <a name="certificate-verification-failed"></a>Verificação do certificado falhada

**Mensagem:** A validade do certificado de backend não pôde ser verificada. Para descobrir a razão, consulte os diagnósticos OpenSSL para a mensagem associada ao código de erro {errorCode}

**Causa:** Este erro ocorre quando o Gateway de Aplicação não pode verificar a validade do certificado.

**Solução:** Para resolver este problema, verifique se o certificado no seu servidor foi criado corretamente. Por exemplo, pode utilizar [o OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e as suas propriedades e, em seguida, tentar recarregar o certificado para as definições do Gateway HTTP.

<a name="backend-health-status-unknown"></a>Estado de saúde de backend: desconhecido
-------------------------------
Se a saúde de backend for mostrada como Desconhecida, a vista do portal assemelhar-se-á à seguinte imagem:

![Saúde backend Do Gateway de aplicação - Desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Este comportamento pode ocorrer por uma ou mais das seguintes razões:

1.  A sub-rede NSG na sub-rede Application Gateway está a bloquear o acesso de entrada às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) de "Internet".
1.  A UDR na sub-rede 'Gateway' de aplicação está definida para a rota predefinida (0.0.0.0/0) e o próximo lúpulo não é especificado como "Internet".
1.  A rota padrão é anunciada por uma ligação ExpressRoute/VPN a uma rede virtual sobre o BGP.
1.  O servidor DNS personalizado está configurado numa rede virtual que não consegue resolver nomes de domínio público.
1.  O Application Gateway está num estado pouco saudável.

**Solução:**

1.  Verifique se o seu NSG está a bloquear o acesso às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da **Internet:**

    a.  No separador **'Visão Geral'** do Gateway de Aplicação, selecione a ligação **Rede Virtual/Sub-rede.**

    b.  No separador **Subnets** da sua rede virtual, selecione a sub-rede onde o Gateway de Aplicação foi implantado.

    c.  Verifique se algum NSG está configurado.

    d.  Se um NSG estiver configurado, procure o recurso NSG no separador **Procurar** ou sob **todos os recursos**.

    e.  Na secção **Regras de Entrada,** adicione uma regra de entrada para permitir a gama de portos de destino 65503-65534 para v1 SKU ou 65200-65535 v2 SKU com o conjunto **de fontes** como **Qualquer** ou **Internet**.

    f.  **Selecione Guardar** e verifique se pode ver o backend como Saudável. Em alternativa, pode fazê-lo através [do PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Verifique se o seu UDR tem uma rota predefinido (0.0.0.0/0) com o próximo salto não definido como **Internet**:
    
    a.  Siga os passos 1a e 1b para determinar a sua sub-rede.

    b.  Verifique se há algum UDR configurado. Se houver, procure o recurso na barra de pesquisa ou sob **todos os recursos**.

    c.  Verifique se existem rotas predefinidos (0.0.0.0/0) com o próximo salto não definido como **Internet**. Se a definição for **ou Aparelho Virtual** ou Gateway de Rede **Virtual,** certifique-se de que o seu aparelho virtual ou o dispositivo no local podem encaminhar corretamente o pacote de volta para o destino da Internet sem modificar o pacote.

    d.  Caso contrário, altere o próximo salto para a **Internet**, selecione **Save**, e verifique a saúde do backend.

1.  Rota padrão anunciada pela ligação ExpressRoute/VPN à rede virtual sobre o BGP:

    a.  Se tiver uma ligação ExpressRoute/VPN à rede virtual através do BGP, e se estiver a publicitar uma rota padrão, deve certificar-se de que o pacote é encaminhado de volta para o destino da Internet sem modificá-lo. Pode verificar utilizando a opção **'Resolução de Problemas de Ligação'** no portal 'Gateway' de aplicação.

    b.  Escolha o destino manualmente como qualquer endereço IP de encaminhamento de internet como 1.1.1.1. Desa estale a porta de destino como qualquer coisa, e verifique a conectividade.

    c.  Se o próximo salto for o gateway de rede virtual, pode haver uma rota padrão anunciada sobre ExpressRoute ou VPN.

1.  Se houver um servidor DNS personalizado configurado na rede virtual, verifique se o servidor (ou servidores) pode resolver domínios públicos. A resolução do nome de domínio público pode ser necessária em cenários em que o Gateway de Aplicação deve chegar a domínios externos, como servidores OCSP ou para verificar o estado de revogação do certificado.

1.  Para verificar se o Gateway de Aplicação está saudável e em funcionamento, vá à opção **Saúde de Recursos** no portal e verifique se o estado é **saudável.** Se vir um estado **insalubre** ou **degradado,** [contacte o suporte](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Passos seguintes
----------

Saiba mais sobre [diagnósticos e registos de gateway de aplicações.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
