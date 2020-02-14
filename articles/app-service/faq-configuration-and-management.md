---
title: Perguntas frequentes sobre configuração
description: Obtenha respostas para perguntas frequentes sobre problemas de configuração e gerenciamento para Azure App serviço.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 598fdd5e3682226253ef8f2611ede2bbeba596f7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190429"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Perguntas frequentes de configuração e gerenciamento para aplicativos Web no Azure

Este artigo tem respostas a perguntas frequentes (PERGUNTAS) sobre questões de configuração e gestão para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Há limitações que devo saber se eu quiser mover os recursos do serviço de aplicativo?

Se você planeja mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura, há algumas limitações a serem consideradas. Para mais informações, consulte [as limitações](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)do Serviço de Aplicações .

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como fazer usar um nome de domínio personalizado para meu aplicativo Web?

Para respostas a perguntas comuns sobre o uso de um nome de domínio personalizado com a sua aplicação web Azure, consulte o nosso vídeo de sete minutos [Adicione um nome](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)de domínio personalizado . O vídeo oferece uma explicação sobre como adicionar um nome de domínio personalizado. Ele descreve como usar sua própria URL em vez da URL *. azurewebsites.net com seu aplicativo Web do serviço de aplicativo. Você também pode ver uma passagem detalhada de [como mapear um nome](app-service-web-tutorial-custom-domain.md)de domínio personalizado .


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como fazer comprar um novo domínio personalizado para meu aplicativo Web?

Para aprender a comprar e configurar um domínio personalizado para a sua aplicação web do App Service, consulte [Comprar e configurar um nome](manage-custom-dns-buy-domain.md)de domínio personalizado no Serviço de Aplicações .


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Como fazer carregar e configurar um certificado SSL existente para meu aplicativo Web?

Para aprender a carregar e configurar um certificado SSL personalizado existente, consulte [Adicionar um certificado SSL à sua aplicação App Service](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Como fazer comprar e configurar um novo certificado SSL no Azure para meu aplicativo Web?

Para aprender a comprar e configurar um certificado SSL para a sua aplicação web App Service, consulte [Adicionar um certificado SSL à sua aplicação App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como fazer mover Application Insights recursos?

Atualmente, o Aplicativo Azure insights não dá suporte à operação de movimentação. Se o grupo de recursos original incluir um recurso de Application Insights, você não poderá mover esse recurso. Se você incluir o recurso de Application Insights quando tentar mover um aplicativo do serviço de aplicativo, toda a operação de movimentação falhará. No entanto, Application Insights e o plano do serviço de aplicativo não precisam estar no mesmo grupo de recursos que o aplicativo para que o aplicativo funcione corretamente.

Para mais informações, consulte [as limitações](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)do Serviço de Aplicações .

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de verificação de diretrizes e saber mais sobre as operações de movimentação de recursos?

[As limitações](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) do Serviço de Aplicações mostram-lhe como mover recursos para uma nova subscrição ou para um novo grupo de recursos na mesma subscrição. Você pode obter informações sobre a lista de verificação de movimentação de recursos, saiba quais serviços dão suporte à operação de movimentação e saiba mais sobre as limitações do serviço de aplicativo e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como fazer definir o fuso horário do servidor para meu aplicativo Web?

Para definir o fuso horário do servidor para seu aplicativo Web:

1. No portal Azure, na subscrição do Serviço de Aplicações, vá ao menu de definições de **Aplicações.**
2. Em termos de definições de **Aplicações,** adicione esta definição:
    * Key = WEBSITE_TIME_ZONE
    * Valor = *O fuso horário que deseja*
3. Selecione **Guardar**.

Para os serviços da App que funcionam no Windows, consulte a coluna **Timezone** no artigo Predefinido time zones para [valores](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) aceites. Para os serviços da App que funcionam no Linux, detete o nome da base de [dados TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) como o valor do fuso horário. Aqui está um exemplo de nome do banco de dados TZ: America/Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Por que meus trabalhos Web contínuos às vezes falham?

Por padrão, as aplicações web são descarregadas se ficarem inativas por um determinado período de tempo. Isso permite ao sistema conservar recursos. Nos planos Basic e Standard, pode ligar a definição **Always On** para manter a aplicação web carregada a toda a hora. Se a sua aplicação web executar WebJobs contínuos, deve ligar **Sempre Ligado**, ou os WebJobs podem não funcionar de forma fiável. Para mais informações, consulte [Criar um WebJob em execução contínua](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como fazer obter o endereço IP de saída para meu aplicativo Web?

Para obter a lista de endereços IP de saída para seu aplicativo Web:

1. No portal Azure, na sua lâmina de aplicação web, vá ao menu **Propriedades.**
2. Procure **endereços ip de saída**.

A lista de endereços IP de saída é exibida.

Para saber como obter o endereço IP de saída se o seu website estiver hospedado num Ambiente de Serviço de Aplicações, consulte [os endereços da rede Outbound](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como fazer obter um endereço IP de entrada dedicado ou reservado para meu aplicativo Web?

Para configurar um endereço IP dedicado ou reservado para chamadas de entrada feitas no site do aplicativo do Azure, instale e configure um certificado SSL baseado em IP.

Observe que para usar um endereço IP dedicado ou reservado para chamadas de entrada, seu plano do serviço de aplicativo deve estar em um plano de serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Posso exportar meu certificado do serviço de aplicativo para uso fora do Azure, como para um site hospedado em outro lugar? 

Sim, você pode exportá-los para uso fora do Azure. Para mais informações, consulte faQs para certificados de serviço de [aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Posso exportar meu certificado do serviço de aplicativo para usar com outros serviços de nuvem do Azure?

O portal fornece uma experiência de primeira classe para implantar um certificado do serviço de aplicativo por meio de Azure Key Vault para aplicativos do serviço de aplicativo. No entanto, recebemos solicitações de clientes para usar esses certificados fora da plataforma do serviço de aplicativo, por exemplo, com máquinas virtuais do Azure. Para aprender a criar uma cópia PFX local do seu certificado de Serviço de Aplicações para que possa utilizar o certificado com outros recursos Do Azure, consulte Criar uma cópia PFX local de um certificado de Serviço de [Aplicações.](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)

Para mais informações, consulte faQs para certificados de serviço de [aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que vejo a mensagem "parcialmente bem-sucedida" quando tento fazer backup do meu aplicativo Web?

Uma causa comum da falha de backup é que alguns arquivos estão em uso pelo aplicativo. Os arquivos que estão em uso são bloqueados enquanto você executa o backup. Isso impede que esses arquivos sejam copiados em backup e possam resultar em um status "parcialmente bem-sucedido". Potencialmente, você pode impedir que isso ocorra excluindo arquivos do processo de backup. Você pode optar por fazer backup apenas do que é necessário. Para mais informações, consulte [a cópia de segurança apenas as partes importantes do seu site com aplicações web Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como fazer remover um cabeçalho da resposta HTTP?

Para remover os cabeçalhos da resposta HTTP, atualize o arquivo Web. config do seu site. Para mais informações, consulte Remova os [cabeçalhos padrão do servidor nos seus websites Do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>O serviço de aplicativo é compatível com PCI Standard 3,0 e 3,1?

Atualmente, o recurso de aplicativos Web do serviço de Azure App está em conformidade com o PCI Data Security Standard (DSS) versão 3,0 nível 1. PCI DSS versão 3,1 está em nosso roteiro. O planejamento já está em andamento para como a adoção do padrão mais recente continuará.

A certificação PCI DSS versão 3,1 requer a desabilitação do protocolo TLS 1,0. Atualmente, desabilitar o TLS 1,0 não é uma opção para a maioria dos planos do serviço de aplicativo. No entanto, se você usar Ambiente do Serviço de Aplicativo ou estiver disposto a migrar sua carga de trabalho para Ambiente do Serviço de Aplicativo, poderá obter maior controle do seu ambiente. Isso envolve desabilitar o TLS 1,0 entrando em contato com o suporte do Azure. Em breve, planejamos tornar essas configurações acessíveis para os usuários.

Para mais informações, consulte o [microsoft Azure App Service na web compliance com PCI Standard 3.0 e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como fazer usar o ambiente de preparo e os slots de implantação?

Nos planos do serviço de aplicativo Standard e Premium, ao implantar seu aplicativo Web no serviço de aplicativo, você pode implantar em um slot de implantação separado em vez de no slot de produção padrão. Os slots de implantação são aplicativos Web dinâmicos que têm seus próprios nomes de host. Elementos de configuração e conteúdo do aplicativo Web podem ser trocados entre dois slots de implantação, incluindo o slot de produção.

Para obter mais informações sobre a utilização de ranhuras de implementação, consulte Configurar um ambiente de [encenação no Serviço](deploy-staging-slots.md)de Aplicações .

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como fazer acessar e examinar os logs do WebJob?

Para examinar os logs do WebJob:

1. Inscreva-se no seu [site da Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecione o WebJob.
3. Selecione o botão **Toggle Output.**
4. Para descarregar o ficheiro de saída, selecione o link **Download.**
5. Para execuções individuais, selecione **Individual Invoke**.
6. Selecione o botão **Toggle Output.**
7. Selecione o link baixar.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou tentando usar Conexões Híbridas com SQL Server. Por que vejo a mensagem "System. OverflowException: a operação aritmética resultou em um estouro"?

Se você usar Conexões Híbridas para acessar SQL Server, uma atualização de Microsoft .NET em 10 de maio de 2016 poderá causar falha nas conexões. Você pode ver esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gerenciador de Conexões Híbridas que foi corrigido desde então. Certifique-se de atualizar o seu Gestor de [Ligação Híbrida](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver este problema.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Como fazer adicionar uma regra de regravação de URL?

Para adicionar uma regra de reescrita de URL, crie um ficheiro web.config com as entradas config relevantes na pasta **wwwroot.** Para mais informações, consulte [Azure App Services: Understanding URL rewrite](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como fazer controlar o tráfego de entrada para o serviço de aplicativo?

No nível do site, você tem duas opções para controlar o tráfego de entrada para o serviço de aplicativo:

* Ative as restrições de IP dinâmico. Para aprender a ativar restrições ip dinâmicas, consulte [restrições IP e domínio para websites Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ative a segurança do módulo. Para saber como ativar a Segurança do Módulo, consulte a firewall de [aplicação web modSecurity nos websites do Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se utilizar o App Service Environment, pode utilizar a [firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como fazer bloquear portas em um aplicativo Web do serviço de aplicativo?

No ambiente de locatário compartilhado do serviço de aplicativo, não é possível bloquear portas específicas devido à natureza da infraestrutura. As portas TCP 4020, 4022 e 4024 também podem estar abertas para depuração remota do Visual Studio.

No Ambiente do Serviço de Aplicativo, você tem controle total sobre o tráfego de entrada e de saída. Você pode usar grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre o Ambiente do Serviço de Aplicações, consulte a introdução do Ambiente do Serviço de [Aplicações.](https://azure.microsoft.com/blog/introducing-app-service-environment/)

## <a name="how-do-i-capture-an-f12-trace"></a>Como fazer capturar um rastreamento F12?

Você tem duas opções para capturar um rastreamento F12:

* Rastreamento de HTTP F12
* Saída do console F12

### <a name="f12-http-trace"></a>Rastreamento de HTTP F12

1. No Internet Explorer, vá para seu site. É importante entrar antes de executar as próximas etapas. Caso contrário, o rastreamento F12 captura dados de entrada confidenciais.
2. Pressione F12.
3. Verifique se o separador **Rede** está selecionado e, em seguida, selecione o botão **reproduzir** verde.
4. Execute as etapas que reproduzem o problema.
5. Selecione o **botão** stop vermelho.
6. Selecione o botão **Guardar** (ícone do disco) e guarde o ficheiro HAR (no Internet Explorer e Microsoft Edge) *ou* clique na direita no ficheiro HAR e, em seguida, selecione **Guardar como HAR com conteúdo** (no Chrome).

### <a name="f12-console-output"></a>Saída do console F12

1. Selecione o separador **Consola.**
2. Para cada separador que contenha mais de zero itens, selecione o separador **(Error,** **Warning**, or **Information**). Se a guia não estiver selecionada, o ícone de guia ficará cinza ou preto quando você mover o cursor para longe dele.
3. Clique à direita na área da mensagem do painel e, em seguida, selecione **Copiar tudo**.
4. Cole o texto copiado em um arquivo e salve o arquivo.

Para ver um ficheiro HAR, pode utilizar o [espectador HAR](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que recebo um erro quando tento conectar um aplicativo Web do serviço de aplicativo a uma rede virtual que está conectada ao ExpressRoute?

Se você tentar conectar um aplicativo Web do Azure a uma rede virtual que está conectada ao Azure ExpressRoute, ele falhará. A seguinte mensagem é exibida: "o gateway não é um gateway de VPN".

No momento, não é possível ter conexões VPN ponto a site com uma rede virtual conectada ao ExpressRoute. Uma VPN ponto a site e o ExpressRoute não podem coexistir para a mesma rede virtual. Para mais informações, consulte [os limites e limitações das ligações VPN expressRoute e do site-to-site](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como fazer conectar um aplicativo Web do serviço de aplicativo a uma rede virtual que tem um gateway de roteamento estático (baseado em políticas)?

Atualmente, não há suporte para a conexão de um aplicativo Web do serviço de aplicativo a uma rede virtual que tenha um gateway de roteamento estático (baseado em políticas). Se a rede virtual de destino já existir, ela deverá ter uma VPN ponto a site habilitada, com um gateway de roteamento dinâmico, antes que possa ser conectada a um aplicativo. Se o gateway estiver definido como roteamento estático, você não poderá habilitar uma VPN ponto a site. 

Para mais informações, consulte [Integrar uma aplicação com uma rede virtual Azure.](web-sites-integrate-with-vnet.md#getting-started)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Em minha Ambiente do Serviço de Aplicativo, por que posso criar apenas um plano do serviço de aplicativo, embora tenha dois trabalhadores disponíveis?

Para fornecer tolerância a falhas, Ambiente do Serviço de Aplicativo requer que cada pool de trabalho precise de pelo menos um recurso de computação adicional. O recurso de computação adicional não pode ser atribuído a uma carga de trabalho.

Para mais informações, consulte Como criar um Ambiente de [Serviço de Aplicações.](environment/app-service-web-how-to-create-an-app-service-environment.md)

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

Para mais informações, consulte [questões frequentes ao implementar (criando) um novo Ambiente](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/)de Serviço de Aplicações Azure .

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

Para obter mais informações sobre webJobs agendados, consulte [Criar um WebJob agendado utilizando uma expressão Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como fazer executar testes de penetração para meu aplicativo do serviço de aplicativo?

Para efetuar testes de penetração, [submeta um pedido](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como fazer configurar um nome de domínio personalizado para um aplicativo Web do serviço de aplicativo que usa o Gerenciador de tráfego?

Para aprender a usar um nome de domínio personalizado com uma aplicação de Serviço de Aplicações que utiliza o Gestor de Tráfego Azure para equilibrar a carga, consulte configurar um nome de [domínio personalizado para uma aplicação web Azure com traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Meu certificado do serviço de aplicativo está sinalizado para fraude. Como posso resolver isto?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante a verificação de domínio de uma compra de certificado do serviço de aplicativo, você poderá ver a seguinte mensagem:

"Seu certificado foi sinalizado para uma possível fraude. A solicitação está em análise no momento. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure.

Como a mensagem indica, esse processo de verificação de fraude pode levar até 24 horas para ser concluído. Durante esse tempo, você continuará a ver a mensagem.

Se o seu certificado de serviço de aplicativo continuar a mostrar esta mensagem após 24 horas, execute o seguinte script do PowerShell. O script contacta o fornecedor de [certificados](https://www.godaddy.com/) diretamente para resolver o problema.

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

Quando cria um novo website utilizando aplicações web em Azure, um *site padrão*de nome de azurewebsites.net é atribuído ao seu site. Se você adicionar um nome de host personalizado ao seu site e não quiser que os usuários possam acessar seu domínio padrão *. azurewebsites.net, você poderá redirecionar a URL padrão. Para aprender a redirecionar todo o tráfego do domínio padrão do seu website para o seu domínio personalizado, consulte [Redirecionar o domínio padrão para o seu domínio personalizado em aplicações web Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como fazer determinar qual versão da versão do .NET está instalada no serviço de aplicativo?

A maneira mais rápida de localizar a versão do Microsoft .NET que está instalada no serviço de aplicativo é usando o console do kudu. Você pode acessar o console do kudu no portal ou usando a URL do seu aplicativo do serviço de aplicativo. Para obter instruções detalhadas, consulte [determinar a versão .NET instalada no Serviço de Aplicações](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Por que o dimensionamento automático não funciona conforme o esperado?

Se o dimensionamento automático do Azure não tiver sido dimensionado ou dimensionado horizontalmente na instância do aplicativo Web conforme o esperado, você poderá estar se esgotando em um cenário no qual escolhemos intencionalmente não dimensionar para evitar um loop infinito devido à "oscilação". Isso geralmente acontece quando não há uma margem adequada entre os limites de expansão e redução. Para aprender a evitar "bater palmas" e ler sobre outras boas práticas de escala automática, consulte [as melhores práticas da Autoscale](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

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

Você também pode especificar os tipos MIME dinâmicos e estáticos específicos que deseja compactar. Para mais informações, consulte a nossa resposta a uma questão do fórum em [definições de httpCompression num simples website Azure.](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como fazer migrar de um ambiente local para o serviço de aplicativo?

Para migrar sites de servidores Web do Windows e Linux para o serviço de aplicativo, você pode usar Azure App Assistente de Migração de serviço. A ferramenta de migração cria aplicativos Web e bancos de dados no Azure conforme necessário e, em seguida, publica o conteúdo. Para mais informações, consulte O Assistente de [Migração do Serviço de Aplicações Azure](https://appmigration.microsoft.com/).
