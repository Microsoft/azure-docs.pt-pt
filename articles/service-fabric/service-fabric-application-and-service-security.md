---
title: Saiba mais sobre a segurança da aplicação Azure Service Fabric
description: Uma visão geral de como executar de forma segura aplicações de microserviços em Service Fabric. Aprenda a executar serviços e script de arranque sob diferentes contas de segurança, autenticar e autorizar utilizadores, gerir segredos de aplicações, garantir comunicações de serviços, usar um gateway API e proteger os dados da aplicação em repouso.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256582"
---
# <a name="service-fabric-application-and-service-security"></a>Aplicação de tecido de serviço e segurança de serviço
Uma arquitetura de microserviços pode trazer [muitos benefícios.](service-fabric-overview-microservices.md) Gerir a segurança dos microserviços, no entanto, é um desafio e diferente da gestão da segurança das aplicações monolíticas tradicionais. 

Com um monólito, a aplicação é normalmente executada em um ou mais servidores dentro de uma rede e é mais fácil identificar as portas expostas e APIs e endereço IP. Há frequentemente um perímetro ou limite e uma base de dados para proteger. Se este sistema estiver comprometido devido a uma falha de segurança ou ataque, é provável que tudo dentro do sistema esteja disponível para o intruso. Com microserviços, o sistema é mais complexo.  Os serviços são descentralizados e distribuídos por muitos hospedeiros e migram de hospedeiro para hospedeiro.  Com a segurança adequada, limita-se os privilégios que um intruso pode obter e a quantidade de dados disponíveis num único ataque, violando um serviço.  A comunicação não é interna, mas acontece ao longo de uma rede, e há muitos portos expostos e interações entre serviços. Saber quais são estas interações de serviço e quando acontecem é crucial para a segurança da sua aplicação.

Este artigo não é um guia para a segurança dos microserviços, existem muitos desses recursos disponíveis online, mas descreve como diferentes aspetos de segurança podem ser realizados no Tecido de Serviço.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
É frequentemente necessário que os recursos e APIs expostos por um serviço se limitem a certos utilizadores ou clientes de confiança. A autenticação é o processo de apurar fiavelmente a identidade de um utilizador.  A autorização é o processo que disponibiliza APIs ou serviços a alguns utilizadores autenticados, mas não a outros.

### <a name="authentication"></a>Autenticação
O primeiro passo para tomar decisões de confiança ao nível da API é a autenticação. A autenticação é o processo de apurar fiavelmente a identidade de um utilizador.  Em cenários de microserviços, a autenticação é normalmente manuseada centralmente. Se estiver a utilizar um Gateway API, pode descarregar a [autenticação](/azure/architecture/patterns/gateway-offloading) para o gateway. Se utilizar esta abordagem, certifique-se de que os serviços individuais não podem ser alcançados diretamente (sem o Gateway API) a menos que exista segurança adicional para autenticar mensagens, quer venham ou não do gateway.

Se os serviços puderem ser acedidos diretamente, um serviço de autenticação como o Azure Ative Directory ou um microserviço de autenticação dedicado agindo como um serviço de símbolo de segurança (STS) pode ser usado para autenticar os utilizadores. As decisões de confiança são partilhadas entre serviços com fichas de segurança ou cookies. 

Para ASP.NET Core, o mecanismo principal para [autenticar os utilizadores](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) é o sistema de subscrição de identidade ASP.NET. ASP.NET Core Identity armazena informações do utilizador (incluindo informações de acesso, funções e reclamações) numa loja de dados configurada pelo desenvolvedor. ASP.NET Identidade Central suporta a autenticação de dois fatores.  Os fornecedores de autenticação externa também são suportados, para que os utilizadores possam iniciar sação utilizando os processos de autenticação existentes de fornecedores como Microsoft, Google, Facebook ou Twitter.

### <a name="authorization"></a>Autorização
Após a autenticação, os serviços precisam de autorizar o acesso do utilizador ou determinar o que um utilizador é capaz de fazer. Este processo permite que um serviço disponibilize APIs a alguns utilizadores autenticados, mas não a todos. A autorização é orogonal e independente da autenticação, que é o processo de averiguação de quem é o utilizador. A autenticação pode criar uma ou mais identidades para o utilizador atual.

[ASP.NET A autorização core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) pode ser feita com base nas funções dos utilizadores ou com base na política personalizada, que pode incluir a inspeção de reclamações ou outras heurísticas.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restringir e garantir o acesso através de um gateway API
Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. Uma [porta de entrada da API](/azure/architecture/microservices/gateway) situa-se entre clientes e serviços e é o ponto de entrada para todos os serviços que a sua aplicação está a prestar. Funciona como um representante inverso, encaminhando pedidos de clientes para serviços. Pode também executar várias tarefas transversais, tais como autenticação e autorização, rescisão de TLS e limitação de tarifas. Se não implementar uma porta de entrada, os clientes devem enviar pedidos diretamente para os serviços front-end.

Em Service Fabric, um gateway pode ser qualquer serviço apátrida, como uma [aplicação core ASP.NET,](service-fabric-reliable-services-communication-aspnetcore.md)ou outro serviço projetado para entrada de tráfego, como [Traefik,](https://docs.traefik.io/) [Event Hubs,](../event-hubs/index.yml) [IoT Hub,](../iot-hub/index.yml)ou [Azure API Management.](../api-management/index.yml)

A API Management integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um rico conjunto de regras de encaminhamento para os seus serviços de Tecido de Serviço de back-end.  Pode garantir o acesso a serviços de backend, prevenir ataques DOS utilizando estrangulamentos ou verificar chaves API, fichas JWT, certificados e outras credenciais. Para saber mais, leia [o Service Fabric com a visão geral da Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Gerir segredos da aplicação
Os segredos podem ser qualquer informação sensível, como cadeias de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples. Este artigo usa o Azure Key Vault para gerir chaves e segredos. No entanto, *usar* segredos numa aplicação é agnóstico na plataforma para permitir que as aplicações sejam implementadas num cluster hospedado em qualquer lugar.

A forma recomendada de gerir as definições de configuração de serviço é através de [pacotes de configuração de serviço][config-package]. Os pacotes de configuração são versados e updatable através de upgrades de rolamento geridos com validação de saúde e reversão automática. Isto é preferido para a configuração global, uma vez que reduz as chances de uma interrupção global do serviço. Segredos encriptados não são exceção. O Service Fabric tem funcionalidades incorporadas para encriptar e desencriptar valores num pacote de configuração Settings.xml ficheiro utilizando encriptação de certificado.

O diagrama a seguir ilustra o fluxo básico para a gestão secreta numa aplicação de Tecido de Serviço:

![Visão geral de gestão secreta][overview]

Há quatro passos principais neste fluxo:

1. Obtenha um certificado de cifra de dados.
2. Instale o certificado no seu cluster.
3. Criptografe os valores secretos ao implementar uma aplicação com o certificado e injete-os no ficheiro de configuração Settings.xml de um serviço.
4. Leia os valores encriptados fora do Settings.xml desencriptando com o mesmo certificado de cifra. 

[O Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma forma de obter certificados instalados em clusters de tecido de serviço em Azure. Se não estiver a ser implantado para o Azure, não precisa de utilizar o Key Vault para gerir segredos em aplicações de Tecido de Serviço.

Por exemplo, consulte [Gerir os segredos da aplicação.](service-fabric-application-secret-management.md)

## <a name="secure-the-hosting-environment"></a>Proteja o ambiente de hospedagem
Ao utilizar o Azure Service Fabric, pode proteger aplicações que estão a ser executadas no cluster sob diferentes contas de utilizador. O Service Fabric também ajuda a garantir os recursos que são utilizados pelas aplicações no momento da implantação nas contas do utilizador -- por exemplo, ficheiros, diretórios e certificados. Isto torna as aplicações de execução, mesmo num ambiente partilhado, mais seguras umas das outras.

O manifesto de aplicação declara que os princípios de segurança (utilizadores e grupos) necessários executam o(s) serviço(s) e recursos seguros.  Estes princípios de segurança são referenciados em políticas, por exemplo, nas políticas de execução, de ligação ao ponto final, da partilha de pacotes ou das políticas de acesso à segurança.  As políticas são então aplicadas aos recursos de serviço na secção **ServiceManifestImport** do manifesto de aplicação.

Ao declarar os principais, também pode definir e criar grupos de utilizadores para que um ou mais utilizadores possam ser adicionados a cada grupo para serem geridos em conjunto. Isto é útil quando há vários utilizadores para diferentes pontos de entrada de serviço e eles precisam ter certos privilégios comuns que estão disponíveis a nível de grupo.

Por predefinição, as aplicações do Service Fabric são executadas sob a conta que o processo de Fabric.exe é executado. O Service Fabric também fornece a capacidade de executar aplicações numa conta de utilizador local ou conta do sistema local, que é especificada dentro do manifesto de aplicação. Para obter mais informações, consulte [executar um serviço como uma conta de utilizador local ou conta do sistema local.](service-fabric-application-runas-security.md)  Também pode [executar um script de arranque de serviço como utilizador local ou conta do sistema.](service-fabric-run-script-at-service-startup.md)

Quando estiver a executar o Service Fabric num cluster autónomo do Windows, pode executar um serviço em [contas de domínio do Ative Directory](service-fabric-run-service-as-ad-user-or-group.md) ou contas de serviço [geridas pelo grupo.](service-fabric-run-service-as-gmsa.md)

## <a name="secure-containers"></a>Proteger contentores
O Service Fabric fornece um mecanismo de serviços dentro de um contentor para aceder a um certificado instalado nos nós num aglomerado Windows ou Linux (versão 5.7 ou superior). Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou comunicação segura com outros serviços. Para obter mais informações, consulte [importar um certificado num recipiente.](service-fabric-securing-containers.md)

Além disso, o Service Fabric também suporta gMSA (contas de serviço geridas pelo grupo) para contentores Windows. Para obter mais informações, consulte [Configurar gMSA para recipientes Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Comunicação de serviço segura
Em Service Fabric, um serviço funciona em algum lugar em um cluster de Tecido de Serviço, normalmente distribuído por vários VMs. A Service Fabric oferece várias opções para garantir as suas comunicações de serviço.

Pode ativar os pontos finais HTTPS nos seus serviços web [ASP.NET Core ou Java.](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)

Pode estabelecer uma ligação segura entre o proxy inverso e os serviços, permitindo assim o fim do canal de segurança. A ligação a serviços seguros só é suportada quando o proxy invertido estiver configurado para ouvir em HTTPS. Para obter informações sobre a configuração do proxy invertido, leia [o proxy reverso no Tecido de Serviço Azure](service-fabric-reverseproxy.md).  [A ligação a um serviço seguro](service-fabric-reverseproxy-configure-secure-communication.md) descreve como estabelecer uma ligação segura entre o proxy inverso e os serviços.

O quadro de aplicações Reliable Services fornece algumas pilhas de comunicação pré-construídas e ferramentas que você pode usar para melhorar a segurança. Aprenda a melhorar a segurança quando estiver a utilizar o remoting de serviço (em [C#](service-fabric-reliable-services-secure-communication.md) ou [Java)](service-fabric-reliable-services-secure-communication-java.md)ou utilizando [o WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Criptografe os dados da aplicação em repouso
Cada [tipo de nó](service-fabric-cluster-nodetypes.md) num cluster de tecido de serviço em execução em Azure é apoiado por um conjunto de balança de máquina [virtual](../virtual-machine-scale-sets/overview.md). Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric.  Se os seus serviços guardam dados para um disco de dados anexado, pode [encriptar esses discos de dados](../virtual-machine-scale-sets/disk-encryption-powershell.md) para proteger os dados da sua aplicação.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Executar um script de configuração na startup de serviço](service-fabric-run-script-at-service-startup.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Saiba mais sobre a segurança do cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
