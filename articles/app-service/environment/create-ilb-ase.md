---
title: Criar Balanceador de carga interno com o ambiente de serviço de aplicações - Azure
description: Detalhes sobre como criar e utilizar um Ambiente de Serviço de Aplicações do Azure isolado da Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 99c6e7dc589a94880976a9c7abcde12377e4e5d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101538"
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Criar e utilizar um balanceador de carga interno com um Ambiente de Serviço de Aplicações #

 O Ambiente de Serviço de Aplicações do Azure é uma implementação do Serviço de Aplicações do Azure numa sub-rede de uma rede virtual do Azure (VNet). Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE): 

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com um VIP num endereço IP interno, muitas vezes chamado ASE de ILB porque o ponto final interno é um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE de ILB. Para obter uma descrição geral sobre o ASE, veja [Introdução aos Ambientes de Serviço de Aplicações][Intro]. Para saber como criar um ASE externo, veja [Criar um ASE Externo][MakeExternalASE].

## <a name="overview"></a>Descrição geral ##

Pode implementar um ASE com um ponto final acessível pela Internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet, o ASE tem de ser implementado com um ILB. Quando implementa o ASE com um ILB, tem de fornecer:

-   O seu próprio domínio que utiliza quando cria as suas aplicações.
-   O certificado utilizado para HTTPS.
-   Gestão de DNS para o seu domínio.

Em contrapartida, pode fazer coisas como:

-   Alojar aplicações da intranet em segurança na cloud, às quais pode aceder através de uma rede de VPNs ou da VPN do Azure ExpressRoute.
-   Alojar aplicações na cloud que não estão listadas em servidores DNS públicos.
-   Criar aplicações back-end isoladas da Internet, nas quais as suas aplicações front-end podem ser integradas em segurança.

### <a name="disabled-functionality"></a>Funcionalidades desativadas ###

Existem algumas coisas que não é possível fazer quando utiliza um ASE de ILB:

-   Utilizar SSL baseado em IP.
-   Atribuir endereços IP a aplicações específicas.
-   Comprar e utilizar um certificado com uma aplicação através do portal do Azure. Pode obter certificados a partir diretamente de uma autoridade de certificação e utilizá-los com as suas aplicações. Não pode obtê-los através do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE de ILB ##

Para criar um ASE de ILB:

1. No portal do Azure, selecione **criar um recurso** > **Web** > **App Service Environment**.

2. Selecione a sua subscrição.

3. Selecione ou crie um grupo de recursos.

4. Selecione ou crie uma VNet.

5. Se selecionar uma VNet existente, terá de criar uma sub-rede para conter o ASE. Certifique-se de que define um tamanho de sub-rede suficientemente grande para se adaptar a qualquer crescimento futuro do seu ASE. Recomendamos um tamanho de `/24`, que tem 256 endereços e pode processar um ASE de tamanho máximo e qualquer necessidade de dimensionamento. 

6. Selecione **rede Virtual/localização** > **configuração de rede Virtual**. Defina o **Tipo de VIP** como **Interno**.

7. Introduza um nome de domínio. Este domínio é o domínio utilizada para as aplicações criadas neste ASE. Existem algumas restrições. Não pode ser:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   Pode [mapear um nome DNS existente para a aplicação][customdomain]. O nome de domínio personalizado utilizado para aplicações e o nome de domínio utilizado pelo seu ASE não se podem sobrepor. Para um ASE de ILB com o nome de domínio _contoso.com_, não pode utilizar nomes de domínio personalizados para as suas aplicações, como:

   * www\.contoso.com

   * abcd.def.contoso.com

   * abcd.contoso.com

   Se souber os nomes de domínio personalizados para as suas aplicações, escolha um domínio para o ASE de ILB que não vá entrar em conflito com esses nomes de domínio personalizados. Neste exemplo, pode utilizar algo semelhante a *contoso-internal.com* para o domínio do ASE porque não vai entrar em conflito com os nomes de domínio personalizados que terminam em *.contoso.com*.

8. Selecione **OK** e, em seguida, selecione **Criar**.

    ![Criação do ASE][1]

No painel **Rede Virtual**, existe uma opção **Configuração de Rede Virtual**. Pode utilizá-la para selecionar um VIP Externo ou um VIP Interno. A predefinição é **Externo**. Se selecionar **Externo**, o seu ASE utiliza um VIP acessível pela Internet. Se selecionar **Interno**, o seu ASE está configurado com um ILB num endereço IP na sua VNet.

Depois de selecionar **Interno**, a capacidade de adicionar mais endereços IP ao seu ASE é removida. Em vez disso, tem de fornecer o domínio do ASE. Num ASE com um VIP Externo, o nome do ASE é utilizado no domínio para as aplicações criadas nesse ASE.

Se definir **Tipo de VIP** como **Interno**, o nome do ASE não é utilizado no domínio para o ASE. Especifique o domínio explicitamente. Se o seu domínio for *contoso.corp.net* e criar uma aplicação nesse ASE com o nome *timereporting*, o URL para essa aplicação é timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Criar uma aplicação num ASE de ILB ##

Uma aplicação num ASE de ILB é criada da mesma forma que cria uma aplicação num ASE normalmente.

1. No portal do Azure, selecione **criar um recurso** > **Web + móvel** > **aplicação Web**.

1. Introduza o nome da aplicação.

1. Selecione uma subscrição.

1. Selecione ou crie um grupo de recursos.

1. Selecione o seu SO. 

    * Se quiser criar uma aplicação Linux com um contentor do Docker personalizado, basta criar o seu próprio contentor ao seguir [estas][linuxapp] instruções. 

1. Selecione ou crie um plano do Serviço de Aplicações. Se quiser criar um novo plano do Serviço de Aplicações, selecione o seu ASE como localização. Selecione o conjunto de processos de trabalho onde pretende que o seu plano do Serviço de Aplicações seja criado. Quando criar o plano do Serviço de Aplicações, selecione o seu ASE como a localização e o conjunto de processos de trabalho. Quando especificar o nome da aplicação, o domínio no nome da aplicação é substituído pelo domínio para o seu ASE.

1. Selecione **Criar**. Se pretender que a aplicação apareça no dashboard, selecione o **afixar ao dashboard** caixa de verificação.

    ![Criação do plano do Serviço de Aplicações][2]

    Em **Nome da aplicação**, o nome de domínio é atualizado para refletir o domínio do seu ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validação da criação do ASE pós-ILB ##

Um ASE de ILB é ligeiramente diferente do ASE não ILB. Como já foi mencionado, tem de gerir o seu próprio DNS. Também tem de fornecer o seu próprio certificado para ligações HTTPS.

Depois de criar o ASE, o nome de domínio mostra o domínio que especificou. Um novo item aparece no **definição** menu chamado **certificado ILB**. O ASE é criado com um certificado que não especifica o domínio do ASE de ILB. Se utilizar o ASE com esse certificado, o seu browser indicará que é inválido. Este certificado torna mais fácil testar o HTTPS, mas é preciso carregar o seu próprio certificado que está associado ao seu domínio do ASE de ILB. Este passo é necessário, independentemente de o seu certificado ser autoassinado ou adquirido junto de uma autoridade de certificação.

![Nome de domínio do ASE de ILB][3]

O ASE de ILB precisa de um certificado SSL válido. Utilize autoridades de certificação internas, compre um certificado junto de um emissor externo ou utilize um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado têm de ser configurados corretamente:

* **Assunto**: Este atributo tem de ser definido como *.your-raiz-domain-here.
* **Nome alternativo do requerente**: Este atributo tem de incluir **.your-raiz-domain-here* e **Here-raiz-domínio-aqui*. As ligações SSL ao site SCM/Kudu associadas a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here*.

Converta/guarde o certificado SSL como um ficheiro .pfx. O ficheiro .pfx tem de incluir todos certificados intermédios e de raiz. Proteja-o com uma palavra-passe.

Se pretender criar um certificado autoassinado, pode utilizar os comandos do PowerShell aqui. Certifique-se de que utiliza o seu nome de domínio do ASE de ILB em vez de *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

O certificado gerado por estes comandos do PowerShell é assinalado pelos browsers porque o certificado não foi criado por uma autoridade de certificação que se encontra na cadeia de certificação dos browsers. Para obter um certificado em que o seu browser confie, obtenha-o junto de uma autoridade de certificação comercial na cadeia de certificação do seu browser. 

![Definir o certificado ILB][4]

Para carregar os seus próprios certificados e testar o acesso:

1. Após a criação do ASE, aceda à IU do ASE. Selecione **ASE** > **Definições** > **Certificado ILB**.

1. Para definir o certificado ILB, selecione o ficheiro .pfx do certificado e introduza a palavra-passe. Este passo demora algum tempo a processar. É apresentada uma mensagem a indicar que uma operação de carregamento está em curso.

1. Obtenha o endereço do ILB para o seu ASE. Selecione **ASE** > **Propriedades** > **Endereço IP Virtual**.

2. Crie uma aplicação no seu ASE após a criação do ASE.

3. Crie uma VM se não tiver uma nessa VNet.

    > [!NOTE] 
    > Não tente criar esta VM na mesma sub-rede que o ASE porque irá falhar ou causar problemas.
    >

4. Defina o DNS para o seu domínio ASE. Pode utilizar um caráter universal com o domínio no seu DNS. Para fazer alguns testes simples, edite o ficheiro de anfitriões na sua VM para definir o nome da aplicação para o endereço IP de VIP:

    a. Se o seu ASE tiver o nome de domínio _. ilbase.com_ e criar a aplicação com o nome _mytestapp_, é abordado na _mytestapp.ilbase.com_. Em seguida, defina _mytestapp.ilbase.com_ para resolver para o endereço do ILB. (No Windows, o ficheiro hosts está em _C:\Windows\System32\drivers\etc\_.)

    b. Para testar a publicação da implementação na Web ou o acesso à consola avançada, crie um registo para _mytestapp.scm.ilbase.com_.

5. Utilize um browser nessa VM e aceda a https://mytestapp.ilbase.com. (Ou vá para tudo o que é o nome da sua aplicação com o seu domínio.)

6. Utilize um browser nessa VM e aceda a https://mytestapp.ilbase.com. Se utilizar um certificado autoassinado, aceite a falta de segurança.

    O endereço IP para o ILB está listado em **Endereços IP**. Esta lista também inclui os endereços IP utilizados pelo VIP externo e para o tráfego de gestão de entrada.

    ![Endereço IP do ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Funções e o ASE de ILB ##

Tanto as Funções como os WebJobs são suportados num ASE de ILB, mas para o portal funcionar com os mesmos, tem de ter acesso de rede ao site SCM.  Isto significa que o browser tem de estar num anfitrião que esteja na rede virtual ou ligado à mesma.  

Quando utiliza as Funções do Azure num ASE de ILB, poderá receber uma mensagem de erro com a indicação "Não é possível obter as funções neste momento. Tente novamente mais tarde." Este erro ocorre porque a IU de Funções tira partido do site do SCM através de HTTPS e o certificado de raiz não está na cadeia de certificação do browser. Os WebJobs têm um problema semelhante. Para evitar este problema, pode executar uma das seguintes ações:

- Adicione o certificado ao arquivo de certificados fidedignos. Esta ação desbloqueia o Microsoft Edge e o Internet Explorer.
- Utilize o Chrome e aceda primeiro ao site do SCM, aceite o certificado não fidedigno e, em seguida, aceda ao portal.
- Utilize um certificado comercial que esteja na cadeia de certificação do seu browser.  Esta é a melhor opção.  

## <a name="dns-configuration"></a>Configuração do DNS ##

Quando utiliza um VIP Externo, o DNS é gerido pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. Para um determinado domínio, tal como _contoso.net_, terá de criar registos A de DNS no seu DNS que apontem para o seu endereço de ILB para:

- *.contoso.net
- *.scm.contoso.net

Se o domínio do ASE de ILB for utilizado para várias coisas fora deste ASE, poderá ter de gerir o DNS por nome de aplicação. Este método é difícil porque tem de adicionar cada nome de aplicação nova ao DNS quando a criar. Por este motivo, recomendamos que utilize um domínio dedicado.

## <a name="publish-with-an-ilb-ase"></a>Publicar com um ASE de ILB ##

Para cada aplicação criada, existem dois pontos finais. Num ASE de ILB, tem *&lt;app name>.&lt;ILB ASE Domain>* e *&lt;app name>.scm.&lt;ILB ASE Domain>*. 

O nome do site SCM leva-o para a consola Kudu, denominada **Portal avançado**, no portal do Azure. A consola Kudu permite-lhe ver as variáveis de ambiente, explorar o disco, utilizar uma consola e muito mais. Para obter mais informações, veja [Consola Kudu para o Serviço de Aplicações do Azure][Kudu]. 

No Serviço de Aplicações multi-inquilino e num ASE Externo, existe início de sessão único entre o portal do Azure e a consola Kudu. No entanto, para o ASE de ILB, terá de utilizar as suas credenciais de publicação para iniciar sessão na consola Kudu.

Os sistemas CI baseados na Internet, como o GitHub e o Azure DevOps, continuarão a funcionar com um ASE de ILB se o agente de compilação for acessível pela Internet e estiver na mesma rede que o ASE de ILB. Por isso, no caso do Azure DevOps, se o agente de compilação for criado na mesma VNET que o ASE de ILB (pode ser outra sub-rede), conseguirá obter o código do git do Azure DevOps e implementar no ASE de ILB. Se não quiser criar o seu próprio agente de compilação, terá de utilizar um sistema CI que utilize um modelo de extração, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Este domínio aparece no perfil de publicação da aplicação e no painel do portal da aplicação (**Descrição geral** > **Informações Básicas** e também **Propriedades**). Se tiver um ASE de ILB com o subdomínio *contoso.net* e uma aplicação com o nome *mytest*, utilize *mytest.contoso.net* para FTP e *mytest.scm.contoso.net* para implementação na Web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Acoplar um ASE de ILB a um dispositivo WAF ##

O Serviço de Aplicações do Azure fornece várias medidas de segurança que protegem o sistema. Também ajudam a determinar se uma aplicação foi vítima de acesso ilícito. A melhor proteção para uma aplicação Web é acoplar uma plataforma de alojamento, como o Serviço de Aplicações do Azure, a uma firewall de aplicações Web (WAF). Uma vez que o ASE de ILB tem um ponto final de aplicação isolado da rede, é adequado a esta utilização.

Para obter mais informações sobre como configurar o ASE de ILB com um dispositivo WAF, veja [Configurar uma firewall de aplicações Web com o seu ambiente de Serviço de Aplicações][ASEWAF]. Este artigo mostra como utilizar uma aplicação virtual Barracuda com o seu ASE. Outra opção é utilizar o Gateway de Aplicação do Azure. O Gateway de Aplicação utiliza as regras de base da OWASP para proteger todas as aplicações colocadas atrás. Para obter mais informações sobre o Gateway de Aplicação, veja [Introdução à firewall de aplicações Web do Azure][AppGW].

## <a name="get-started"></a>Introdução ##

* Para começar a utilizar ASEs, veja [Introdução aos ambientes de Serviço de Aplicações][Intro].
 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
