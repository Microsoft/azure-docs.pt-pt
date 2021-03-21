---
title: Perguntas frequentes de configuração
description: Obtenha respostas para perguntas frequentes sobre questões de configuração e gestão para o Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 843e11e7f2df404a6bf1866da7b383ee9d1097de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574780"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Perguntas frequentes de configuração e gestão para aplicações web em Azure

Este artigo tem respostas para perguntas frequentes (PERGUNTAS Frequentes) sobre questões de configuração e gestão para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existem limitações que eu deva estar ciente se eu quiser mover recursos do Serviço de Aplicações?

Se planeia transferir os recursos do Serviço de Aplicações para um novo grupo de recursos ou subscrição, existem algumas limitações a ter em conta. Para mais informações, consulte [as limitações do Serviço de Aplicações.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como uso um nome de domínio personalizado para a minha aplicação web?

Para obter respostas a perguntas comuns sobre a utilização de um nome de domínio personalizado com a sua aplicação web Azure, consulte o nosso vídeo de sete minutos [Adicione um nome](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)de domínio personalizado . O vídeo oferece uma passagem de como adicionar um nome de domínio personalizado. Descreve como usar o seu próprio URL em vez do URL *.azurewebsites.net com a sua aplicação web do Serviço de Aplicações. Também pode ver uma passagem detalhada de [como mapear um nome](app-service-web-tutorial-custom-domain.md)de domínio personalizado.


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como posso comprar um novo domínio personalizado para a minha aplicação web?

Para aprender a comprar e configurar um domínio personalizado para a sua aplicação web do Serviço de Aplicações, consulte [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicações.](manage-custom-dns-buy-domain.md)


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Como faço o upload e configurar um certificado TLS/SSL existente para a minha aplicação web?

Para aprender a carregar e configurar um certificado TLS/SSL personalizado existente, consulte [adicionar um certificado TLS/SSL à sua aplicação de Serviço de Aplicações.](configure-ssl-certificate.md)


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Como posso comprar e configurar um novo certificado TLS/SSL em Azure para a minha aplicação web?

Para aprender a comprar e configurar um certificado TLS/SSL para a sua aplicação web Do Serviço de Aplicações, consulte [adicionar um certificado TLS/SSL à sua aplicação App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como movo os recursos de Application Insights?

Atualmente, a Azure Application Insights não suporta a operação de movimento. Se o seu grupo de recursos original incluir um recurso Application Insights, não poderá mover esse recurso. Se incluir o recurso Application Insights quando tenta mover uma aplicação do Serviço de Aplicações, toda a operação de movimento falha. No entanto, o Application Insights e o plano de Serviço de Aplicações não necessitam de estar no mesmo grupo de recursos que a aplicação para que a app funcione corretamente.

Para mais informações, consulte [as limitações do Serviço de Aplicações.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de orientação e saber mais sobre operações de movimentação de recursos?

[As limitações do Serviço de Aplicações](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) mostram-lhe como mover recursos para uma nova subscrição ou para um novo grupo de recursos na mesma subscrição. Pode obter informações sobre a lista de verificação de movimentos de recursos, saber quais os serviços que suportam a operação de movimento e saber mais sobre as limitações do Serviço de Aplicações e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como posso definir o fuso horário do servidor para a minha aplicação web?

Para definir o fuso horário do servidor para a sua aplicação web:

1. No portal Azure, na subscrição do Serviço de Aplicações, aceda ao menu **de definições de Aplicação.**
2. Nas **definições da App,** adicione esta definição:
    * Chave = WEBSITE_TIME_ZONE
    * Valor = *O fuso horário que deseja*
3. Selecione **Guardar**.

Para os serviços de Aplicação que funcionam no Windows, consulte a saída do `tzutil /L` comando Windows. Utilize o valor da segunda linha de cada entrada. Por exemplo: "Tempo Padrão de Tonga". Alguns destes valores também estão listados na coluna **Timezone** em [Fusos Horários Predefinidos](/windows-hardware/manufacture/desktop/default-time-zones).

Para os serviços de Aplicação que funcionam no Linux, desaprova um valor a partir da base de [dados IANA TZ.](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) Por exemplo: "América/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Porque é que os meus Contínuos WebJobs às vezes falham?

Por padrão, as aplicações web são descarregadas se estiverem inativas por um período de tempo definido. Isto permite que o sistema conserva recursos. Nos planos Basic e Standard, pode ligar a definição **Always On** para manter a aplicação web carregada a toda a hora. Se a sua aplicação web executar WebJobs contínuos, deve ligar **Always On**, ou os WebJobs podem não funcionar de forma fiável. Para obter mais informações, consulte [Criar um WebJob em execução contínua.](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como consigo o endereço IP de saída para a minha aplicação web?

Para obter a lista de endereços IP de saída para a sua aplicação web:

1. No portal Azure, na sua lâmina de aplicação web, aceda ao menu **Propriedades.**
2. Procure **endereços IP de saída.**

A lista de endereços IP de saída aparece.

Para aprender a obter o endereço IP de saída se o seu website estiver hospedado num Ambiente de Serviço de Aplicações, consulte [endereços de rede de saída](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como consigo um endereço IP de entrada reservado ou dedicado para a minha aplicação web?

Para configurar um endereço IP dedicado ou reservado para chamadas de entrada feitas no seu website de aplicações Azure, instale e configuure um certificado TLS/SSL baseado em IP.

Note que para utilizar um endereço IP dedicado ou reservado para chamadas de entrada, o seu plano de Serviço de Aplicações deve estar num plano de serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Posso exportar o meu certificado de Serviço de Aplicações para usar fora do Azure, como para um site hospedado em outro lugar? 

Sim, pode exportá-los para usar fora de Azure. Para obter mais informações, consulte [as PERGUNTAS Frequentes para certificados de Serviço de Aplicações e domínios personalizados.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Posso exportar o meu certificado de Serviço de Aplicações para usar com outros serviços em nuvem Azure?

O portal proporciona uma experiência de primeira classe para a implementação de um certificado de Serviço de Aplicações através do Cofre de Chaves Azure para aplicações do Serviço de Aplicações. No entanto, temos vindo a receber pedidos de clientes para utilizar estes certificados fora da plataforma do Serviço de Aplicações, por exemplo, com máquinas virtuais Azure. Para aprender a criar uma cópia PFX local do seu certificado de Serviço de Aplicações para que possa utilizar o certificado com outros recursos Azure, consulte [Criar uma cópia PFX local de um certificado de Serviço de Aplicação.](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)

Para obter mais informações, consulte [as PERGUNTAS Frequentes para certificados de Serviço de Aplicações e domínios personalizados.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que vejo a mensagem "Parcialmente Bem-sucedido" quando tento fazer o back up da minha aplicação web?

Uma causa comum de falha de backup é que alguns ficheiros estão a ser utilizados pela aplicação. Os ficheiros que estão a ser utilizados estão bloqueados enquanto executa a cópia de segurança. Isto evita que estes ficheiros sejam apoiados e pode resultar num estado "Parcialmente Bem Sucedido". Pode potencialmente evitar que isso aconteça excluindo ficheiros do processo de cópia de segurança. Podes escolher apoiar apenas o que for necessário. Para obter mais informações, consulte [backup apenas as partes importantes do seu site com aplicações web Azure.](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como retiro um cabeçalho da resposta HTTP?

Para remover os cabeçalhos da resposta HTTP, atualize o ficheiro web.config do seu site. Para obter mais informações, consulte [remover os cabeçalhos padrão do servidor nos seus websites Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>O Serviço de Aplicações está em conformidade com a Norma PCI 3.0 e 3.1?

Atualmente, a funcionalidade de Aplicações Web do Azure App Service está em conformidade com a versão 3.0 nível 3.0 do PCI Data Security Standard (DSS). A versão 3.1 do PCI DSS está no nosso roteiro. Já está em curso o planeamento para a forma como a adoção da última norma irá prosseguir.

A certificação PCI DSS versão 3.1 requer a desativação da Segurança da Camada de Transporte (TLS) 1.0. Atualmente, desativar o TLS 1.0 não é uma opção para a maioria dos planos do Serviço de Aplicações. No entanto, se utilizar o App Service Environment ou estiver disposto a migrar a sua carga de trabalho para o App Service Environment, poderá obter um maior controlo do seu ambiente. Isto envolve desativar o TLS 1.0 contactando o Suporte Azure. Num futuro próximo, pretendemos tornar estas configurações acessíveis aos utilizadores.

Para obter mais informações, consulte [a aplicação web do Microsoft Azure App Service, cumprindo o PCI Standard 3.0 e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como uso o ambiente de preparação e as ranhuras de implantação?

Nos planos Standard e Premium App Service, quando implementa a sua aplicação web para o App Service, pode implementar para uma faixa de implementação separada em vez de para a ranhura de produção predefinido. As slots de implementação são aplicações web ao vivo que têm os seus próprios nomes de anfitrião. O conteúdo da aplicação web e os elementos de configuração podem ser trocados entre duas ranhuras de implementação, incluindo a ranhura de produção.

Para obter mais informações sobre a utilização de slots de implementação, consulte [Configurar um ambiente de preparação no Serviço de Aplicações.](deploy-staging-slots.md)

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como acedo e revê registos webJob?

Para rever os registos da WebJob:

1. Inscreva-se no seu **website da Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
2. Selecione o WebJob.
3. Selecione o botão **de saída toggle.**
4. Para descarregar o ficheiro de saída, selecione o link **Descarregar.**
5. Para execuções individuais, **selecione Individual Invoke**.
6. Selecione o botão **de saída toggle.**
7. Selecione o link de descarregamento.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou a tentar usar ligações híbridas com o SQL Server. Por que vejo a mensagem "System.OverflowException: Aritmética operação resultou num transbordo"?

Se utilizar Ligações Híbridas para aceder ao SQL Server, uma atualização Microsoft .NET no dia 10 de maio de 2016, poderá causar a falha das ligações. Pode ver esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gestor de Ligação Híbrido que entretanto foi corrigido. Certifique-se de [atualizar o seu Gestor de Ligação Híbrido](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver este problema.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Como adiciono uma regra de reescrita de URL?

Para adicionar uma regra de reescrita de URL, crie um ficheiro web.config com as entradas config relevantes na pasta **wwwroot.** Para obter mais informações, consulte [os Serviços de Aplicações Azure: Compreensão do URL.](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como controlo o tráfego de entrada para o Serviço de Aplicações?

Ao nível do site, tem duas opções para controlar o tráfego de entrada no Serviço de Aplicações:

* Ligue as restrições dinâmicas de IP. Para aprender a ligar restrições dinâmicas de IP, consulte [as restrições ip e de domínio para websites Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ligue a Segurança do Módulo. Para aprender a ligar a Segurança do Módulo, consulte a [firewall da aplicação web ModSecurity nos websites da Azure.](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)

Se utilizar o App Service Environment, pode utilizar [firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como posso bloquear portas numa aplicação web do Serviço de Aplicações?

No ambiente de inquilino partilhado do Serviço de Aplicações, não é possível bloquear portos específicos devido à natureza da infraestrutura. As portas TCP 4020, 4022 e 4024 também podem estar abertas para depurações remotas do Visual Studio.

No Ambiente de Serviço de Aplicações, você tem controlo total sobre o tráfego de entrada e saída. Pode utilizar grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre o Ambiente de Serviço de Aplicações, consulte [a introdução do Ambiente de Serviço de Aplicações.](https://azure.microsoft.com/blog/introducing-app-service-environment/)

## <a name="how-do-i-capture-an-f12-trace"></a>Como posso capturar um vestígio de F12?

Tem duas opções para capturar um vestígio de F12:

* Vestígios de F12 HTTP
* Saída da consola F12

### <a name="f12-http-trace"></a>Vestígios de F12 HTTP

1. No Internet Explorer, vá ao seu site. É importante entrar antes de fazer os próximos passos. Caso contrário, o rastreio de F12 captura dados sensíveis de inscrição.
2. Pressione F12.
3. Verifique se o separador **'Rede'** está selecionado e, em seguida, selecione o botão **de Reprodução** verde.
4. Faça os passos que reproduzem a questão.
5. Selecione o **botão de paragem** vermelho.
6. Selecione o botão **Guardar** (ícone do disco) e guarde o ficheiro HAR (no Internet Explorer e Microsoft Edge) *ou* clique com o botão HAR e, em seguida, selecione **Guardar como HAR com conteúdo** (no Chrome).

### <a name="f12-console-output"></a>Saída da consola F12

1. Selecione o **separador Consola.**
2. Para cada separador que contenha mais de zero itens, selecione o separador **(Erro**, **Aviso** ou **Informação).** Se o separador não for selecionado, o ícone do separador é cinzento ou preto quando afasta o cursor.
3. Clique com o botão direito na área da mensagem do painel e, em seguida, selecione **Copiar tudo**.
4. Cole o texto copiado num ficheiro e guarde o ficheiro.

Para visualizar um ficheiro HAR, pode utilizar o [espectador HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que tenho um erro quando tento ligar uma aplicação do Serviço de Aplicações a uma rede virtual que está ligada ao ExpressRoute?

Se tentar ligar uma aplicação web Azure a uma rede virtual que está ligada ao Azure ExpressRoute, falha. A seguinte mensagem aparece: "Gateway não é uma porta de entrada VPN."

Atualmente, não é possível ter ligações VPN ponto a local a uma rede virtual que está ligada ao ExpressRoute. Uma VPN ponto-a-local e ExpressRoute não podem coexistir para a mesma rede virtual. Para obter mais informações, consulte [expressRoute e as ligações VPN site-to-site limits e limitações](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como posso ligar uma aplicação web do Serviço de Aplicações a uma rede virtual que tem um gateway de encaminhamento estático (baseado em políticas) ?

Atualmente, não é suportada a ligação de uma aplicação do Serviço de Aplicações a uma rede virtual que tenha um gateway de encaminhamento estático (baseado em políticas). Se a sua rede virtual alvo já existir, deve ter VPN ponto a local ativado, com um gateway de encaminhamento dinâmico, antes de poder ser ligado a uma aplicação. Se o seu gateway estiver definido para encaminhamento estático, não pode ativar uma VPN ponto a local. 

Para obter mais informações, consulte [Integrar uma aplicação com uma rede virtual Azure.](web-sites-integrate-with-vnet.md)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>No meu Ambiente de Serviço de Aplicações, por que posso criar apenas um plano de Serviço de Aplicações, mesmo tendo dois trabalhadores disponíveis?

Para fornecer tolerância a falhas, o App Service Environment requer que cada grupo de trabalhadores precise de pelo menos um recurso adicional de computação. O recurso adicional de computação não pode ser atribuído a uma carga de trabalho.

Para mais informações, consulte [Como criar um Ambiente de Serviço de Aplicações.](environment/app-service-web-how-to-create-an-app-service-environment.md)

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Por que vejo intervalos de tempo quando tento criar um Ambiente de Serviço de Aplicações?

Às vezes, a criação de um Ambiente de Serviço de Aplicações falha. Nesse caso, vê-se o seguinte erro nos registos de Atividade:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Para resolver isto, certifique-se de que nenhuma das seguintes condições é verdadeira:
* A sub-rede é muito pequena.
* A sub-rede não está vazia.
* O ExpressRoute impede os requisitos de conectividade da rede de um Ambiente de Serviço de Aplicações.
* Um grupo de segurança de rede mau impede os requisitos de conectividade da rede de um Ambiente de Serviço de Aplicações.
* Os túneis forçados estão ligados.

Para obter mais informações, consulte [questões frequentes ao implementar (criando) um novo Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Por que não posso apagar o meu plano de Serviço de Aplicações?

Não é possível eliminar um plano de Serviço de Aplicações se alguma aplicação do Serviço de Aplicações estiver associada ao plano de Serviço de Aplicações. Antes de eliminar um plano de Serviço de Aplicações, remova todas as aplicações associadas do Serviço de Aplicações do plano de Serviço de Aplicações.

## <a name="how-do-i-schedule-a-webjob"></a>Como posso agendar um WebJob?

Pode criar um WebJob programado utilizando expressões cron:

1. Crie um ficheiro de definições.job.
2. Neste ficheiro JSON, inclua uma propriedade de agenda através de uma expressão Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Para obter mais informações sobre WebJobs programados, consulte [Criar um WebJob programado utilizando uma expressão Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como faço os testes de penetração para a minha aplicação app App Service?

Para realizar testes de penetração, [apresente um pedido](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como posso configurar um nome de domínio personalizado para uma aplicação web do Serviço de Aplicações que utiliza o Traffic Manager?

Para aprender a usar um nome de domínio personalizado com uma aplicação de Serviço de Aplicações que utiliza o Azure Traffic Manager para equilibrar a carga, consulte [configurar um nome de domínio personalizado para uma aplicação web Azure com o Traffic Manager.](configure-domain-traffic-manager.md)

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>O meu certificado do Serviço de Aplicações está sinalizado por fraude. Como posso resolver isto?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante a verificação de domínio de uma compra de certificado do Serviço de Aplicações, poderá ver a seguinte mensagem:

"O seu certificado foi sinalizado por possível fraude. O pedido está atualmente a ser analisado. Se o certificado não se tornar utilizável dentro de 24 horas, contacte o Suporte Azure."

Como a mensagem indica, este processo de verificação de fraude pode demorar até 24 horas para ser concluído. Durante este tempo, continuará a ver a mensagem.

Se o seu certificado de Serviço de Aplicações continuar a mostrar esta mensagem após 24 horas, por favor, execute o seguinte script PowerShell. O script contacta diretamente o [fornecedor de certificados](https://www.godaddy.com/) para resolver o problema.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Como funciona a autenticação e autorização no Serviço de Aplicações?

Para documentação detalhada para autenticação e autorização no Serviço de Aplicações, consulte os documentos para vários registos do fornecedor de identificação:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Conta Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Como redirecionar o domínio padrão *.azurewebsites.net para o domínio personalizado da minha aplicação web Azure?

Quando cria um novo website utilizando aplicações web em Azure, um nome de *site* padrão .azurewebsites.net domínio é atribuído ao seu site. Se adicionar um nome de anfitrião personalizado ao seu site e não quiser que os utilizadores possam aceder ao seu domínio padrão *.azurewebsites.net, pode redirecionar o URL predefinido. Para aprender a redirecionar todo o tráfego do domínio padrão do seu website para o seu domínio personalizado, consulte [redirecionar o domínio padrão para o seu domínio personalizado em aplicações web Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como determino qual versão da versão .NET está instalada no Serviço de Aplicações?

A forma mais rápida de encontrar a versão do Microsoft .NET que está instalada no Serviço de Aplicações é utilizando a consola Kudu. Pode aceder à consola Kudu a partir do portal ou utilizando o URL da sua aplicação App Service. Para obter instruções detalhadas, consulte [determine a versão .NET instalada no Serviço de Aplicações.](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services)

## <a name="why-isnt-autoscale-working-as-expected"></a>Porque é que a Autoscale não está a funcionar como esperado?

Se o Azure Autoscale não tiver escalado ou dimensionado a instância da aplicação web como esperava, poderá estar a deparar-se com um cenário em que optamos intencionalmente por não escalar para evitar um loop infinito devido a "bater palmas". Isto geralmente acontece quando não há uma margem adequada entre os limiares de escala e escala. Para aprender a evitar "bater palmas" e ler sobre outras melhores práticas da Autoscale, consulte as [melhores práticas da Autoscale](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Por que a Autoscale às vezes escala apenas parcialmente?

A autoescala é ativada quando as métricas excedem os limites pré-configurados. Às vezes, pode notar que a capacidade só está parcialmente preenchida em comparação com o que esperava. Isto pode ocorrer quando o número de casos que deseja não estiver disponível. Nesse cenário, a Autoscale preenche parcialmente o número disponível de instâncias. Em seguida, a autoescalação executa a lógica de reequilíbrio para obter mais capacidade. Atribui as restantes instâncias. Note que isto pode levar alguns minutos.

Se não vir o número esperado de casos após alguns minutos, pode ser porque a recarga parcial foi suficiente para colocar as métricas dentro dos limites. Ou, a Autoscale pode ter diminuído porque atingiu o limite de métricas mais baixo.

Se nenhuma destas condições se aplicar e o problema persistir, apresente um pedido de apoio.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Como posso ligar a compressão HTTP para o meu conteúdo?

Para ligar a compressão tanto para tipos de conteúdo estático como dinâmico, adicione o seguinte código ao ficheiro web.config de nível de aplicação:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Também pode especificar os tipos de MIME dinâmicos e estáticos específicos que pretende comprimir. Para mais informações, consulte a nossa resposta a uma pergunta do fórum em [httpCompression configurações num simples website da Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como posso migrar de um ambiente no local para o Serviço de Aplicações?

Para migrar sites de servidores web Windows e Linux para o Serviço de Aplicações, pode utilizar o Assistente de Migração do Serviço de Aplicações Azure. A ferramenta de migração cria aplicações web e bases de dados em Azure, conforme necessário, e depois publica o conteúdo. Para mais informações, consulte [o Assistente de Migração do Serviço de Aplicações Azure.](https://appmigration.microsoft.com/)

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Por que motivo o meu certificado foi emitido para 11 meses e não para um ano inteiro?

Para todos os certificados emitidos após 01/09/2020, a duração máxima é agora de 397 dias. Os certificados emitidos antes de 01/09/2020 têm uma validade máxima de 825 dias até serem renovados, terem a chave reintroduzida, etc. Qualquer certificado renovado após 01/09/2020 será afetado por esta alteração e os utilizadores poderão observar uma validade mais curta nos certificados renovados.
O GoDaddy implementou um serviço de subscrição que cumpre os novos requisitos enquanto respeita os certificados de cliente existentes. Trinta dias antes da expiração do certificado emitido recentemente, o serviço emite automaticamente um segundo certificado que prolonga a duração até à data de expiração original. O Serviço de Aplicações está a trabalhar com o GoDaddy para contornar esta alteração e garantir que os clientes recebem a duração completa dos certificados.
