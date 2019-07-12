---
title: FAQs sobre a configuração - serviço de aplicações do Azure | Documentos da Microsoft
description: Obtenha respostas às perguntas mais frequentes sobre problemas de configuração e gestão para a funcionalidade de aplicações Web do serviço de aplicações do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ec2580f0c71c98c7a03f3326cb1b8ff539109ee0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720022"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>FAQs sobre a configuração e gestão de aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de configuração e gestão para o [funcionalidade de aplicações Web do serviço de aplicações do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existem limitações que deve ter conhecimento se eu quiser mover os recursos de serviço de aplicações?

Se pretender mover os recursos de serviço de aplicações para um novo grupo de recursos ou subscrição, existem algumas limitações a ter em consideração. Para obter mais informações, consulte [limitações do serviço de aplicações](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como posso utilizar um nome de domínio personalizado para a minha aplicação web?

Para respostas a perguntas comuns sobre como utilizar um nome de domínio personalizado com a sua aplicação web do Azure, consulte o nosso vídeo de sete minutos [adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). O vídeo oferece instruções sobre como adicionar um nome de domínio personalizado. Ele descreve como utilizar o seu URL em vez do *. URL azurewebsites.net com a sua aplicação web do serviço de aplicações. Também pode ver um passo a passo detalhada da [como mapear um nome de domínio personalizado](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como posso comprar um domínio personalizado novo para a minha aplicação web?

Para saber como comprar e configurar um domínio personalizado para a sua aplicação web do serviço de aplicações, veja [comprar e configurar um nome de domínio personalizado no serviço de aplicações](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Como carregar e configurar um certificado SSL existente para a minha aplicação web?

Para saber como carregar e configurar um certificado SSL personalizado já existente, veja [vincular um certificado SSL personalizado já existente a uma aplicação web do Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Como comprar e configurar um novo certificado SSL no Azure para a minha aplicação web?

Para saber como comprar e configurar um certificado SSL para a sua aplicação web do serviço de aplicações, veja [adicionar um certificado SSL ao seu serviço de aplicações](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como faço para mover recursos do Application Insights?

Atualmente, o Azure Application Insights não suporta a operação de movimentação. Se o seu grupo de recursos original inclui um recurso do Application Insights, não é possível mover esse recurso. Se incluir o recurso do Application Insights ao tentar mover uma aplicação de serviço de aplicações, toda a mover a falha da operação. No entanto, o Application Insights e o plano de serviço de aplicações não é necessário estar no mesmo grupo de recursos que a aplicação para a aplicação a funcionar corretamente.

Para obter mais informações, consulte [limitações do serviço de aplicações](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de verificação de orientação e saiba mais sobre os recursos a mover operações?

[Limitações do serviço de aplicações](../azure-resource-manager/move-limitations/app-service-move-limitations.md) mostra como mover recursos para uma nova subscrição ou para um novo grupo de recursos na mesma subscrição. Pode obter informações sobre a lista de verificação de movimentação do recurso, saiba quais os serviços que suportam a operação de movimentação e saber mais sobre as limitações de serviço de aplicações e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como posso definir o fuso horário do servidor para a minha aplicação web?

Para definir o fuso horário do servidor para a sua aplicação web:

1. No portal do Azure, na sua subscrição do serviço de aplicações, vá para o **as definições da aplicação** menu.
2. Sob **as definições da aplicação**, adicione esta definição:
    * Key = WEBSITE_TIME_ZONE
    * Valor = *o fuso horário que pretende*
3. Selecione **Guardar**.

Consulte a **fuso horário** coluna na [predefinido fusos horários](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) artigo para valores aceites.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Por que minha WebJobs contínuos, às vezes, falhar?

Por predefinição, aplicações web são descarregadas que estejam Inativos durante um determinado período de tempo. Isso permite que o sistema poupar recursos. Nos planos básico e Standard, pode ativar a **Always On** definição para que o aplicativo web carregado o tempo todo. Se a sua aplicação web executa trabalhos Web contínuos, deve ativar **Always On**, ou os trabalhos Web poderão não ser fiável. Para obter mais informações, consulte [criar um WebJob em execução contínua](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como posso obter o endereço IP de saída para a minha aplicação web?

Para obter a lista de endereços IP de saída para a sua aplicação web:

1. No portal do Azure, no painel da aplicação web, vá para o **propriedades** menu.
2. Procure **endereços ip de saída**.

É apresentada a lista de endereços IP de saída.

Para saber como obter o endereço IP de saída, se seu Web site estiver alojado num ambiente de serviço de aplicações, veja [endereços de rede de saída](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como posso obter um endereço IP entrado reservado ou dedicado para a minha aplicação web?

Para configurar um endereço IP reservado ou dedicado para chamadas de entrada feitas ao seu Web site de aplicações do Azure, instalar e configurar um certificado SSL baseado em IP.

Tenha em atenção que para utilizar um endereço IP reservado ou dedicado para chamadas de entrada, seu plano do serviço de aplicações tem de ser num plano do serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Pode exportar meu certificado de serviço de aplicações para utilizar fora do Azure, tal como para um Web site alojado noutro local? 

Certificados de serviço de aplicações são considerados de recursos do Azure. Estas não recomendações pretendem para utilizar fora de seus serviços do Azure. Não é possível exportar-os para utilizar fora do Azure. Para obter mais informações, consulte [FAQs para certificados de serviço de aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Pode exportar meu certificado de serviço de aplicações para utilizar com outros serviços cloud do Azure?

O portal fornece uma experiência de primeira classe para implementar um certificado de serviço de aplicações através do Azure Key Vault para aplicações de serviço de aplicações. No entanto, temos sido contactados pedidos de clientes para utilizar estes certificados fora da plataforma de serviço de aplicações, por exemplo, com máquinas virtuais do Azure. Para saber como criar uma cópia PFX local do seu certificado de serviço de aplicações, pelo que pode utilizar o certificado com outros recursos do Azure, veja [criar uma cópia PFX local de um certificado de serviço de aplicações](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Para obter mais informações, consulte [FAQs para certificados de serviço de aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que vejo a mensagem "Êxito parcial" quando tento fazer cópias de segurança da minha aplicação web?

Uma causa comum de falha de cópia de segurança é que alguns ficheiros estão em utilização pela aplicação. Ficheiros que estão em utilização são bloqueados enquanto executa a cópia de segurança. Isso impede que esses arquivos a cópia de segurança e poderá resultar num Estado de "Êxito parcial". Pode potencialmente impedir que tal aconteça excluindo ficheiros do processo de cópia de segurança. Pode optar por criar cópias de segurança apenas o que é necessário. Para obter mais informações, consulte [apenas as partes importantes do seu site com aplicações web do Azure de cópia de segurança](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como posso remover um cabeçalho de resposta HTTP?

Para remover os cabeçalhos de resposta HTTP, atualize o ficheiro Web. config de seu site. Para obter mais informações, consulte [remover os cabeçalhos de servidor padrão em Web sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>É o serviço de aplicações em conformidade com PCI 3.0 de padrão e 3.1?

Atualmente, a funcionalidade de aplicações Web do serviço de aplicações do Azure está em conformidade com a versão do PCI Data Security Standard (DSS) 3.0 nível 1. PCI DSS versão 3.1 está no nosso plano. Planejamento já está em andamento para como irá continuar a adoção do padrão mais recente.

Certificação de versão 3.1 da PCI DSS requer a desativação de Transport Layer Security (TLS) 1.0. Atualmente, a desativação de TLS 1.0 não é uma opção para a maioria dos planos do serviço de aplicações. No entanto, se utilizar o ambiente de serviço de aplicações ou está disposto a migrar a sua carga de trabalho para o ambiente de serviço de aplicações, pode obter maior controle do seu ambiente. Isso envolve desabilitar o TLS 1.0 ao contactar o suporte do Azure. Em breve, planeamos disponibilizar estas definições aos utilizadores.

Para obter mais informações, consulte [compatibilidade das aplicações de web do serviço de aplicações do Microsoft Azure com 3.0 padrão da PCI e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como utilizo os blocos de implementação e de ambiente de teste?

Nos planos Standard e Premium do serviço de aplicações, quando implementar a aplicação web no serviço de aplicações, pode implementar para um bloco de implementação separados em vez de para o bloco de produção predefinido. Blocos de implementação são aplicações web em direto com seus próprios nomes de anfitrião. Elementos de conteúdo e a configuração da aplicação Web podem ser trocados entre duas ranhuras de implementação, incluindo o bloco de produção.

Para obter mais informações sobre como utilizar blocos de implementação, consulte [configurar um ambiente de teste no serviço de aplicações](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como aceder e reveja a registos WebJob?

Para rever a registos WebJob:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o WebJob.
3. Selecione o **saída de alternância** botão.
4. Para transferir o ficheiro de saída, selecione o **transferir** ligação.
5. Para execuções de individuais, selecione **invocar individuais**.
6. Selecione o **saída de alternância** botão.
7. Selecione o link de download.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou a tentar utilizar ligações híbridas com o SQL Server. Por que razão vejo a mensagem "System.OverflowException: Operação aritmética resultou num estouro"?

Se utilizar ligações híbridas para aceder ao SQL Server, uma atualização do Microsoft .NET em 10 de Maio de 2016, pode fazer com que as ligações efetuar a ativação. Poderá ver esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gestor de ligações híbridas que que foi então corrigida. Não se esqueça [atualizar o seu Gestor de ligações híbridas](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver este problema.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Como adicionar ou editar uma regra de reescrita de URL?

Para adicionar ou editar uma regra de reescrita de URL:

1. Configure o Gestor de serviços de informação Internet (IIS), de modo a que se liga à sua aplicação web do serviço de aplicações. Para saber como ligar o Gestor do IIS ao serviço de aplicações, veja [administração remota do Web sites do Azure utilizando o Gestor de IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. No Gestor de IIS, adicionar ou editar uma regra de reescrita de URL. Para saber como adicionar ou editar uma regra de reescrita de URL, veja [criar regras de reescrita para o URL rewrite módulo](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como posso controlar o tráfego de entrada no serviço de aplicações?

Ao nível do site, tem duas opções para controlar o tráfego de entrada no serviço de aplicações:

* Ative restrições de IP dinâmicas. Para saber como ativar restrições de IP dinâmicas, consulte [restrições de IP e domínio nos Web sites Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ative o módulo de segurança. Para saber como ativar o módulo de segurança, veja [ModSecurity firewall de aplicações web em Web sites do Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se utilizar o ambiente de serviço de aplicações, pode utilizar [Barracuda firewall](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como posso bloquear portas numa aplicação web do serviço de aplicações?

No ambiente de inquilino partilhado do serviço de aplicações, não é possível bloquear portas específicas devido à natureza da infraestrutura. As portas TCP 4016, 4018 e 4020 também podem ser abertas para a depuração remota do Visual Studio.

No ambiente de serviço de aplicações, tem controlo total sobre o tráfego de entrada e saído. Pode utilizar grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre o ambiente de serviço de aplicações, consulte [introdução ao ambiente de serviço de aplicações](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Como posso capturar um rastreamento de F12?

Tem duas opções para capturar um rastreamento de F12:

* Rastreio de HTTP de F12
* Resultado da consola de F12

### <a name="f12-http-trace"></a>Rastreio de HTTP de F12

1. No Internet Explorer, aceda ao seu Web site. É importante iniciar sessão antes de efetuar os passos seguintes. Caso contrário, o rastreio de F12 captura dados de início de sessão confidenciais.
2. Premir a tecla F12.
3. Certifique-se de que o **rede** separador está selecionado e, em seguida, selecione o verde **reproduzir** botão.
4. Siga os passos que reproduza o problema.
5. Selecione o vermelho **parar** botão.
6. Selecione o **salvar** botão (ícone de disco) e guarde o ficheiro HAR (no Internet Explorer e o Microsoft Edge) *ou* o ficheiro HAR com o botão direito e, em seguida, selecione **guardar como HAR com conteúdo**(no Chrome).

### <a name="f12-console-output"></a>Resultado da consola de F12

1. Selecione o **consola** separador.
2. Para cada separador que contém mais do que zero itens, selecione o separador (**erro**, **aviso**, ou **informações**). Se o separador não estiver selecionado, o ícone de separador é preto ou branco cinzento ao mover o cursor para fora.
3. Faça duplo clique na área de mensagens do painel e, em seguida, selecione **copie todos os**.
4. Colar o texto copiado num arquivo e, em seguida, guarde o ficheiro.

Para ver um ficheiro HAR, pode utilizar o [Visualizador HAR](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que motivo recebo um erro quando tento me conectar um serviço de aplicações web de aplicação a uma rede virtual que está ligada ao ExpressRoute?

Se tentar ligar a uma aplicação web do Azure a uma rede virtual que tenha ligado ao Azure ExpressRoute, falhará. É apresentada a seguinte mensagem: "Gateway não é um gateway de VPN."

Atualmente, não pode ter ligações de VPN ponto a site a uma rede virtual que está ligada ao ExpressRoute. Um ponto-para-site VPN e ExpressRoute não podem coexistir para a mesma rede virtual. Para obter mais informações, consulte [ExpressRoute e VPN site a site limites de ligações e limitações](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como posso ligar uma aplicação do serviço de aplicações web a uma rede virtual que tenha um gateway (baseado em políticas) de encaminhamento estático?

Atualmente, uma aplicação do serviço de aplicações web a ligar a uma rede virtual que tenha um gateway (baseado em políticas) de encaminhamento estático não é suportada. Se a rede virtual de destino já existir, tem de ter ativado, com um gateway de encaminhamento dinâmico, antes de ele pode ser conectado a uma aplicação VPN de ponto a site. Se o gateway estiver definido como encaminhamento estático, não é possível ativar uma VPN ponto a site. 

Para obter mais informações, consulte [integrar uma aplicação com uma Azure virtual network](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>No meu ambiente de serviço de aplicações, por que é possível criar apenas um plano de serviço de aplicações, embora tenha dois trabalhos disponíveis?

Para fornecer tolerância a falhas, o ambiente de serviço de aplicações requer que cada conjunto de trabalho necessita de pelo menos um recurso de computação adicionais. O recurso de computação adicionais não pode ser atribuído uma carga de trabalho.

Para obter mais informações, consulte [como criar um ambiente de serviço de aplicações](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Por que vejo tempos limite ao tentar criar um ambiente de serviço de aplicações?

Às vezes, a criação de um ambiente de serviço de aplicação falha. Nesse caso, verá o seguinte erro nos registos de atividade:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Para resolver este problema, certifique-se de que nenhuma das condições seguintes forem verdadeiras:
* A sub-rede é demasiado pequena.
* A sub-rede não está vazia.
* ExpressRoute impede que os requisitos de conectividade de rede de um ambiente de serviço de aplicações.
* Um grupo de segurança de rede ruim impede que os requisitos de conectividade de rede de um ambiente de serviço de aplicações.
* O túnel forçado é ativado.

Para obter mais informações, consulte [problemas frequentes ao implementar (criar) um novo ambiente do serviço de aplicações do Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Por que não é possível eliminar o meu plano de serviço de aplicações?

Não é possível eliminar um plano do serviço de aplicações se todas as aplicações de serviço de aplicações estão associadas com o plano do serviço de aplicações. Antes de eliminar um plano do serviço de aplicações, remova todas as aplicações de serviço de aplicações associadas do plano de serviço de aplicações.

## <a name="how-do-i-schedule-a-webjob"></a>Como faço para agendar um trabalho Web?

Pode criar um WebJob agendado com expressões Cron:

1. Crie um ficheiro Settings.
2. Neste ficheiro de JSON, incluem uma propriedade de agenda, utilizando uma expressão Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Para obter mais informações sobre trabalhos Web agendados, consulte [criar um WebJob agendado com uma expressão Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como posso executar testes para a minha aplicação de serviço de aplicações de penetração?

Para executar testes de penetração [submeter um pedido](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como posso configurar um nome de domínio personalizado para uma aplicação de web do serviço de aplicações que utiliza o Gestor de tráfego?

Para saber como utilizar um nome de domínio personalizado com uma aplicação de serviço de aplicações que utiliza o Gestor de tráfego do Azure para o balanceamento de carga, veja [configurar um nome de domínio personalizado para uma aplicação web do Azure com o Gestor de tráfego](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Meu certificado de serviço de aplicações é sinalizado por fraude. Como posso resolver isto?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante a verificação de domínio de uma compra do certificado de serviço de aplicações, poderá ver a mensagem seguinte:

"O certificado foi sinalizado como possível fraude. O pedido está atualmente a ser analisadas. Se o certificado se tornar utilizável dentro de 24 horas, contacte o suporte do Azure."

Como a mensagem indica, este processo de verificação de fraude poderá demorar até 24 horas a concluir. Durante este período, irá continuar a ver a mensagem.

Se o certificado do serviço de aplicação continua a mostrar esta mensagem após 24 horas, execute o seguinte script do PowerShell. Os contactos do script da [fornecedor de certificados](https://www.godaddy.com/) diretamente para resolver o problema.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Como a autenticação e autorização funcionam no serviço de aplicações?

Para obter documentação detalhada para autenticação e autorização no serviço de aplicações, consulte documentos para vários identificar inícios de sessão do fornecedor:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Conta Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Como redirecionar o padrão *. domínio azurewebsites.net para o domínio personalizado da minha aplicação web do Azure?

Quando cria um novo Web site através da utilização de aplicações Web no Azure, um padrão *sitename*. domínio azurewebsites.net é atribuído ao seu site. Se adicionar um nome de anfitrião personalizado ao seu site e não quiser que os utilizadores possam acessar seu padrão *. domínio azurewebsites.net, pode redirecionar o URL predefinido. Para saber como redirecionar todo o tráfego de domínio do seu Web site predefinido ao seu domínio personalizado, veja [redirecionar o domínio predefinido para o seu domínio personalizado em aplicações web do Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como posso determinar qual é a versão do .NET está instalada a versão no serviço de aplicações?

É a forma mais rápida para localizar a versão do Microsoft .NET que está instalado no serviço de aplicações utilizando a consola Kudu. Pode aceder a consola Kudu a partir do portal ou utilizando o URL da sua aplicação de serviço de aplicações. Para obter instruções detalhadas, consulte [determinar a versão instalada do .NET no serviço de aplicações](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Por que o dimensionamento automático não está funcionando como esperado?

Se o dimensionamento automático do Azure ainda não reduzido horizontalmente ou aumentar horizontalmente a instância da aplicação web conforme esperado, pode estar em execução num cenário em que podemos intencionalmente optar por não ser dimensionado para evitar um loop infinito devido a "em oscilação." Normalmente, isso acontece quando existem não é uma margem adequada entre os limiares de aumento horizontal e a redução horizontal. Para saber como evitar "em oscilação" e leia sobre outras práticas recomendadas de dimensionamento automático, veja [melhores práticas de dimensionamento automático](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Por que motivo dimensionamento automático, às vezes, dimensionar apenas parcialmente?

Dimensionamento automático é acionado quando métricas excederem os limites pré-configurada. Às vezes, pode observar que a capacidade é apenas parcialmente preenchida em comparação com o que esperava. Esta situação pode ocorrer quando o número de instâncias que pretende que não está disponível. Nesse cenário, o dimensionamento automático parcialmente preenche com o número de disponibilidade de instâncias. Dimensionamento automático, em seguida, executa a lógica de rebalanceamento para obter mais capacidade. Ele aloca as muitas instâncias remanescentes. Tenha em atenção que esta operação pode demorar alguns minutos.

Se não vir o número de instâncias esperado após alguns minutos, poderá ser porque o refill parcial foi suficiente para dar as métricas de dentro dos limites. Em alternativa, dimensionamento automático pode ter reduzido porque atingiu o limite de métrica mais baixo.

Se nenhuma dessas condições aplicam-se e o problema persistir, submeta um pedido de suporte.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Como posso ativá compactação HTTP para o meu conteúdo?

Para ativar a compressão para tipos de conteúdos estáticos e dinâmicos, adicione o seguinte código ao ficheiro Web. config de nível de aplicativo:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Também pode especificar os tipos de MIME estáticos e dinâmicos específicos que pretende comprimir. Para obter mais informações, consulte a resposta a uma pergunta do fórum no [httpCompression definições num Web site do Azure simple](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como posso migrar de um ambiente no local para o serviço de aplicações?

A migrar os sites dos servidores de web do Windows e Linux para o serviço de aplicações, pode utilizar Azure App Service Migration Assistant. A ferramenta de migração cria aplicações web e bases de dados no Azure conforme necessário e, em seguida, publica o conteúdo. Para obter mais informações, consulte [Assistente de migração de serviço do Azure aplicações](https://www.migratetoazure.net/).
