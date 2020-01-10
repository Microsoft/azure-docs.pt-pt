---
title: Saiba mais sobre a segurança do aplicativo Service Fabric do Azure
description: Uma visão geral de como executar aplicativos de microserviço com segurança em Service Fabric. Saiba como executar o script de serviços e de inicialização em diferentes contas de segurança, autenticar e autorizar usuários, gerenciar segredos de aplicativos, proteger comunicações de serviço, usar um gateway de API e proteger dados de aplicativos em repouso.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 6c40bf66d1068310790d1440174eeb5b2a571154
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452259"
---
# <a name="service-fabric-application-and-service-security"></a>Segurança de aplicativo e serviço Service Fabric
Uma arquitetura de microserviços pode trazer [muitos benefícios](service-fabric-overview-microservices.md). No entanto, gerenciar a segurança dos microserviços é um desafio e diferente de gerenciar a segurança dos aplicativos monolíticos tradicionais. 

Com um monolítico, o aplicativo normalmente é executado em um ou mais servidores em uma rede e é mais fácil identificar as portas e as APIs e o endereço IP expostos. Geralmente, há um perímetro ou limite e um banco de dados para proteger. Se esse sistema estiver comprometido devido a um ataque ou violação de segurança, é provável que tudo dentro do sistema estará disponível para o invasor. Com os microserviços, o sistema é mais complexo.  Os serviços são descentralizados e distribuídos em vários hosts e migram do host para o host.  Com a segurança adequada, você limita os privilégios que um invasor pode obter e a quantidade de dados disponíveis em um único ataque, violando um serviço.  A comunicação não é interna, mas acontece em uma rede, e há muitas portas expostas e interações entre serviços. Saber o que são essas interações de serviço e quando elas acontecem são cruciais para a segurança do seu aplicativo.

Este artigo não é um guia para a segurança de microserviços, há muitos desses recursos disponíveis online, mas descreve como diferentes aspectos de segurança podem ser realizados em Service Fabric.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Geralmente, é necessário que os recursos e as APIs expostas por um serviço sejam limitados a determinados usuários ou clientes confiáveis. A autenticação é o processo de garantir a identidade de um usuário de forma confiável.  A autorização é o processo que disponibiliza APIs ou serviços para alguns usuários autenticados, mas não para outros.

### <a name="authentication"></a>Autenticação
A primeira etapa para fazer decisões de confiança no nível da API é a autenticação. A autenticação é o processo de garantir a identidade de um usuário de forma confiável.  Em cenários de microserviço, a autenticação é normalmente tratada centralmente. Se você estiver usando um gateway de API, poderá [descarregar a autenticação](/azure/architecture/patterns/gateway-offloading) para o gateway. Se você usar essa abordagem, certifique-se de que os serviços individuais não podem ser acessados diretamente (sem o gateway de API), a menos que a segurança adicional esteja em vigor para autenticar mensagens se elas forem provenientes do gateway ou não.

Se os serviços puderem ser acessados diretamente, um serviço de autenticação como Azure Active Directory ou um microserviço de autenticação dedicado agindo como um serviço de token de segurança (STS) pode ser usado para autenticar usuários. As decisões de confiança são compartilhadas entre serviços com tokens de segurança ou cookies. 

Por ASP.NET Core, o mecanismo primário para [autenticar usuários](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) é o sistema de associação de identidade ASP.NET Core. ASP.NET Core identidade armazena informações do usuário (incluindo informações de entrada, funções e declarações) em um repositório de dados configurado pelo desenvolvedor. ASP.NET Core identidade dá suporte à autenticação de dois fatores.  Os provedores de autenticação externa também têm suporte, para que os usuários possam entrar usando os processos de autenticação existentes de provedores como Microsoft, Google, Facebook ou Twitter.

### <a name="authorization"></a>Autorização
Após a autenticação, os serviços precisam autorizar o acesso do usuário ou determinar o que um usuário pode fazer. Esse processo permite que um serviço torne as APIs disponíveis para alguns usuários autenticados, mas não para todos. A autorização é ortogonal e independente da autenticação, que é o processo de garantir quem é um usuário. A autenticação pode criar uma ou mais identidades para o usuário atual.

[ASP.NET Core autorização](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) pode ser feita com base nas funções dos usuários ou com base na política personalizada, que pode incluir inspecionar declarações ou outras heurísticas.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restringir e proteger o acesso usando um gateway de API
Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. Um [Gateway de API](/azure/architecture/microservices/gateway) fica entre clientes e serviços e é o ponto de entrada para todos os serviços que seu aplicativo está fornecendo. Ele atua como um proxy reverso, roteando solicitações de clientes para serviços. Ele também pode executar várias tarefas abrangentes, como autenticação e autorização, terminação de SSL e limitação de taxa. Se você não implantar um gateway, os clientes deverão enviar solicitações diretamente para os serviços de front-end.

No Service Fabric, um gateway pode ser qualquer serviço sem estado, como um [aplicativo ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para entrada de tráfego, como [Traefik](https://docs.traefik.io/), [hubs de eventos](https://docs.microsoft.com/azure/event-hubs/), [Hub IOT](https://docs.microsoft.com/azure/iot-hub/)ou [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management).

O gerenciamento de API se integra diretamente com o Service Fabric, permitindo que você publique APIs com um conjunto avançado de regras de roteamento para seus serviços de back-end Service Fabric.  Você pode proteger o acesso aos serviços de back-end, evitar ataques de DOS usando a limitação ou verificar chaves de API, tokens JWT, certificados e outras credenciais. Para saber mais, leia [Service Fabric com a visão geral do gerenciamento de API do Azure](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Gerir segredos da aplicação
Os segredos podem ser qualquer informação confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser manipulados em texto sem formatação. Este artigo usa Azure Key Vault para gerenciar chaves e segredos. No entanto, o *uso* de segredos em um aplicativo é independente da plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar.

A maneira recomendada para gerenciar definições de configuração de serviço é por meio de [pacotes de configuração de serviço][config-package]. Os pacotes de configuração têm controle de versão e são atualizáveis por meio de atualizações sem interrupção gerenciadas com validação de integridade e reversão automática. Isso é preferível à configuração global, pois reduz as chances de uma interrupção de serviço global. Segredos criptografados não são exceção. Service Fabric tem recursos internos para criptografar e descriptografar valores em um arquivo Settings. XML do pacote de configuração usando a criptografia de certificado.

O diagrama a seguir ilustra o fluxo básico para o gerenciamento de segredo em um aplicativo Service Fabric:

![Visão geral do gerenciamento de segredos][overview]

Há quatro etapas principais neste fluxo:

1. Obter um certificado de codificação de dados.
2. Instale o certificado em seu cluster.
3. Criptografe valores secretos ao implantar um aplicativo com o certificado e insira-os no arquivo de configuração Settings. XML de um serviço.
4. Ler valores criptografados de Settings. xml descriptografando com o mesmo certificado de codificação. 

[Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma maneira de obter certificados instalados em clusters Service Fabric no Azure. Se você não estiver implantando no Azure, não será necessário usar Key Vault para gerenciar segredos em aplicativos Service Fabric.

Para obter um exemplo, consulte [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Proteger o ambiente de hospedagem
Usando o Azure Service Fabric, você pode proteger aplicativos que estão em execução no cluster em contas de usuário diferentes. Service Fabric também ajuda a proteger os recursos que são usados por aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.

O manifesto do aplicativo declara as entidades de segurança (usuários e grupos) necessárias para executar os serviços e proteger os recursos.  Essas entidades de segurança são referenciadas em políticas, por exemplo, executar como, associação de ponto de extremidade, compartilhamento de pacote ou políticas de acesso de segurança.  As políticas são aplicadas aos recursos de serviço na seção **ServiceManifestImport** do manifesto do aplicativo.

Ao declarar entidades de segurança, você também pode definir e criar grupos de usuários para que um ou mais usuários possam ser adicionados a cada grupo para serem gerenciados juntos. Isso é útil quando há vários usuários para diferentes pontos de entrada de serviço e eles precisam ter determinados privilégios comuns que estão disponíveis no nível de grupo.

Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o processo Fabric. exe é executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário local ou em uma conta de sistema local, que é especificada no manifesto do aplicativo. Para obter mais informações, consulte [executar um serviço como uma conta de usuário local ou uma conta de sistema local](service-fabric-application-runas-security.md).  Você também pode [executar um script de inicialização de serviço como uma conta de usuário ou sistema local](service-fabric-run-script-at-service-startup.md).

Quando você estiver executando Service Fabric em um cluster autônomo do Windows, poderá executar um serviço em [Active Directory contas de domínio](service-fabric-run-service-as-ad-user-or-group.md) ou [contas de serviço gerenciado de grupo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Proteger contentores
Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado instalado nos nós em um cluster do Windows ou do Linux (versão 5,7 ou superior). Esse certificado PFX pode ser usado para autenticar o aplicativo ou serviço ou proteger a comunicação com outros serviços. Para obter mais informações, consulte [importar um certificado para um contêiner](service-fabric-securing-containers.md).

Além disso, Service Fabric também dá suporte a gMSA (contas de serviço gerenciado de grupo) para contêineres do Windows. Para obter mais informações, consulte [Configurar gMSA para contêineres do Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Proteger a comunicação do serviço
No Service Fabric, um serviço é executado em algum lugar em um Cluster Service Fabric, normalmente distribuído entre várias VMs. O Service Fabric fornece várias opções para proteger suas comunicações de serviço.

Você pode habilitar pontos de extremidade HTTPS em seus [ASP.NET Core ou](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) serviços Web Java.

Você pode estabelecer uma conexão segura entre o proxy reverso e os serviços, permitindo assim um canal seguro de ponta a ponta. Só há suporte para a conexão com serviços seguros quando o proxy reverso está configurado para escutar em HTTPS. Para obter informações sobre como configurar o proxy reverso, leia [proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md).  [Conectar-se a um serviço seguro](service-fabric-reverseproxy-configure-secure-communication.md) descreve como estabelecer uma conexão segura entre o proxy reverso e os serviços.

A estrutura de aplicativo Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Saiba como melhorar a segurança quando você estiver usando a comunicação remota do [C#](service-fabric-reliable-services-secure-communication.md) serviço (no ou [Java](service-fabric-reliable-services-secure-communication-java.md)) ou usando o [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Criptografar dados de aplicativo em repouso
Cada [tipo de nó](service-fabric-cluster-nodetypes.md) em um Cluster Service Fabric em execução no Azure é apoiado por um [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric.  Se seus serviços salvarem dados em um disco de dados anexado, você poderá [criptografar esses discos de dados](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) para proteger os dados do aplicativo.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Executar um script de instalação na inicialização do serviço](service-fabric-run-script-at-service-startup.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Saiba mais sobre a segurança do cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png