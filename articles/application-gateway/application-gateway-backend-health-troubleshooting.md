---
title: Solucionar problemas de integridade de back-end no gateway Aplicativo Azure
description: Este artigo orienta você na solução de problemas de integridade de back-end para Aplicativo Azure gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097584"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Solucionar problemas de integridade de back-end no gateway de aplicativo
==================================================

<a name="overview"></a>Descrição geral
--------

O gateway de aplicativo, por padrão, investiga os servidores de back-end para verificar seu status de integridade e se eles estão prontos para atender a solicitações. Os usuários podem criar investigações personalizadas também para mencionar o nome do host, o caminho a ser investigado e os códigos de status a serem aceitos como íntegros. Em ambos os casos, se o servidor back-end não estiver respondendo com êxito, o gateway de aplicativo marcará o servidor como não íntegro e interromperá o encaminhamento da solicitação para o servidor. Depois que o servidor começar a responder com êxito, ele continuará atendendo as solicitações.

### <a name="how-to-check-backend-health"></a>Como verificar a integridade do back-end

Para verificar a integridade do pool de back-end, você pode usar a página "integridade de back-end" no portal do Azure. Ou você pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)ou [API REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) para obter o status de integridade. Verifique o artigo vinculado em relação a cada método para obter mais informações.

O status recuperado por qualquer método mencionado acima pode ser de três tipos, que são:

1.  Bom estado de funcionamento

2.  Estado de funcionamento incorreto

3.  Desconhecido

Se o status de integridade de back-end para um servidor estiver íntegro, significa que o gateway de aplicativo encaminhará as solicitações para esse servidor. Mas se a integridade do back-end para todos os servidores em um pool de back-end não estiver íntegra ou for desconhecida, você poderá enfrentar alguns problemas durante o acesso ao aplicativo. Este documento descreve o sintoma, a causa e a solução de cada um dos erros mostrados quando os servidores back-end podem ser não íntegros ou desconhecidos.

<a name="backend-health-status-unhealthy"></a>Status de integridade de back-end não íntegro
-------------------------------

Se o status de integridade do back-end for mostrado como não íntegro, você o verá no portal, como a captura de tela abaixo:

![Integridade de back-end do gateway de aplicativo-não íntegro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou, se você estiver usando a consulta Azure PowerShell, CLI ou API REST do Azure, verá uma resposta semelhante à seguinte:
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
Quando você vir o status do servidor back-end como não íntegro para todos os servidores em um pool de back-end, as solicitações não serão encaminhadas para eles e o gateway de aplicativo retornará 502 erro de gateway inadequado para o cliente solicitante. Para solucionar o problema, verifique a coluna detalhes da guia integridade de back-end.

A mensagem exibida na coluna detalhes da guia integridade de back-end fornece informações mais detalhadas sobre o problema e, com base neles, podemos começar a solucionar o problema.

> [!NOTE]
> A solicitação de investigação padrão é enviada no formato de \<protocolo\>://127.0.0.1:\<Port\>/, por exemplo, <http://127.0.0.1/> para uma investigação http na porta 80 e considera apenas uma resposta dos códigos de status HTTP 200-399 como resposta íntegra. O protocolo e a porta de destino são herdados das configurações de HTTP. Se você quiser que o gateway de aplicativo teste em um protocolo, nome de host ou caminho diferente e aceite um código de status diferente como íntegro, configure uma investigação personalizada e associe-a às configurações de HTTP.

<a name="error-messages"></a>Mensagens de Erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo limite do servidor de back-end

**Mensagem:** O tempo gasto pelo back-end para responder à\'investigação de integridade do gateway de aplicativo é maior do que o limite de tempo limite na configuração de investigação.

**Faz** Depois que o gateway de aplicativo envia uma solicitação de investigação HTTP (S) para o servidor de back-end, ele aguarda a resposta do servidor back-end por um determinado período de tempo configurado. Se o servidor back-end não estiver respondendo dentro do valor de tempo limite, ele será marcado como não íntegro até que comece a responder dentro do tempo limite novamente.

**Resolução:** Verifique no servidor back-end ou no aplicativo por que ele não está respondendo dentro do tempo limite configurado e verifique as dependências do aplicativo também, por exemplo, se o banco de dados tiver algum problema, o que pode levar ao atraso em resposta. Se você estiver ciente do comportamento do aplicativo e ele deve responder somente após o valor de tempo limite, aumente o valor de tempo limite das configurações de investigação personalizadas.
Você precisa ter uma investigação personalizada para alterar o valor do tempo limite. Se você quiser saber como configurar uma investigação personalizada, [consulte a página de documentação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Para aumentar o tempo limite, siga as etapas abaixo:

1.  Acesse o servidor back-end diretamente e verifique o tempo necessário para que o servidor responda nessa página. Você pode usar qualquer ferramenta para acessar o, incluindo um navegador usando as ferramentas de desenvolvedor.

2.  Depois de descobrir o tempo necessário para que o aplicativo responda, clique na guia investigações de integridade e selecione a investigação que está associada às suas configurações de HTTP.

3.  Insira um valor maior de tempo limite do que o tempo de resposta do aplicativo, em segundos.

4.  Salve as configurações de investigação personalizadas e verifique a integridade do back-end se ele mostrar íntegro agora.

#### <a name="dns-resolution-error"></a>Erro de resolução de DNS

**Mensagem:** O gateway de aplicativo não pôde criar uma investigação para este back-end. Isso geralmente acontece quando o FQDN do back-end não foi inserido corretamente. 

**Faz** Se o pool de back-end for do tipo endereço IP/FQDN ou serviço de aplicativo, o gateway de aplicativo será resolvido para o endereço IP do FQDN inserido usando o servidor DNS (padrão do Azure ou personalizado) e tentará se conectar ao servidor na porta TCP mencionada nas configurações de HTTP. Mas se essa mensagem for exibida, ela sugere que o Application Gateway não conseguiu resolver com êxito o endereço IP do FQDN inserido.

**Resolução:**

1.  Verifique se o FQDN inserido no pool de back-end está correto e, se for um domínio público, tente resolvê-lo do computador local.

2.  Se você puder resolver o endereço IP, pode haver algo errado com a configuração de DNS na VNet.

3.  Verifique se a VNet está configurada com um servidor DNS personalizado. Nesse caso, verifique no servidor DNS por que ele não está sendo capaz de resolver para o endereço IP do FQDN fornecido.

4.  Se for o DNS padrão do Azure, verifique com seu registrador de nome de domínio se for apropriado um registro ou mapeamento de registro CNAME.

5.  Se o domínio for privado/interno, tente resolvê-lo de uma VM na mesma VNet e, se for possível resolvê-lo, reinicie o gateway de aplicativo e verifique novamente. Para reiniciar o gateway de aplicativo, você precisa [parar](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [começar](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) a usar os comandos do PowerShell fornecidos nos respectivos links de documento.

#### <a name="tcp-connect-error"></a>Erro de conexão TCP

**Mensagem:** O gateway de aplicativo não pôde se conectar ao back-end.
Verifique se o back-end responde na porta usada para a investigação.
Verifique também se algum NSG/UDR/firewall está bloqueando o acesso ao IP e à porta desse back-end

**Faz** Após a fase de resolução DNS, o gateway de aplicativo tenta se conectar ao servidor de back-end na porta TCP configurada nas configurações de HTTP. Se o gateway de aplicativo não puder estabelecer uma sessão TCP na porta especificada, a investigação será marcada como não íntegra com essa mensagem.

**Solução:** Se você vir esse erro, verifique o seguinte:

1.  Verifique se você pode se conectar ao servidor de back-end na porta mencionada nas configurações de HTTP usando um navegador ou usando o PowerShell, por exemplo, você pode usar o comando: Test-NetConnection-ComputerName www.bing.com-Port 443

2.  Se a porta mencionada não for a porta desejada, insira o número da porta correto para que o gateway de aplicativo se conecte ao servidor de back-end

3.  Se você não puder se conectar na porta do computador local também, então:

    a.  Verifique as configurações de NSG da NIC e da sub-rede do servidor de back-end e verifique se as conexões de entrada para a porta configurada são permitidas. Se não forem permitidas, crie uma nova regra para permitir as conexões. Para saber como criar regras do NSG, [consulte a página de documentação](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verifique as configurações de NSG da sub-rede do gateway de aplicativo se o tráfego de saída público e privado for permitido, para que a conexão possa ser feita. Verifique o documento vinculado (a) para saber mais sobre como criar regras NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as configurações de UDR da sub-rede do servidor de back-end e do gateway de aplicativo para quaisquer anomalias de roteamento. Verifique se o UDR não está direcionando o tráfego para fora da sub-rede de back-end. Por exemplo, verifique se há rotas para dispositivos virtuais de rede ou rotas padrão sendo anunciadas para a sub-rede do gateway de aplicativo via ExpressRoute/VPN.

    d.  Para verificar as rotas e regras em vigor para uma NIC, você pode usar os comandos do PowerShell a seguir.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Se você não encontrar problemas com NSG ou UDR, verifique o servidor back-end para problemas relacionados ao aplicativo devido a quais clientes não podem estabelecer uma sessão TCP nas portas configuradas. Algumas coisas a serem verificadas:

    e.  Abra o prompt de comando (Win +\> R-cmd) e insira netstat e pressione Enter

    f.  Verifique se o servidor está escutando na porta configurada.
        Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Se não estiver, verifique as configurações do servidor Web, por exemplo, associações de site no IIS, bloco de servidor em NGINX e host virtual no Apache

    h.  Verifique as configurações de firewall do so para certificar-se de que o tráfego de entrada para a porta seja permitido

#### <a name="http-status-code-mismatch"></a>Incompatibilidade de código de status HTTP

**Mensagem:** O código de status da\'resposta http s de back-end não correspondeu à configuração de investigação. Esperado: {HTTPStatusCode0} recebido: {HTTPStatusCode1}.

**Faz** Depois que a conexão TCP tiver sido estabelecida e o handshake SSL for concluído (se o SSL estiver habilitado), o gateway de aplicativo enviará a investigação como uma solicitação HTTP GET para o servidor de back-end. Conforme mencionado acima, a \<investigação padrão será o protocolo\>://127.0.0.1:\<Port\>/e considerará os códigos de status de resposta no Rage 200-399 como íntegro. Se o servidor retornar qualquer outro código de status, ele será marcado como não íntegro com essa mensagem.

**Solução:** Dependendo do código de resposta do servidor de back-end, você pode executar as etapas a seguir. Alguns dos códigos de status comuns estão listados aqui:

| **Ao** | **Ações** |
| --- | --- |
| Incompatibilidade de código de status de investigação: 401 recebido | Verifique se o servidor back-end requer autenticação. As investigações do gateway de aplicativo não podem passar credenciais para autenticação neste momento. Permita que \"http 401\" em um código de status de investigação corresponda ou teste a um caminho em que o servidor não requer autenticação. | |
| Incompatibilidade de código de status de investigação: 403 recebido | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor back-end. | |
| Incompatibilidade de código de status de investigação: 404 recebido | Página não encontrada. Verifique o caminho do nome do host se ele estiver acessível no servidor back-end. Altere o parâmetro HostName ou Path para um valor acessível. | |
| Incompatibilidade de código de status de investigação: 405 recebido | As solicitações de investigação do gateway de aplicativo usam o método HTTP GET. Verifique se o servidor permite. | |
| Incompatibilidade de código de status de investigação: 500 recebido | Erro interno do servidor. Verifique a integridade do servidor back-end e se os serviços estão em execução. | |
| Incompatibilidade de código de status de investigação: 503 recebido | Serviço indisponível. Verifique a integridade do servidor back-end e se os serviços estão em execução. | |

Ou, se você acredita que a resposta é legit e deseja que o gateway de aplicativo aceite outros códigos de status como íntegros, você pode criar uma investigação personalizada. Modificar isso será útil em situações em que o site de back-end precisa de autenticação e uma vez que as solicitações de investigação não carregam credenciais de usuário, elas falharão e um código de status HTTP 401 será retornado pelo servidor de back-end.

Para criar uma investigação personalizada, siga as etapas listadas [aqui](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Incompatibilidade de corpo de resposta HTTP

**Mensagem:** O corpo da resposta\'http s de back-end não correspondeu à configuração de investigação. O corpo da resposta recebida não contém {String}.

**Faz** Ao criar uma investigação personalizada, você tem a opção de marcar um servidor de back-end íntegro combinando uma cadeia de caracteres do corpo da resposta. Por exemplo, você pode configurar o gateway de aplicativo para aceitar "não autorizado" como uma cadeia de caracteres para corresponder. Se a resposta do servidor back-end para a solicitação de investigação contiver a cadeia de caracteres "não autorizado", ela será marcada como íntegra.
Caso contrário, ele será marcado como não íntegro com esta mensagem.

**Solução:** Você pode resolver esse problema seguindo as etapas abaixo:

1.  Acesse o servidor back-end localmente ou em um computador cliente no caminho de investigação e verifique o corpo da resposta.

2.  Verifique a configuração de investigação personalizada do gateway de aplicativo para verificar se o corpo da resposta corresponde ao que está configurado.

3.  Se eles não corresponderem, altere a configuração de investigação com o valor de cadeia de caracteres correto para aceitar.

Você pode ler mais sobre a correspondência de investigação do gateway de aplicativo [aqui](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>AC inválida do certificado do servidor de back-end

**Mensagem:** O certificado do servidor usado pelo back-end não é assinado por uma autoridade de certificação (CA) conhecida. Lista de permissões do back-end no gateway de aplicativo, carregando o certificado raiz do certificado do servidor usado pelo back-end.

**Faz** O SSL de ponta a ponta com o gateway de aplicativo v2 exige que o certificado do servidor back-end seja verificado para considerar o servidor íntegro.
Para que um certificado SSL seja confiável, esse certificado do servidor back-end deve ter sido emitido por uma autoridade de certificação que está incluída no repositório confiável do gateway de aplicativo. Se o certificado não foi emitido por uma autoridade de certificação confiável, por exemplo, certificados autoassinados, os usuários devem carregar o certificado do emissor no gateway de aplicativo.

**Solução:** Siga as etapas abaixo para exportar e carregar o certificado raiz confiável para o gateway de aplicativo (as etapas fornecidas abaixo são para clientes Windows)

1.  Entre no computador onde seu aplicativo está hospedado

2.  Abra executar pressionando Win + R ou clicando com o botão direito do mouse no botão Iniciar e selecionando executar

3.  Digite certmgr. msc e pressione Enter. Você também pode pesquisar o Gerenciador de certificados no menu iniciar.

4.  Localize o certificado, normalmente em \'certificados – certificados\'pessoais\\\\do usuário atual e abra o certificado

5.  Nas propriedades do certificado, selecione a guia caminho de certificação

6.  Selecione o certificado raiz e selecione a opção "Exibir certificado"

7.  Nas propriedades do certificado, alterne para a guia detalhes

8.  Na guia detalhes, selecione a opção "copiar para arquivo" e salve o arquivo no X. 509 codificado em base-64 (. CER) formato

9.  Depois de salvo, abra a página Configurações de HTTP do gateway de aplicativo na portal do Azure

10. Abra as configurações de HTTP e clique em "Adicionar certificado" e localize o arquivo de certificado que salvamos recentemente

11. Clique em salvar para salvar as configurações de HTTP

Como alternativa, você pode exportar o certificado raiz de um computador cliente acessando diretamente o servidor (ignorando o gateway de aplicativo) usando um navegador e exportando o certificado raiz do navegador.

Para obter etapas detalhadas sobre como extrair e carregar certificados raiz confiáveis no gateway de aplicativo, consulte [aqui](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Incompatibilidade de certificado raiz confiável

**Mensagem:** O certificado raiz do certificado do servidor usado pelo back-end não corresponde ao certificado raiz confiável adicionado ao gateway de aplicativo. Certifique-se de adicionar o certificado raiz correto para a lista de permissões de back-end

**Faz** O SSL de ponta a ponta com o gateway de aplicativo v2 exige que o certificado do servidor back-end seja verificado para considerar o servidor íntegro.
Para que um certificado SSL seja confiável, esse certificado do servidor back-end deve ter sido emitido por uma autoridade de certificação que está incluída no repositório confiável do gateway de aplicativo. Se o certificado não foi emitido por uma autoridade de certificação confiável, por exemplo, certificados autoassinados, os usuários devem carregar o certificado do emissor no gateway de aplicativo.

O certificado que foi carregado para as configurações de HTTP do gateway de aplicativo deve corresponder ao certificado raiz do certificado do servidor de back-end.

**Solução:** Se você vir a mensagem de erro mencionada acima, significa que há uma incompatibilidade entre o certificado que foi carregado no gateway de aplicativo e o que foi carregado no servidor de back-end.

Siga as etapas 1-11 mencionadas acima para carregar o certificado raiz confiável correto para o gateway de aplicativo.

Para obter etapas detalhadas sobre como extrair e carregar certificados raiz confiáveis no gateway de aplicativo, consulte [aqui](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> O erro mencionado também pode ocorrer se o servidor back-end não estiver trocando a cadeia completa do certificado, incluindo a folha raiz > intermediária (se aplicável)-> durante o handshake TLS. Para verificar, você pode usar comandos do OpenSSL de qualquer cliente e conectar-se ao servidor de back-end usando as configurações definidas na investigação do gateway de aplicativo.

Por exemplo,
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar a cadeia completa do certificado que está sendo retornado, exporte o certificado novamente com a cadeia completa, incluindo o certificado raiz, e configure-o em seu servidor de back-end. 

CONECTADO (00000188) \
profundidade = 0 ou = controle de domínio validado, \*CN =. example.com \
erro de verificação: num = 20: não é possível obter o certificado do emissor local \
verificar retorno: 1 \
profundidade = 0 ou = controle de domínio validado, \*CN =. example.com \
verificar erro: num = 21: não é possível verificar o primeiro certificado \
verificar retorno: 1 \
\-\-\-\
Cadeia de certificados \
 0 s:/OU = controle de domínio validado/CN = *. example. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----INICIAR O-----DE CERTIFICADO \
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\----------DE CERTIFICADO FINAL

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comum inválido do certificado de back-end (CN)

**Mensagem:** O CN (nome comum) do certificado de back-end não corresponde ao cabeçalho de host da investigação.

**Faz** O gateway de aplicativo valida se o nome de host especificado na configuração http de back-end corresponde ao CN (Common Name) apresentado pelo certificado SSL do servidor back-end. Isso é apenas um comportamento de SKU Standard_v2 e WAF_v2. O SNI do SKU Standard e WAF é definido como o FQDN no endereço do pool de back-end.

Na SKU v2, se houver uma investigação padrão (sem investigação personalizada configurada/associada), a SNI será definida a partir do nome do host mencionado nas configurações de HTTP ou se "escolher nome do host do endereço de back-end" for mencionado nas configurações de HTTP, em que o pool de endereços de back-end contém um FQDN válido.

Se houver uma investigação personalizada associada às configurações de HTTP, o SNI será definido do nome de host mencionado na configuração de investigação personalizada ou, se "escolher nome do host das configurações de HTTP de back-end" estiver marcado na investigação personalizada, ele será definido do nome de host mencionado no HTTP Configurações.

No caso, "escolher nome do host do endereço de back-end" é definido nas configurações de HTTP, o pool de endereços de back-end deve conter um FQDN válido.

Se você vir a mensagem de erro mencionada acima, significa que o CN (nome comum) do certificado de back-end não corresponde ao hostname configurado na investigação personalizada ou as configurações de HTTP (caso contrário, "escolher nome do host das configurações de HTTP de back-end" é escolhido). Se você estiver usando uma investigação padrão, o nome do host será definido como "127.0.0.1". Se esse não for um valor desejado, você deverá criar uma investigação personalizada e associá-la às configurações de HTTP.

**Solução:**

Para resolver o problema, siga as etapas abaixo:

Para Windows:

1.  Entre no computador onde seu aplicativo está hospedado

2.  Abra executar pressionando Win + R ou clicando com o botão direito do mouse no botão Iniciar e selecionando executar

3.  Digite certmgr. msc e pressione Enter. Você também pode pesquisar o Gerenciador de certificados no menu iniciar.

4.  Localize o certificado, normalmente em \'certificados – certificados\'pessoais\\\\do usuário atual e abra o certificado

5.  Na guia detalhes, verifique o assunto do certificado

6.  Verifique o CN do certificado dos detalhes e insira o mesmo no campo nome do host da investigação personalizada ou as configurações de HTTP (no caso de "escolher nome do host das configurações de HTTP de back-end" for escolhido). Se esse não for o nome de host desejado para seu site, você deverá obter um certificado para esse domínio ou inserir o nome de host correto na configuração de configuração personalizada de investigação/http.

Para Linux usando OpenSSL

1.  Execute este comando no OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  Nas propriedades exibidas, localize o CN do certificado e insira o mesmo no campo nome do host das configurações de http. Se esse não for o nome de host desejado para seu site, você deverá obter um certificado para esse domínio ou inserir o nome de host correto na configuração de configuração personalizada de investigação/http.

#### <a name="backend-certificate-is-invalid"></a>O certificado de back-end é inválido

**Mensagem:** O certificado de back-end é inválido. A data atual não está dentro \"do intervalo\" válido \"de e\" válido até a data do certificado.

**Faz** Cada certificado vem com uma validade e a conexão HTTPS não será segura, a menos que o certificado SSL do servidor seja válido.
Os dados atuais devem estar dentro do intervalo válido de e válido para. Caso contrário, o certificado será considerado inválido e será uma preocupação de segurança. Neste ponto, o gateway de aplicativo marcará o servidor de back-end como não íntegro.

**Solução:** Se seu certificado SSL estiver expirado, renove o certificado com seu fornecedor e atualize as configurações do servidor com o novo certificado. Se for um certificado autoassinado, você precisará gerar um certificado válido e carregar o certificado raiz nas configurações de HTTP do gateway de aplicativo. Para fazer isso, siga as etapas abaixo:

1.  Abra as configurações de HTTP do gateway de aplicativo no portal

2.  Selecione as configurações de HTTP que têm o certificado expirado, selecione Adicionar certificado e abra o novo arquivo de certificado

3.  Remova o certificado antigo usando o ícone excluir ao lado do certificado e clique em salvar

#### <a name="certificate-verification-failed"></a>Falha na verificação do certificado

**Mensagem:** A validade do certificado de back-end não pôde ser verificada. Para descobrir o motivo, marque o diagnóstico de SSL aberto para a mensagem associada ao código de erro {errorCode}

**Faz** Esse erro ocorre quando o gateway de aplicativo não é capaz de verificar a validade do certificado.

**Solução:** Para resolver esse problema, verifique o certificado no servidor se ele estiver criado corretamente. Por exemplo, você pode usar o [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e suas propriedades e tentar recarregar o certificado nas configurações de http do gateway de aplicativo.

<a name="backend-health-status-unknown"></a>Status de integridade de back-end desconhecido
-------------------------------
Se a integridade do back-end for mostrada como desconhecida, ela será exibida no portal, como a captura de tela abaixo:

![Integridade de back-end do gateway de aplicativo-desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Caso a integridade do back-end seja mostrada como desconhecida, isso pode ser devido a um ou mais dos seguintes motivos:

1.  NSG na sub-rede do gateway de aplicativo está bloqueando o acesso de entrada às portas 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) de "Internet"
2.  UDR na sub-rede do gateway de aplicativo com rota padrão (0.0.0.0/0) com o próximo salto não como Internet
3.  Rota padrão anunciada pela conexão de ExpressRoute/VPN para a VNet por BGP
4.  O servidor DNS personalizado é configurado na VNet que não pode resolver nomes de domínio públicos
5.  O gateway de aplicativo está em um estado não íntegro

**Solução:**

1.  Verifique se o NSG está bloqueando o acesso às portas 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) de "Internet"

    a.  Na guia "visão geral" do gateway de aplicativo, selecione o link rede virtual/sub-rede

    b.  Na guia sub-redes da sua rede virtual, selecione a sub-rede na qual o gateway de aplicativo foi implantado

    c.  Verificar se há algum NSG configurado

    d.  Se houver um, procure por esse recurso NSG na guia Pesquisar ou em todos os recursos

    e.  Na seção regras de entrada, adicione uma regra de entrada para permitir o intervalo de portas de destino 65503-65534 para SKU v1 ou SKU 65200-65535 V2 com fonte como qualquer ou Internet

    f.  Clique em salvar e verifique se você pode exibir o back-end íntegro com êxito

    g.  Como alternativa, você pode fazer isso por meio do [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

2.  Verifique se o seu UDR está tendo uma rota padrão (0.0.0.0/0) com o próximo salto, não como Internet
    
    a.  Siga as etapas 1 a e 1. b para determinar sua sub-rede

    b.  Verifique se há algum UDR configurado. Se houver um, procure o recurso na barra de pesquisa ou em todos os recursos

    c.  Verifique se há rotas padrão (0.0.0.0/0) com o próximo salto, não como Internet. Se for um dispositivo virtual ou um gateway de rede virtual, você precisará certificar-se de que sua solução virtual ou o dispositivo local será capaz de rotear corretamente o pacote de volta para o destino da Internet sem modificar o pacote

    d.  Caso contrário, altere o próximo salto para a Internet, clique em salvar e verifique a integridade do back-end

3.  Rota padrão anunciada pela conexão de ExpressRoute/VPN para a VNet por BGP

    a.  Se você tiver uma conexão de ExpressRoute/VPN com a VNet por BGP e se estiver anunciando a rota padrão, você precisará certificar-se de que o pacote seja roteado de volta para o destino da Internet sem modificá-lo

    b.  Você pode verificar usando a opção "solução de problemas de conexão" no portal do gateway de aplicativo

    c.  Escolha o destino manualmente como qualquer endereço IP roteável da Internet como "1.1.1.1" e a porta de destino como qualquer coisa e verifique a conectividade.

    d.  Se o próximo salto for o gateway de rede virtual, poderá haver uma rota padrão anunciada via ExpressRoute ou VPN

4.  Se houver um servidor DNS personalizado configurado na VNet, verifique se os servidores podem resolver domínios públicos. A resolução de nome de domínio público pode ser necessária em cenários em que o gateway de aplicativo deve acessar domínios externos, como servidores OCSP, ou verificar o status de revogação do certificado, etc.

5.  Para verificar se o gateway de aplicativo está íntegro e em execução, vá para Resource Health opção no portal e verifique se ele é "íntegro". Se você estiver vendo o estado não íntegro ou degradado, [entre em contato com o suporte](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Próximos Passos
----------

Para saber mais sobre o diagnóstico e o log do gateway de aplicativo, consulte [aqui](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
