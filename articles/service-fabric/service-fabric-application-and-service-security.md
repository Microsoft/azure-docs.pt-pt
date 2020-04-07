---
title: Conheça a segurança da aplicação azure service Fabric
description: Uma visão geral de como executar com segurança aplicações de microserviços no Service Fabric. Saiba como executar serviços e iniciar scripts em diferentes contas de segurança, autenticar e autorizar utilizadores, gerir segredos de aplicação, proteger as comunicações de serviço, usar um gateway API e proteger os dados da aplicação em repouso.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: e9b4a1209838bdd5eee401b0defb01839b5cf684
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756229"
---
# <a name="service-fabric-application-and-service-security"></a>Aplicação de serviço Fabric e segurança de serviço
Uma arquitetura de microserviços pode trazer [muitos benefícios.](service-fabric-overview-microservices.md) Gerir a segurança dos microserviços, no entanto, é um desafio e diferente de gerir a segurança de aplicações monolíticas tradicionais. 

Com um monólito, a aplicação está normalmente a funcionar em um ou mais servidores dentro de uma rede e é mais fácil identificar as portas expostas e APIs e endereço IP. Há muitas vezes um perímetro ou limite e uma base de dados para proteger. Se esse sistema estiver comprometido devido a uma falha de segurança ou ataque, é provável que tudo dentro do sistema esteja disponível para o atacante. Com os microserviços, o sistema é mais complexo.  Os serviços são descentralizados e distribuídos por muitos anfitriões e migram de hospedeiro para hospedeiro.  Com segurança adequada, limita-se os privilégios que um intruso pode obter e a quantidade de dados disponíveis num único ataque, violando um serviço.  A comunicação não é interna, mas acontece ao longo de uma rede, e há muitos portos expostos e interações entre serviços. Saber quais são estas interações de serviço e quando acontecem é crucial para a segurança da sua aplicação.

Este artigo não é um guia para a segurança dos microserviços, existem muitos desses recursos disponíveis online, mas descreve como diferentes aspetos de segurança podem ser realizados no Tecido de Serviço.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Muitas vezes é necessário que os recursos e APIs expostos por um serviço se limitem a determinados utilizadores ou clientes de confiança. A autenticação é o processo de apurar de forma fiável a identidade de um utilizador.  A autorização é o processo que disponibiliza APIs ou serviços a alguns utilizadores autenticados, mas não a outros.

### <a name="authentication"></a>Autenticação
O primeiro passo para tomar decisões de confiança ao nível da API é a autenticação. A autenticação é o processo de apurar de forma fiável a identidade de um utilizador.  Em cenários de microserviço, a autenticação é normalmente manuseada centralmente. Se estiver a utilizar um Gateway API, pode [descarregar a autenticação](/azure/architecture/patterns/gateway-offloading) para o portal. Se utilizar esta abordagem, certifique-se de que os serviços individuais não podem ser contactados diretamente (sem o Gateway API) a menos que haja uma segurança adicional para autenticar mensagens, quer venham ou não do portal.

Se os serviços puderem ser acedidos diretamente, um serviço de autenticação como o Azure Ative Directory ou um microserviço de autenticação dedicado, agindo como um serviço de token de segurança (STS) pode ser usado para autenticar os utilizadores. As decisões de confiança são partilhadas entre serviços com fichas de segurança ou cookies. 

Para ASP.NET Core, o principal mecanismo para [autenticar os utilizadores](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) é o sistema de adesão à Identidade Central ASP.NET. ASP.NET Core Identity armazena informações dos utilizadores (incluindo informações de sessão, funções e reclamações) numa loja de dados configurada pelo desenvolvedor. ASP.NET Identidade Central suporta a autenticação de dois fatores.  Os fornecedores de autenticação externa também são suportados, para que os utilizadores possam assinar a utilização de processos de autenticação existentes de fornecedores como microsoft, Google, Facebook ou Twitter.

### <a name="authorization"></a>Autorização
Após a autenticação, os serviços precisam de autorizar o acesso do utilizador ou determinar o que um utilizador é capaz de fazer. Este processo permite que um serviço disponibilize APIs a alguns utilizadores autenticados, mas não a todos. A autorização é ortogonal e independente da autenticação, que é o processo de apurar quem é um utilizador. A autenticação pode criar uma ou mais identidades para o utilizador atual.

[ASP.NET a autorização core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) pode ser feita com base nas funções dos utilizadores ou com base na política personalizada, que pode incluir a inspeção de reclamações ou outras heurísticas.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restringir e garantir o acesso usando um gateway DaPI
Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. Um [portal da API](/azure/architecture/microservices/gateway) situa-se entre clientes e serviços e é o ponto de entrada de todos os serviços que a sua aplicação está a prestar. Atua como um representante inverso, encaminhando pedidos de clientes para serviços. Pode também executar várias tarefas transversais, tais como autenticação e autorização, rescisão de TLS e limitação de taxas. Se não implementar uma porta de entrada, os clientes devem enviar pedidos diretamente para serviços front-end.

No Tecido de Serviço, um gateway pode ser qualquer serviço apátrida, como uma [aplicação ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para ingresso de tráfego, como [Traefik,](https://docs.traefik.io/)Hubs de [Eventos,](https://docs.microsoft.com/azure/event-hubs/) [Hub IoT,](https://docs.microsoft.com/azure/iot-hub/)ou [Azure API Management.](https://docs.microsoft.com/azure/api-management)

A API Management integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um conjunto rico de regras de encaminhamento para os seus serviços de tecido de serviço de back-end.  Pode garantir o acesso aos serviços backend, prevenir ataques DOS utilizando estrangulamentos ou verificar chaves API, fichas JWT, certificados e outras credenciais. Para saber mais, leia o Serviço Fabric com visão geral da [API Management Azure.](service-fabric-api-management-overview.md)

## <a name="manage-application-secrets"></a>Gerir segredos da aplicação
Os segredos podem ser qualquer informação sensível, como cordas de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples. Este artigo usa o Cofre chave Azure para gerir chaves e segredos. No entanto, *o uso* de segredos numa aplicação é cloud platform-agnóstico para permitir que as aplicações sejam implementadas para um cluster hospedado em qualquer lugar.

A forma recomendada de gerir as definições de configuração do serviço é através de pacotes de configuração de [serviço.][config-package] Os pacotes de configuração são versonizados e atualizados através de atualizações de rolling geridas com validação de saúde e reversão automática. Isto é preferido para a configuração global, uma vez que reduz as chances de uma interrupção global do serviço. Segredos encriptados não são exceção. O Service Fabric tem funcionalidades incorporadas para encriptar e desencriptar valores num pacote de configuração Definições.xml ficheiro utilizando encriptação de certificado.

O diagrama que se segue ilustra o fluxo básico para a gestão secreta numa aplicação de Tecido de Serviço:

![visão geral da gestão secreta][overview]

Há quatro passos principais neste fluxo:

1. Obtenha um certificado de obtenção de dados.
2. Instale o certificado no seu cluster.
3. Criptografe valores secretos ao implementar uma aplicação com o certificado e injete-os no ficheiro de configuração Definições.xml de um serviço.
4. Leia os valores encriptados fora de Definições.xml desencriptando com o mesmo certificado de encipherment. 

[O Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como forma de obter certificados instalados em clusters de Tecido de Serviço em Azure. Se não estiver a ser implantado no Azure, não precisa de usar o Key Vault para gerir segredos em aplicações de Tecido de Serviço.

Por exemplo, consulte [Gerir os segredos](service-fabric-application-secret-management.md)da aplicação .

## <a name="secure-the-hosting-environment"></a>Proteja o ambiente de hospedagem
Ao utilizar o Tecido de Serviço Azure, pode proteger aplicações que estão a ser realizadas no cluster sob diferentes contas de utilizador. O Service Fabric também ajuda a proteger os recursos utilizados pelas aplicações no momento da implementação ao abrigo das contas de utilizador -- por exemplo, ficheiros, diretórios e certificados. Isto torna as aplicações de execução, mesmo num ambiente partilhado, mais seguras umas das outras.

O manifesto de aplicação declara que os principais de segurança (utilizadores e grupos) necessários executam os serviços e protegem os recursos.  Estes diretores de segurança são referenciados em políticas, por exemplo, as políticas de controlo, encadernação de pontos finais, partilha de pacotes ou acesso à segurança.  As políticas são então aplicadas aos recursos de serviço na secção **ServiceManifestImport** do manifesto de aplicação.

Ao declarar os principais, também pode definir e criar grupos de utilizadores para que um ou mais utilizadores possam ser adicionados a cada grupo para serem geridos em conjunto. Isto é útil quando existem vários utilizadores para diferentes pontos de entrada de serviço e precisam de ter certos privilégios comuns que estão disponíveis a nível de grupo.

Por predefinição, as aplicações de Tecido de Serviço são executadas sob a conta que o processo Fabric.exe é executado. O Service Fabric também fornece a capacidade de executar aplicações sob uma conta de utilizador local ou conta de sistema local, que é especificada dentro do manifesto de aplicação. Para mais informações, consulte [Executar um serviço como uma conta de utilizador local ou conta do sistema local](service-fabric-application-runas-security.md).  Também pode executar um script de arranque de [serviço como utilizador local ou conta de sistema.](service-fabric-run-script-at-service-startup.md)

Quando estiver a executar o Tecido de Serviço num cluster autónomo do Windows, pode executar um serviço sob contas de [domínio active'd'maneio](service-fabric-run-service-as-ad-user-or-group.md) ou contas de [serviço geridas pelo grupo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Proteger contentores
O Service Fabric fornece um mecanismo de serviços dentro de um contentor para aceder a um certificado instalado nos nós de um cluster Windows ou Linux (versão 5.7 ou superior). Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou uma comunicação segura com outros serviços. Para mais informações, consulte [importar um certificado num contentor](service-fabric-securing-containers.md).

Além disso, o Service Fabric também suporta gMSA (contas de serviço geridas pelo grupo) para recipientes Windows. Para mais informações, consulte [A configuração de gMSA para recipientes Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Comunicação de serviço seguro
No Service Fabric, um serviço funciona algures num cluster de Tecido de Serviço, tipicamente distribuído por vários VMs. O Service Fabric oferece várias opções para garantir as suas comunicações de serviço.

Pode ativar pontos finais HTTPS nos seus serviços web [Core de ASP.NET ou Java.](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)

Pode estabelecer uma ligação segura entre o proxy e os serviços invertidos, permitindo assim o fim do canal seguro final. A ligação a serviços seguros só é suportada quando o proxy inverso estiver configurado para ouvir em HTTPS. Para obter informações sobre a configuração do proxy inverso, leia o [proxy inverso no Tecido de Serviço Azure](service-fabric-reverseproxy.md).  [A ligação a um serviço seguro](service-fabric-reverseproxy-configure-secure-communication.md) descreve como estabelecer uma ligação segura entre o proxy e os serviços invertidos.

O quadro de aplicação de Serviços Fiáveis fornece algumas pilhas e ferramentas de comunicação pré-construídas que pode usar para melhorar a segurança. Aprenda a melhorar a segurança quando estiver a utilizar remoting de serviço (em [C#](service-fabric-reliable-services-secure-communication.md) ou [Java)](service-fabric-reliable-services-secure-communication-java.md)ou usando [wCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Criptografe os dados da aplicação em repouso
Cada [tipo de nó](service-fabric-cluster-nodetypes.md) num cluster de tecido de serviço em funcionamento em Azure é apoiado por um conjunto de escala de máquina [virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric.  Se os seus serviços guardarem dados para um disco de dados anexo, pode [encriptar esses discos](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) de dados para proteger os dados da sua aplicação.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Executar um script de configuração na startup de serviço](service-fabric-run-script-at-service-startup.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Conheça a segurança do cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png