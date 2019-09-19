---
title: Perguntas frequentes sobre configuração-serviço de Azure App | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre problemas de configuração e gerenciamento do recurso de aplicativos Web do serviço de Azure App.
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 68d0f693d0cc7d8db8e6f697ff8907400a7aca50
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121316"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Perguntas frequentes de configuração e gerenciamento para aplicativos Web no Azure

Este artigo tem respostas para perguntas frequentes sobre problemas de configuração e gerenciamento para o [recurso de aplicativos Web do serviço de Azure app](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Há limitações que devo saber se eu quiser mover os recursos do serviço de aplicativo?

Se você planeja mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura, há algumas limitações a serem consideradas. Para obter mais informações, consulte [limitações do serviço de aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como fazer usar um nome de domínio personalizado para meu aplicativo Web?

Para obter respostas a perguntas comuns sobre como usar um nome de domínio personalizado com seu aplicativo Web do Azure, consulte nosso vídeo de sete minutos [Adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). O vídeo oferece uma explicação sobre como adicionar um nome de domínio personalizado. Ele descreve como usar sua própria URL em vez da URL *. azurewebsites.net com seu aplicativo Web do serviço de aplicativo. Você também pode ver uma explicação detalhada de [como mapear um nome de domínio personalizado](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como fazer comprar um novo domínio personalizado para meu aplicativo Web?

Para saber como comprar e configurar um domínio personalizado para seu aplicativo Web do serviço de aplicativo, consulte [comprar e configurar um nome de domínio personalizado no serviço de aplicativo](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Como fazer carregar e configurar um certificado SSL existente para meu aplicativo Web?

Para saber como carregar e configurar um certificado SSL personalizado existente, consulte [associar um certificado SSL personalizado existente a um aplicativo Web do Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Como fazer comprar e configurar um novo certificado SSL no Azure para meu aplicativo Web?

Para saber como comprar e configurar um certificado SSL para seu aplicativo Web do serviço de aplicativo, consulte [Adicionar um certificado SSL ao seu aplicativo do serviço de aplicativo](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como fazer mover Application Insights recursos?

Atualmente, o Aplicativo Azure insights não dá suporte à operação de movimentação. Se o grupo de recursos original incluir um recurso de Application Insights, você não poderá mover esse recurso. Se você incluir o recurso de Application Insights quando tentar mover um aplicativo do serviço de aplicativo, toda a operação de movimentação falhará. No entanto, Application Insights e o plano do serviço de aplicativo não precisam estar no mesmo grupo de recursos que o aplicativo para que o aplicativo funcione corretamente.

Para obter mais informações, consulte [limitações do serviço de aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de verificação de diretrizes e saber mais sobre as operações de movimentação de recursos?

As [limitações do serviço de aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md) mostram como mover recursos para uma nova assinatura ou para um novo grupo de recursos na mesma assinatura. Você pode obter informações sobre a lista de verificação de movimentação de recursos, saiba quais serviços dão suporte à operação de movimentação e saiba mais sobre as limitações do serviço de aplicativo e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como fazer definir o fuso horário do servidor para meu aplicativo Web?

Para definir o fuso horário do servidor para seu aplicativo Web:

1. Na portal do Azure, em sua assinatura do serviço de aplicativo, vá para o menu **configurações do aplicativo** .
2. Em **configurações do aplicativo**, adicione esta configuração:
    * Key = WEBSITE_TIME_ZONE
    * Valor = *o fuso horário que você deseja*
3. Selecione **Guardar**.

Para os serviços de aplicativos que são executados no Windows, consulte a coluna **timezone** no artigo [fusos horários padrão](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) para obter os valores aceitos. Para os serviços de aplicativos que são executados no Linux, defina o [nome do banco de dados TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) como o valor de fuso horário. Aqui está um exemplo de nome do banco de dados TZ: América/Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Por que meus trabalhos Web contínuos às vezes falham?

Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um período de tempo definido. Isso permite ao sistema conservar recursos. Em planos básico e padrão, você pode ativar a configuração de **Always on** para manter o aplicativo Web carregado o tempo todo. Se seu aplicativo Web executar webjobs contínuos, você deverá ativar **Always on**ou os trabalhos Web poderão não ser executados de forma confiável. Para obter mais informações, consulte [criar um WebJob de execução contínua](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como fazer obter o endereço IP de saída para meu aplicativo Web?

Para obter a lista de endereços IP de saída para seu aplicativo Web:

1. Na portal do Azure, na folha do seu aplicativo Web, vá para o menu **Propriedades** .
2. Procure **endereços IP de saída**.

A lista de endereços IP de saída é exibida.

Para saber como obter o endereço IP de saída se seu site estiver hospedado em um Ambiente do Serviço de Aplicativo, consulte [endereços de rede de saída](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como fazer obter um endereço IP de entrada dedicado ou reservado para meu aplicativo Web?

Para configurar um endereço IP dedicado ou reservado para chamadas de entrada feitas no site do aplicativo do Azure, instale e configure um certificado SSL baseado em IP.

Observe que para usar um endereço IP dedicado ou reservado para chamadas de entrada, seu plano do serviço de aplicativo deve estar em um plano de serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Posso exportar meu certificado do serviço de aplicativo para uso fora do Azure, como para um site hospedado em outro lugar? 

Sim, você pode exportá-los para uso fora do Azure. Para obter mais informações, consulte [perguntas frequentes para certificados do serviço de aplicativo e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Posso exportar meu certificado do serviço de aplicativo para usar com outros serviços de nuvem do Azure?

O portal fornece uma experiência de primeira classe para implantar um certificado do serviço de aplicativo por meio de Azure Key Vault para aplicativos do serviço de aplicativo. No entanto, recebemos solicitações de clientes para usar esses certificados fora da plataforma do serviço de aplicativo, por exemplo, com máquinas virtuais do Azure. Para saber como criar uma cópia local do PFX do seu certificado do serviço de aplicativo para que você possa usar o certificado com outros recursos do Azure, consulte [criar uma cópia do pfx local de um certificado do serviço de aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Para obter mais informações, consulte [perguntas frequentes para certificados do serviço de aplicativo e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que vejo a mensagem "parcialmente bem-sucedida" quando tento fazer backup do meu aplicativo Web?

Uma causa comum da falha de backup é que alguns arquivos estão em uso pelo aplicativo. Os arquivos que estão em uso são bloqueados enquanto você executa o backup. Isso impede que esses arquivos sejam copiados em backup e possam resultar em um status "parcialmente bem-sucedido". Potencialmente, você pode impedir que isso ocorra excluindo arquivos do processo de backup. Você pode optar por fazer backup apenas do que é necessário. Para obter mais informações, consulte [fazer backup apenas das partes importantes do seu site com os aplicativos Web do Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como fazer remover um cabeçalho da resposta HTTP?

Para remover os cabeçalhos da resposta HTTP, atualize o arquivo Web. config do seu site. Para obter mais informações, consulte [remover cabeçalhos de servidor padrão em seus sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>O serviço de aplicativo é compatível com PCI Standard 3,0 e 3,1?

Atualmente, o recurso de aplicativos Web do serviço de Azure App está em conformidade com o PCI Data Security Standard (DSS) versão 3,0 nível 1. PCI DSS versão 3,1 está em nosso roteiro. O planejamento já está em andamento para como a adoção do padrão mais recente continuará.

A certificação PCI DSS versão 3,1 requer a desabilitação do protocolo TLS 1,0. Atualmente, desabilitar o TLS 1,0 não é uma opção para a maioria dos planos do serviço de aplicativo. No entanto, se você usar Ambiente do Serviço de Aplicativo ou estiver disposto a migrar sua carga de trabalho para Ambiente do Serviço de Aplicativo, poderá obter maior controle do seu ambiente. Isso envolve desabilitar o TLS 1,0 entrando em contato com o suporte do Azure. Em breve, planejamos tornar essas configurações acessíveis para os usuários.

Para obter mais informações, consulte [Microsoft Azure aplicativo Web do serviço de aplicativo conformidade com PCI Standard 3,0 e 3,1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como fazer usar o ambiente de preparo e os slots de implantação?

Nos planos do serviço de aplicativo Standard e Premium, ao implantar seu aplicativo Web no serviço de aplicativo, você pode implantar em um slot de implantação separado em vez de no slot de produção padrão. Os slots de implantação são aplicativos Web dinâmicos que têm seus próprios nomes de host. Elementos de configuração e conteúdo do aplicativo Web podem ser trocados entre dois slots de implantação, incluindo o slot de produção.

Para obter mais informações sobre como usar slots de implantação, consulte [configurar um ambiente de preparo no serviço de aplicativo](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como fazer acessar e examinar os logs do WebJob?

Para examinar os logs do WebJob:

1. Entre no site do [kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o WebJob.
3. Selecione o botão **alternar saída** .
4. Para baixar o arquivo de saída, selecione o link **baixar** .
5. Para execuções individuais, selecione **invocar individual**.
6. Selecione o botão **alternar saída** .
7. Selecione o link baixar.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou tentando usar Conexões Híbridas com SQL Server. Por que vejo a mensagem "System. OverflowException: A operação aritmética resultou em um estouro "?

Se você usar Conexões Híbridas para acessar SQL Server, uma atualização de Microsoft .NET em 10 de maio de 2016 poderá causar falha nas conexões. Você pode ver esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gerenciador de Conexões Híbridas que foi corrigido desde então. Certifique-se de [atualizar seu Gerenciador de conexões híbridas](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver esse problema.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Como fazer adicionar uma regra de regravação de URL?

Para adicionar uma regra de reescrita de URL, crie um arquivo Web. config com as entradas de configuração relevantes na pasta **wwwroot** . Para obter mais informações, [consulte serviços de Azure App: Entendendo a regravação](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/)de URL.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como fazer controlar o tráfego de entrada para o serviço de aplicativo?

No nível do site, você tem duas opções para controlar o tráfego de entrada para o serviço de aplicativo:

* Ative as restrições de IP dinâmico. Para saber como ativar as restrições de IP dinâmico, consulte [restrições de IP e de domínio para sites do Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ative a segurança do módulo. Para saber como ativar a segurança do módulo, consulte [Firewall do aplicativo Web do ModSecurity nos sites do Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se você usar Ambiente do Serviço de Aplicativo, poderá usar o [Firewall do Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como fazer bloquear portas em um aplicativo Web do serviço de aplicativo?

No ambiente de locatário compartilhado do serviço de aplicativo, não é possível bloquear portas específicas devido à natureza da infraestrutura. As portas TCP 4020, 4022 e 4024 também podem estar abertas para depuração remota do Visual Studio.

No Ambiente do Serviço de Aplicativo, você tem controle total sobre o tráfego de entrada e de saída. Você pode usar grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre Ambiente do Serviço de Aplicativo, consulte [introdução a ambiente do serviço de aplicativo](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Como fazer capturar um rastreamento F12?

Você tem duas opções para capturar um rastreamento F12:

* Rastreamento de HTTP F12
* Saída do console F12

### <a name="f12-http-trace"></a>Rastreamento de HTTP F12

1. No Internet Explorer, vá para seu site. É importante entrar antes de executar as próximas etapas. Caso contrário, o rastreamento F12 captura dados de entrada confidenciais.
2. Pressione F12.
3. Verifique se a guia **rede** está selecionada e, em seguida, selecione o botão verde **executar** .
4. Execute as etapas que reproduzem o problema.
5. Selecione o botão de **parada** vermelha.
6. Selecione o botão **salvar** (ícone de disco) e salve o arquivo Har (no Internet Explorer e Microsoft Edge) *ou* clique com o botão direito do mouse no arquivo Har e selecione **salvar como Har com conteúdo** (no Chrome).

### <a name="f12-console-output"></a>Saída do console F12

1. Selecione a guia **console** .
2. Para cada guia que contenha mais de zero itens, selecione a guia (**erro**, **aviso**ou **informações**). Se a guia não estiver selecionada, o ícone de guia ficará cinza ou preto quando você mover o cursor para longe dele.
3. Clique com o botão direito do mouse na área de mensagem do painel e selecione **copiar tudo**.
4. Cole o texto copiado em um arquivo e salve o arquivo.

Para exibir um arquivo HAR, você pode usar o [Visualizador Har](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que recebo um erro quando tento conectar um aplicativo Web do serviço de aplicativo a uma rede virtual que está conectada ao ExpressRoute?

Se você tentar conectar um aplicativo Web do Azure a uma rede virtual que está conectada ao Azure ExpressRoute, ele falhará. É apresentada a seguinte mensagem: "O gateway não é um gateway de VPN."

No momento, não é possível ter conexões VPN ponto a site com uma rede virtual conectada ao ExpressRoute. Uma VPN ponto a site e o ExpressRoute não podem coexistir para a mesma rede virtual. Para obter mais informações, veja [limites e limitações de conexões VPN site a](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)site e de ExpressRoute.

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como fazer conectar um aplicativo Web do serviço de aplicativo a uma rede virtual que tem um gateway de roteamento estático (baseado em políticas)?

Atualmente, não há suporte para a conexão de um aplicativo Web do serviço de aplicativo a uma rede virtual que tenha um gateway de roteamento estático (baseado em políticas). Se a rede virtual de destino já existir, ela deverá ter uma VPN ponto a site habilitada, com um gateway de roteamento dinâmico, antes que possa ser conectada a um aplicativo. Se o gateway estiver definido como roteamento estático, você não poderá habilitar uma VPN ponto a site. 

Para obter mais informações, consulte [integrar um aplicativo a uma rede virtual do Azure](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Em minha Ambiente do Serviço de Aplicativo, por que posso criar apenas um plano do serviço de aplicativo, embora tenha dois trabalhadores disponíveis?

Para fornecer tolerância a falhas, Ambiente do Serviço de Aplicativo requer que cada pool de trabalho precise de pelo menos um recurso de computação adicional. O recurso de computação adicional não pode ser atribuído a uma carga de trabalho.

Para obter mais informações, consulte [como criar um ambiente do serviço de aplicativo](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Por que vejo tempos limite quando tento criar um Ambiente do Serviço de Aplicativo?

Às vezes, a criação de um Ambiente do Serviço de Aplicativo falha. Nesse caso, você verá o seguinte erro nos logs de atividade:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Para resolver isso, certifique-se de que nenhuma das condições a seguir seja verdadeira:
* A sub-rede é muito pequena.
* A sub-rede não está vazia.
* O ExpressRoute impede os requisitos de conectividade de rede de um Ambiente do Serviço de Aplicativo.
* Um grupo de segurança de rede inadequado impede os requisitos de conectividade de rede de um Ambiente do Serviço de Aplicativo.
* O túnel forçado está ativado.

Para obter mais informações, consulte [problemas frequentes ao implantar (criar) um novo ambiente do serviço de aplicativo do Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Por que não posso excluir meu plano do serviço de aplicativo?

Você não poderá excluir um plano do serviço de aplicativo se algum aplicativo do serviço de aplicativo estiver associado ao plano do serviço de aplicativo. Antes de excluir um plano do serviço de aplicativo, remova todos os aplicativos do serviço de aplicativo associados do plano do serviço de aplicativo.

## <a name="how-do-i-schedule-a-webjob"></a>Como fazer agendar um WebJob?

Você pode criar um WebJob agendado usando expressões cron:

1. Crie um arquivo Settings. Job.
2. Nesse arquivo JSON, inclua uma propriedade de agendamento usando uma expressão cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Para obter mais informações sobre trabalhos Web agendados, consulte [criar um WebJob agendado usando uma expressão cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como fazer executar testes de penetração para meu aplicativo do serviço de aplicativo?

Para executar o teste de penetração, [envie uma solicitação](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como fazer configurar um nome de domínio personalizado para um aplicativo Web do serviço de aplicativo que usa o Gerenciador de tráfego?

Para saber como usar um nome de domínio personalizado com um aplicativo do serviço de aplicativo que usa o Gerenciador de tráfego do Azure para balanceamento de carga, consulte [configurar um nome de domínio personalizado para um aplicativo Web do Azure com o Gerenciador de tráfego](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Meu certificado do serviço de aplicativo está sinalizado para fraude. Como fazer resolver isso?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante a verificação de domínio de uma compra de certificado do serviço de aplicativo, você poderá ver a seguinte mensagem:

"Seu certificado foi sinalizado para uma possível fraude. A solicitação está em análise no momento. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure.

Como a mensagem indica, esse processo de verificação de fraude pode levar até 24 horas para ser concluído. Durante esse tempo, você continuará a ver a mensagem.

Se o seu certificado de serviço de aplicativo continuar a mostrar esta mensagem após 24 horas, execute o seguinte script do PowerShell. O script entra em contato diretamente com o [provedor de certificados](https://www.godaddy.com/) para resolver o problema.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Como funciona a autenticação e a autorização no serviço de aplicativo?

Para obter a documentação detalhada para autenticação e autorização no serviço de aplicativo, consulte documentos para várias entradas de provedor de identidade:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Conta Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Como fazer redirecionar o domínio padrão *. azurewebsites.net para o domínio personalizado do meu aplicativo Web do Azure?

Quando você cria um novo site usando aplicativos Web no Azure, um domínio padrão *SiteName*. azurewebsites.net é atribuído ao seu site. Se você adicionar um nome de host personalizado ao seu site e não quiser que os usuários possam acessar seu domínio padrão *. azurewebsites.net, você poderá redirecionar a URL padrão. Para saber como redirecionar todo o tráfego do domínio padrão do seu site para seu domínio personalizado, consulte [redirecionar o domínio padrão para seu domínio personalizado nos aplicativos Web do Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como fazer determinar qual versão da versão do .NET está instalada no serviço de aplicativo?

A maneira mais rápida de localizar a versão do Microsoft .NET que está instalada no serviço de aplicativo é usando o console do kudu. Você pode acessar o console do kudu no portal ou usando a URL do seu aplicativo do serviço de aplicativo. Para obter instruções detalhadas, consulte [determinar a versão do .net instalada no serviço de aplicativo](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Por que o dimensionamento automático não funciona conforme o esperado?

Se o dimensionamento automático do Azure não tiver sido dimensionado ou dimensionado horizontalmente na instância do aplicativo Web conforme o esperado, você poderá estar se esgotando em um cenário no qual escolhemos intencionalmente não dimensionar para evitar um loop infinito devido à "oscilação". Isso geralmente acontece quando não há uma margem adequada entre os limites de expansão e redução. Para saber como evitar "oscilação" e ler sobre outras práticas recomendadas de dimensionamento automático, consulte [práticas recomendadas de dimensionamento automático](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Por que o dimensionamento automático às vezes dimensiona apenas parcialmente?

O dimensionamento automático é disparado quando as métricas excedem os limites pré-configurados. Às vezes, você pode notar que a capacidade é apenas parcialmente preenchida em comparação com o esperado. Isso pode ocorrer quando o número de instâncias que você deseja não está disponível. Nesse cenário, o dimensionamento automático é preenchido parcialmente com o número disponível de instâncias. O dimensionamento automático executa a lógica de rebalanceamento para obter mais capacidade. Ele aloca as instâncias restantes. Observe que isso pode levar alguns minutos.

Se você não vir o número esperado de instâncias depois de alguns minutos, pode ser porque o reabastecimento parcial era suficiente para trazer as métricas dentro dos limites. Ou, o dimensionamento automático pode ter sido reduzido porque atingiu o limite de métricas menores.

Se nenhuma dessas condições se aplicar e o problema persistir, envie uma solicitação de suporte.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Como fazer ativar a compactação HTTP para meu conteúdo?

Para ativar a compactação para tipos de conteúdo estáticos e dinâmicos, adicione o seguinte código ao arquivo Web. config no nível de aplicativo:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Você também pode especificar os tipos MIME dinâmicos e estáticos específicos que deseja compactar. Para obter mais informações, consulte nossa resposta a uma pergunta de fórum em [configurações de httpCompression em um site simples do Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como fazer migrar de um ambiente local para o serviço de aplicativo?

Para migrar sites de servidores Web do Windows e Linux para o serviço de aplicativo, você pode usar Azure App Assistente de Migração de serviço. A ferramenta de migração cria aplicativos Web e bancos de dados no Azure conforme necessário e, em seguida, publica o conteúdo. Para obter mais informações, consulte [Azure app de serviço assistente de migração](https://www.migratetoazure.net/).
