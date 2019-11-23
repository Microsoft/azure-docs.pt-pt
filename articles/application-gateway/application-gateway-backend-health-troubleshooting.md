---
title: Solucionar problemas de integridade de back-end no gateway Aplicativo Azure
description: Descreve como solucionar problemas de integridade de back-end para Aplicativo Azure gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001664"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Solucionar problemas de integridade de back-end no gateway de aplicativo
==================================================

<a name="overview"></a>Descrição geral
--------

Por padrão, Aplicativo Azure gateway investiga servidores back-end para verificar seu status de integridade e verificar se eles estão prontos para atender a solicitações. Os usuários também podem criar investigações personalizadas para mencionar o nome do host, o caminho a ser investigado e os códigos de status a serem aceitos como íntegros. Em cada caso, se o servidor back-end não responder com êxito, o gateway de aplicativo marcará o servidor como não íntegro e interromperá o encaminhamento das solicitações para o servidor. Depois que o servidor começa a responder com êxito, o gateway de aplicativo retoma o encaminhamento das solicitações.

### <a name="how-to-check-backend-health"></a>Como verificar a integridade do back-end

Para verificar a integridade do pool de back-end, você pode usar a página de **integridade de back-end** no portal do Azure. Ou, você pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)ou [API REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

O status recuperado por qualquer um desses métodos pode ser qualquer um dos seguintes:

- Bom estado de funcionamento

- Estado de funcionamento incorreto

- Desconhecido

Se o status de integridade do back-end de um servidor estiver íntegro, isso significará que o gateway de aplicativo encaminhará as solicitações para esse servidor. Mas se a integridade do back-end para todos os servidores em um pool de back-end não estiver íntegra ou for desconhecida, você poderá encontrar problemas ao tentar acessar aplicativos. Este artigo descreve os sintomas, a causa e a resolução de cada um dos erros mostrados.

<a name="backend-health-status-unhealthy"></a>Status de integridade de back-end: não íntegro
-------------------------------

Se o status de integridade do back-end não estiver íntegro, a exibição do portal será semelhante à captura de tela a seguir:

![Integridade de back-end do gateway de aplicativo-não íntegro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou, se você estiver usando uma consulta Azure PowerShell, CLI ou API REST do Azure, obterá uma resposta semelhante à seguinte:
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
Depois que você receber um status de servidor back-end não íntegro para todos os servidores em um pool de back-end, as solicitações não serão encaminhadas para os servidores e o gateway de aplicativo retornará um erro "502 gateway inadequado" para o cliente solicitante. Para solucionar esse problema, verifique a coluna **detalhes** na guia **integridade de back-end** .

A mensagem exibida na coluna **detalhes** fornece informações mais detalhadas sobre o problema e, com base neles, você pode começar a solucionar o problema.

> [!NOTE]
> A solicitação de investigação padrão é enviada no formato de \<protocolo\>://127.0.0.1:\<porta\>/. Por exemplo, http://127.0.0.1:80 para uma investigação http na porta 80. Somente os códigos de status HTTP de 200 a 399 são considerados íntegros. O protocolo e a porta de destino são herdados das configurações de HTTP. Se você quiser que o gateway de aplicativo teste em um protocolo diferente, nome de host ou caminho e reconheça um código de status diferente como íntegro, configure uma investigação personalizada e associe-a às configurações de HTTP.

<a name="error-messages"></a>Mensagens de erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo limite do servidor de back-end

**Mensagem:** O tempo gasto pelo back-end para responder à investigação de integridade do gateway de aplicativo\'s é maior que o limite de tempo limite na configuração de investigação.

**Causa:** Depois que o gateway de aplicativo envia uma solicitação de investigação HTTP (S) para o servidor de back-end, ele aguarda uma resposta do servidor back-end por um período configurado. Se o servidor back-end não responder dentro do período configurado (o valor de tempo limite), ele será marcado como não íntegro até que ele comece a responder dentro do período de tempo limite configurado novamente.

**Resolução:** Verifique por que o servidor ou aplicativo de back-end não está respondendo dentro do período de tempo limite configurado e verifique também as dependências do aplicativo. Por exemplo, verifique se o banco de dados tem algum problema que possa disparar um atraso em resposta. Se você estiver ciente do comportamento do aplicativo e ele deve responder somente após o valor de tempo limite, aumente o valor de tempo limite das configurações de investigação personalizadas. Você deve ter uma investigação personalizada para alterar o valor de tempo limite. Para obter informações sobre como configurar uma investigação personalizada, [consulte a página de documentação](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Para aumentar o valor de tempo limite, siga estas etapas:

1.  Acesse o servidor back-end diretamente e verifique o tempo necessário para que o servidor responda nessa página. Você pode usar qualquer ferramenta para acessar o servidor back-end, incluindo um navegador usando ferramentas de desenvolvedor.

1.  Depois de descobrir o tempo necessário para que o aplicativo responda, selecione a guia **investigações de integridade** e, em seguida, selecione a investigação associada às suas configurações de http.

1.  Insira qualquer valor de tempo limite maior que o tempo de resposta do aplicativo, em segundos.

1.  Salve as configurações de investigação personalizadas e verifique se a integridade do back-end é mostrada como íntegra agora.

#### <a name="dns-resolution-error"></a>Erro de resolução de DNS

**Mensagem:** O gateway de aplicativo não pôde criar uma investigação para este back-end. Normalmente, esta situação acontece quando o FQDN do back-end não foi inserido corretamente. 

**Causa:** Se o pool de back-end for do tipo endereço IP/FQDN ou serviço de aplicativo, o gateway de aplicativo será resolvido para o endereço IP do FQDN inserido por meio do DNS (sistema de nomes de domínio) (padrão do Azure ou personalizado) e tentará se conectar ao servidor na porta TCP mencionada nas configurações de HTTP. Mas se essa mensagem for exibida, ela sugere que o gateway de aplicativo não pôde resolver com êxito o endereço IP do FQDN inserido.

**Resolução:**

1.  Verifique se o FQDN inserido no pool de back-end está correto e se é um domínio público e tente resolvê-lo do computador local.

1.  Se você puder resolver o endereço IP, pode haver algo errado com a configuração de DNS na rede virtual.

1.  Verifique se a rede virtual está configurada com um servidor DNS personalizado. Se for, verifique o servidor DNS sobre por que ele não pode resolver para o endereço IP do FQDN especificado.

1.  Se você estiver usando o DNS padrão do Azure, verifique com seu registrador de nome de domínio se um registro ou mapeamento de registro CNAME adequado foi concluído.

1.  Se o domínio for privado ou interno, tente resolvê-lo de uma VM na mesma rede virtual. Se você puder resolvê-lo, reinicie o gateway de aplicativo e verifique novamente. Para reiniciar o gateway de aplicativo, você precisa [parar](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [Iniciar](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) usando os comandos do PowerShell descritos nesses recursos vinculados.

#### <a name="tcp-connect-error"></a>Erro de conexão TCP

**Mensagem:** O gateway de aplicativo não pôde se conectar ao back-end.
Verifique se o back-end responde na porta usada para a investigação.
Verifique também se algum NSG/UDR/firewall está bloqueando o acesso ao IP e à porta desse back-end

**Causa:** Após a fase de resolução DNS, o gateway de aplicativo tenta se conectar ao servidor de back-end na porta TCP configurada nas configurações de HTTP. Se o gateway de aplicativo não puder estabelecer uma sessão TCP na porta especificada, a investigação será marcada como não íntegra com essa mensagem.

**Solução:** Se você receber esse erro, siga estas etapas:

1.  Verifique se você pode se conectar ao servidor de back-end na porta mencionada nas configurações de HTTP usando um navegador ou o PowerShell. Por exemplo, execute o seguinte comando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se a porta mencionada não for a porta desejada, insira o número da porta correto para que o gateway de aplicativo se conecte ao servidor de back-end

1.  Se você não puder se conectar na porta do computador local também, então:

    a.  Verifique as configurações do NSG (grupo de segurança de rede) do adaptador de rede e da sub-rede do servidor de back-end e se as conexões de entrada para a porta configurada são permitidas. Se não forem, crie uma nova regra para permitir as conexões. Para saber como criar regras do NSG, [consulte a página de documentação](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verifique se as configurações de NSG da sub-rede do gateway de aplicativo permitem tráfego de saída público e privado, para que uma conexão possa ser feita. Verifique a página do documento fornecida na etapa 3a para saber mais sobre como criar regras NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as configurações de UDR (rotas definidas pelo usuário) do gateway de aplicativo e a sub-rede do servidor de back-end para quaisquer anomalias de roteamento. Verifique se o UDR não está direcionando o tráfego para fora da sub-rede de back-end. Por exemplo, verifique se há rotas para dispositivos virtuais de rede ou rotas padrão sendo anunciadas para a sub-rede do gateway de aplicativo por meio do Azure ExpressRoute e/ou VPN.

    d.  Para verificar as rotas e regras em vigor para um adaptador de rede, você pode usar os seguintes comandos do PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se você não encontrar problemas com NSG ou UDR, verifique o servidor back-end para problemas relacionados a aplicativos que estão impedindo clientes de estabelecer uma sessão TCP nas portas configuradas. Algumas coisas a serem verificadas:

    a.  Abra um prompt de comando (Win + R-\> cmd), insira `netstat`e selecione Enter.

    b.  Verifique se o servidor está escutando na porta configurada. Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se não estiver escutando na porta configurada, verifique as configurações do servidor Web. Por exemplo: associações de site no IIS, bloco de servidor em NGINX e host virtual no Apache.

    d.  Verifique as configurações de firewall do so para ter certeza de que o tráfego de entrada para a porta é permitido.

#### <a name="http-status-code-mismatch"></a>Incompatibilidade de código de status HTTP

**Mensagem:** O código de status da resposta HTTP do back-end\'s não correspondeu à configuração de investigação. Esperado: {HTTPStatusCode0} recebido: {HTTPStatusCode1}.

**Causa:** Depois que a conexão TCP tiver sido estabelecida e um handshake SSL for feito (se o SSL estiver habilitado), o gateway de aplicativo enviará a investigação como uma solicitação HTTP GET para o servidor de back-end. Conforme descrito anteriormente, a investigação padrão será \<protocolo\>://127.0.0.1:\<porta\>/e considera códigos de status de resposta no Rage 200 a 399 como íntegros. Se o servidor retornar qualquer outro código de status, ele será marcado como não íntegro com essa mensagem.

**Solução:** Dependendo do código de resposta do servidor de back-end, você pode executar as etapas a seguir. Alguns dos códigos de status comuns estão listados aqui:

| **Ao** | **Ações** |
| --- | --- |
| Incompatibilidade de código de status de investigação: recebido 401 | Verifique se o servidor back-end requer autenticação. As investigações do gateway de aplicativo não podem passar credenciais para autenticação neste momento. Permita que \"HTTP 401\" em um código de status de investigação corresponder ou sondar um caminho em que o servidor não exija autenticação. | |
| Incompatibilidade de código de status de investigação: recebido 403 | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor de back-end. | |
| Incompatibilidade de código de status de investigação: recebido 404 | Página não encontrada. Verifique se o caminho do nome do host está acessível no servidor de back-end. Altere o nome do host ou o parâmetro de caminho para um valor acessível. | |
| Incompatibilidade de código de status de investigação: recebido 405 | As solicitações de investigação para o gateway de aplicativo usam o método HTTP GET. Verifique se o servidor permite esse método. | |
| Incompatibilidade de código de status de investigação: recebido 500 | Erro interno do servidor. Verifique a integridade do servidor back-end e se os serviços estão em execução. | |
| Incompatibilidade de código de status de investigação: recebido 503 | Serviço indisponível. Verifique a integridade do servidor back-end e se os serviços estão em execução. | |

Ou, se você acredita que a resposta é legítima e deseja que o gateway de aplicativo aceite outros códigos de status como íntegros, você pode criar uma investigação personalizada. Essa abordagem é útil em situações em que o site de back-end precisa de autenticação. Como as solicitações de investigação não carregam nenhuma credencial do usuário, elas falharão e um código de status HTTP 401 será retornado pelo servidor de back-end.

Para criar uma investigação personalizada, siga [estas etapas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Incompatibilidade de corpo de resposta HTTP

**Mensagem:** O corpo da resposta HTTP do back-end\'s não correspondeu à configuração de investigação. O corpo da resposta recebida não contém {String}.

**Causa:** Ao criar uma investigação personalizada, você tem a opção de marcar um servidor de back-end como íntegro, combinando uma cadeia de caracteres do corpo da resposta. Por exemplo, você pode configurar o gateway de aplicativo para aceitar "não autorizado" como uma cadeia de caracteres para corresponder. Se a resposta do servidor back-end para a solicitação de investigação contiver a cadeia de caracteres **não autorizada**, ela será marcada como íntegra. Caso contrário, ele será marcado como não íntegro com esta mensagem.

**Solução:** Para resolver esse problema, siga estas etapas:

1.  Acesse o servidor back-end localmente ou em um computador cliente no caminho de investigação e verifique o corpo da resposta.

1.  Verifique se o corpo da resposta na configuração de investigação personalizada do gateway de aplicativo corresponde ao que está configurado.

1.  Se eles não corresponderem, altere a configuração de investigação para que o tenha o valor de cadeia de caracteres correto para aceitar.

Saiba mais sobre a [correspondência de investigação do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>AC inválida do certificado do servidor de back-end

**Mensagem:** O certificado do servidor usado pelo back-end não é assinado por uma autoridade de certificação (CA) conhecida. Lista de permissões do back-end no gateway de aplicativo, carregando o certificado raiz do certificado do servidor usado pelo back-end.

**Causa:** O SSL de ponta a ponta com o gateway de aplicativo v2 exige que o certificado do servidor back-end seja verificado para considerar o servidor íntegro.
Para que um certificado SSL seja confiável, esse certificado do servidor back-end deve ser emitido por uma autoridade de certificação que está incluída no repositório confiável do gateway de aplicativo. Se o certificado não foi emitido por uma autoridade de certificação confiável (por exemplo, se um certificado autoassinado foi usado), os usuários devem carregar o certificado do emissor no gateway de aplicativo.

**Solução:** Siga estas etapas para exportar e carregar o certificado raiz confiável para o gateway de aplicativo. (Essas etapas são para clientes Windows.)

1.  Entre no computador onde seu aplicativo está hospedado.

1.  Selecione Win + R ou clique com o botão direito do mouse em **Iniciar** e selecione **executar**.

1.  Insira `certmgr.msc` e selecione Enter. Você também pode procurar por Gerenciador de certificados no menu **Iniciar** .

1.  Localize o certificado, normalmente em `\Certificates - Current User\\Personal\\Certificates\`e abra-o.

1.  Selecione o certificado raiz e, em seguida, selecione **Exibir certificado**.

1.  Nas propriedades do certificado, selecione a guia **detalhes** .

1.  Na guia **detalhes** , selecione a opção **copiar para arquivo** e salve o arquivo no X. 509 codificado em base-64 (. CER) formato.

1.  Abra a página **configurações** de http do gateway de aplicativo na portal do Azure.

1. Abra as configurações de HTTP, selecione **Adicionar certificado**e localize o arquivo de certificado que você acabou de salvar.

1. Selecione **salvar** para salvar as configurações de http.

Como alternativa, você pode exportar o certificado raiz de um computador cliente acessando diretamente o servidor (ignorando o gateway de aplicativo) por meio do navegador e exportando o certificado raiz do navegador.

Para obter mais informações sobre como extrair e carregar certificados raiz confiáveis no gateway de aplicativo, consulte [Exportar certificado raiz confiável (para SKU v2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Incompatibilidade de certificado raiz confiável

**Mensagem:** O certificado raiz do certificado do servidor usado pelo back-end não corresponde ao certificado raiz confiável adicionado ao gateway de aplicativo. Certifique-se de adicionar o certificado raiz correto para a lista de permissões de back-end

**Causa:** O SSL de ponta a ponta com o gateway de aplicativo v2 exige que o certificado do servidor back-end seja verificado para considerar o servidor íntegro.
Para que um certificado SSL seja confiável, o certificado do servidor back-end deve ser emitido por uma autoridade de certificação que está incluída no repositório confiável do gateway de aplicativo. Se o certificado não foi emitido por uma autoridade de certificação confiável (por exemplo, um certificado autoassinado foi usado), os usuários devem carregar o certificado do emissor no gateway de aplicativo.

O certificado que foi carregado nas configurações de HTTP do gateway de aplicativo deve corresponder ao certificado raiz do certificado do servidor de back-end.

**Solução:** Se você receber essa mensagem de erro, haverá uma incompatibilidade entre o certificado que foi carregado no gateway de aplicativo e aquele que foi carregado no servidor de back-end.

Siga as etapas 1-11 no método anterior para carregar o certificado raiz confiável correto para o gateway de aplicativo.

Para obter mais informações sobre como extrair e carregar certificados raiz confiáveis no gateway de aplicativo, consulte [Exportar certificado raiz confiável (para SKU v2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Esse erro também pode ocorrer se o servidor back-end não trocar a cadeia completa do certificado, incluindo a raiz > intermediária (se aplicável) > folha durante o handshake de TLS. Para verificar, você pode usar comandos do OpenSSL de qualquer cliente e conectar-se ao servidor de back-end usando as configurações definidas na investigação do gateway de aplicativo.

Por exemplo:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar a cadeia completa do certificado que está sendo retornado, exporte o certificado novamente com a cadeia completa, incluindo o certificado raiz. Configure esse certificado em seu servidor de back-end. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comum inválido do certificado de back-end (CN)

**Mensagem:** O CN (nome comum) do certificado de back-end não corresponde ao cabeçalho de host da investigação.

**Causa:** O gateway de aplicativo verifica se o nome do host especificado nas configurações de HTTP de back-end corresponde ao do CN apresentado pelo certificado SSL do servidor de back-end. Isso é Standard_v2 e WAF_v2 comportamento de SKU. O Indicação de Nome de Servidor do SKU padrão e do WAF (SNI) é definido como o FQDN no endereço do pool de back-end.

Na SKU v2, se houver uma investigação padrão (nenhuma investigação personalizada foi configurada e associada), a SNI será definida a partir do nome do host mencionado nas configurações de HTTP. Ou, se "escolher nome do host do endereço de back-end" for mencionado nas configurações de HTTP, em que o pool de endereços de back-end contém um FQDN válido, essa configuração será aplicada.

Se houver uma investigação personalizada associada às configurações de HTTP, SNI será definido do nome de host mencionado na configuração de investigação personalizada. Ou, se **escolher nome do host das configurações http de back-end** estiver selecionado na investigação personalizada, SNI será definido do nome do host mencionado nas configurações de http.

Se **escolher nome do host do endereço de back-end** estiver definido nas configurações de http, o pool de endereços de back-end deverá conter um FQDN válido.

Se você receber essa mensagem de erro, o CN do certificado de back-end não corresponderá ao nome do host configurado na investigação personalizada ou nas configurações de HTTP (se **escolher nome do host das configurações de http de back-end** estiver selecionado). Se você estiver usando uma investigação padrão, o nome do host será definido como **127.0.0.1**. Se esse não for um valor desejado, você deverá criar uma investigação personalizada e associá-la às configurações de HTTP.

**Solução:**

Para resolver o problema, siga estes passos.

Para Windows:

1.  Entre no computador onde seu aplicativo está hospedado.

1.  Selecione Win + R ou clique com botão direito do mouse no botão **Iniciar** e selecione **executar**.

1.  Digite **certmgr. msc** e selecione Enter. Você também pode procurar por Gerenciador de certificados no menu **Iniciar** .

1.  Localize o certificado (normalmente em `\Certificates - Current User\\Personal\\Certificates`) e abra o certificado.

1.  Na guia **detalhes** , verifique a **entidade**do certificado.

1.  Verifique o CN do certificado dos detalhes e insira o mesmo no campo nome do host da investigação personalizada ou nas configurações de HTTP (se **escolher nome do host das configurações de http de back-end** estiver selecionado). Se esse não for o nome de host desejado para seu site, você deverá obter um certificado para esse domínio ou inserir o nome de host correto na configuração de configuração de HTTP ou investigação personalizada.

Para Linux usando OpenSSL:

1.  Execute este comando no OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Nas propriedades exibidas, localize o CN do certificado e insira o mesmo no campo nome do host das configurações de http. Se esse não for o nome de host desejado para seu site, você deverá obter um certificado para esse domínio ou inserir o nome de host correto na configuração de configuração de HTTP ou investigação personalizada.

#### <a name="backend-certificate-is-invalid"></a>O certificado de back-end é inválido

**Mensagem:** O certificado de back-end é inválido. A data atual não está dentro do \"válido de\" e \"válido para\" intervalo de datas no certificado.

**Causa:** Cada certificado é fornecido com um intervalo de validade e a conexão HTTPS não será segura, a menos que o certificado SSL do servidor seja válido. Os dados atuais devem estar dentro do intervalo **válido de** e **válido para** . Se não for, o certificado será considerado inválido e isso criará um problema de segurança no qual o gateway de aplicativo marca o servidor de back-end como não íntegro.

**Solução:** Se o seu certificado SSL tiver expirado, renove o certificado com seu fornecedor e atualize as configurações do servidor com o novo certificado. Se for um certificado autoassinado, você deverá gerar um certificado válido e carregar o certificado raiz nas configurações de HTTP do gateway de aplicativo. Para tal, siga estes passos:

1.  Abra as configurações de HTTP do gateway de aplicativo no Portal.

1.  Selecione a configuração que tem o certificado expirado, selecione **Adicionar certificado**e abra o novo arquivo de certificado.

1.  Remova o certificado antigo usando o ícone **excluir** ao lado do certificado e, em seguida, selecione **salvar**.

#### <a name="certificate-verification-failed"></a>Falha na verificação do certificado

**Mensagem:** A validade do certificado de back-end não pôde ser verificada. Para descobrir o motivo, marque o diagnóstico de SSL aberto para a mensagem associada ao código de erro {errorCode}

**Causa:** Esse erro ocorre quando o gateway de aplicativo não pode verificar a validade do certificado.

**Solução:** Para resolver esse problema, verifique se o certificado em seu servidor foi criado corretamente. Por exemplo, você pode usar o [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e suas propriedades e, em seguida, tentar recarregar o certificado nas configurações de http do gateway de aplicativo.

<a name="backend-health-status-unknown"></a>Status de integridade de back-end: desconhecido
-------------------------------
Se a integridade do back-end for mostrada como desconhecida, a exibição do portal será semelhante à captura de tela a seguir:

![Integridade de back-end do gateway de aplicativo-desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Esse comportamento pode ocorrer por um ou mais dos seguintes motivos:

1.  O NSG na sub-rede do gateway de aplicativo está bloqueando o acesso de entrada às portas 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) de "Internet".
1.  O UDR na sub-rede do gateway de aplicativo é definido como a rota padrão (0.0.0.0/0) e o próximo salto não é especificado como "Internet".
1.  A rota padrão é anunciada por uma conexão de ExpressRoute/VPN para uma rede virtual por BGP.
1.  O servidor DNS personalizado é configurado em uma rede virtual que não pode resolver nomes de domínio públicos.
1.  O gateway de aplicativo está em um estado não íntegro.

**Solução:**

1.  Verifique se o NSG está bloqueando o acesso às portas 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) da **Internet**:

    a.  Na guia **visão geral** do gateway de aplicativo, selecione o link **rede virtual/sub-rede** .

    b.  Na guia **sub-redes** da sua rede virtual, selecione a sub-rede na qual o gateway de aplicativo foi implantado.

    c.  Verifique se algum NSG está configurado.

    d.  Se um NSG estiver configurado, procure esse recurso NSG na guia **Pesquisar** ou em **todos os recursos**.

    e.  Na seção **regras de entrada** , adicione uma regra de entrada para permitir o intervalo de portas de destino 65503-65534 para SKU de versão v1 ou 65200-65535 V2 com a **fonte** definida como **qualquer** ou **Internet**.

    f.  Selecione **salvar** e verifique se você pode exibir o back-end como íntegro. Como alternativa, você pode fazer isso por meio do [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Verifique se o UDR tem uma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**:
    
    a.  Siga as etapas 1a e 1B para determinar sua sub-rede.

    b.  Verifique se há qualquer UDR configurado. Se houver, pesquise o recurso na barra de pesquisa ou em **todos os recursos**.

    c.  Verifique se há alguma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**. Se a configuração for **Virtual Appliance** ou **Gateway de rede virtual**, você deverá garantir que sua solução virtual ou o dispositivo local possam rotear corretamente o pacote de volta para o destino da Internet sem modificar o pacote.

    d.  Caso contrário, altere o próximo salto para a **Internet**, selecione **salvar**e verifique a integridade do back-end.

1.  Rota padrão anunciada pela conexão de ExpressRoute/VPN para a rede virtual por BGP:

    a.  Se você tiver uma conexão de ExpressRoute/VPN com a rede virtual por BGP e se estiver anunciando uma rota padrão, deverá certificar-se de que o pacote seja roteado de volta para o destino da Internet sem modificá-lo. Você pode verificar usando a opção de **solução de problemas de conexão** no portal do gateway de aplicativo.

    b.  Escolha o destino manualmente como qualquer endereço IP roteável da Internet, como 1.1.1.1. Defina a porta de destino como qualquer coisa e verifique a conectividade.

    c.  Se o próximo salto for um gateway de rede virtual, pode haver uma rota padrão anunciada via ExpressRoute ou VPN.

1.  Se houver um servidor DNS personalizado configurado na rede virtual, verifique se o servidor (ou servidores) pode resolver domínios públicos. A resolução de nome de domínio público pode ser necessária em cenários em que o gateway de aplicativo deve acessar domínios externos, como servidores OCSP, ou verificar o status de revogação do certificado.

1.  Para verificar se o gateway de aplicativo está íntegro e em execução, vá para a opção **Resource Health** no portal e verifique se o estado é **íntegro**. Se você vir um estado não **íntegro** ou **degradado** , [entre em contato com o suporte](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Passos seguintes
----------

Saiba mais sobre o [diagnóstico e o log do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
