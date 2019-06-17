---
title: Saiba mais sobre segurança de aplicações do Azure Service Fabric | Documentos da Microsoft
description: Uma visão geral de como executar com segurança as aplicações de microsserviços no Service Fabric. Saiba como executar o script de arranque e de serviços com diferentes contas de segurança, autenticar e autorizar utilizadores, gerir segredos da aplicação, proteger as comunicações de serviço, utilize um gateway de API e dados de aplicativo seguro em repouso.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: aljo
ms.openlocfilehash: cb0f750f4049a1ce652c829f43928a95f30e6973
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66302232"
---
# <a name="service-fabric-application-and-service-security"></a>Segurança do serviço e de aplicação do Service Fabric
Pode colocar uma arquitetura de microsserviços [muitos benefícios](service-fabric-overview-microservices.md). No entanto, gerenciar a segurança dos microsserviços, é um desafio e diferente de gerenciamento da segurança de aplicativos monolíticos tradicionais. 

Com um monólito, a aplicação é executada normalmente num ou mais servidores dentro de uma rede e é mais fácil identificar as portas expostas e APIs e o endereço IP. Muitas vezes, é um perímetro ou limite de e para proteger uma base de dados. Se esse sistema for comprometido devido a uma violação de segurança ou de um ataque, é provável que tudo o que o sistema estará disponível para o atacante. Com os microsserviços, o sistema é mais complexo.  Os serviços são descentralizados e distribuídos em vários anfitriões e migrar de anfitrião para anfitrião.  Com a segurança adequada, limitar os privilégios que um invasor pode obter e a quantidade de dados disponíveis num único ataque por que viola um serviço.  Comunicação não é interna, mas acontece através de uma rede, e existem muitas portas expostas e interações entre os serviços. Saber quais são essas interações de serviço e quando eles ocorrem é crucial para sua segurança de aplicações.

Este artigo não é um guia para segurança de microsserviços, há muitos tais recursos disponíveis online, mas descreve como diferentes aspectos de segurança pode ser feito no Service Fabric.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Muitas vezes, é necessário para recursos e APIs expostas por um serviço a ser limitada a determinados utilizadores fidedignos ou clientes. A autenticação é o processo de forma fiável para averiguar a identidade do utilizador.  Autorização é o processo que faz com que as APIs ou serviços disponíveis para alguns autenticado os usuários, mas não para outros.

### <a name="authentication"></a>Autenticação
A primeira etapa para tomar decisões de confiança ao nível da API é a autenticação. A autenticação é o processo de forma fiável para averiguar a identidade do utilizador.  Em cenários de microsserviços, a autenticação é normalmente processada centralmente. Se estiver a utilizar um Gateway de API, pode [autenticação de descarga](/azure/architecture/patterns/gateway-offloading) para o gateway. Se usar essa abordagem, certifique-se de que os serviços individuais não não possível aceder diretamente (sem o Gateway de API), a menos que seja de segurança adicional no local para autenticar as mensagens se eles vêm o gateway ou não.

Se os serviços podem ser acedidos diretamente, um serviço de autenticação como o Azure Active Directory ou um microsserviço de autenticação dedicado a agir como um serviço de token (STS) pode ser utilizado para autenticar os utilizadores de segurança. Decisões de confiança são partilhadas entre serviços com tokens de segurança ou cookies. 

Para o ASP.NET Core, o mecanismo primário para [autenticar os utilizadores](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) é o sistema de associação do ASP.NET Core Identity. Identidade do ASP.NET Core armazena informações de utilizador (incluindo informações de início de sessão, funções e afirmações) num arquivo de dados configurado pelo programador. Identidade do ASP.NET Core suporta a autenticação de dois fatores.  Também são suportados fornecedores de autenticação externos, para que os utilizadores podem iniciar sessão com processos de autenticação existentes de fornecedores como a Microsoft, Google, Facebook ou Twitter.

### <a name="authorization"></a>Autorização
Após a autenticação, tem de serviços autorizar o acesso de utilizador ou a determinar o que um utilizador é capaz de fazer. Esse processo permite que um serviço de APIs disponíveis para alguns usuários autenticados, mas não a todos os. Autorização é ortogonal e independente de autenticação, o que é o processo de para averiguar o que é um utilizador. Autenticação, pode criar um ou mais identidades para o utilizador atual.

[Autorização do ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) pode ser feito com base em funções dos utilizadores ou com base numa política personalizada, que pode incluir a inspecionar afirmações ou outras heurística.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restringir e proteger o acesso com um gateway de API
Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. Uma [gateway de API](/azure/architecture/microservices/gateway) encontra-se entre clientes e serviços e é o ponto de entrada para todos os serviços que está a fornecer seu aplicativo. Ele atua como proxy inverso, encaminhamento de pedidos de clientes para serviços. Ele também pode fazer várias tarefas de transversais, como a autenticação e autorização, terminação de SSL e limitação de velocidade. Se não implementar um gateway, os clientes devem enviar pedidos diretamente para os serviços front-end.

No Service Fabric, um gateway pode ser qualquer serviço sem estado, tais como um [aplicação ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço concebido para entrada de tráfego, tal como [Traefik](https://docs.traefik.io/), [dos Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), ou [gestão de API do Azure](https://docs.microsoft.com/azure/api-management).

Gestão de API integra-se diretamente com o Service Fabric, que lhe permite publicar APIs com um conjunto avançado de regras de encaminhamento para os serviços de recursos de infraestrutura do serviço de back-end.  Pode proteger o acesso aos serviços de back-end, impedir ataques DOS através da utilização de limitação ou verificar chaves de API, tokens JWT, certificados e outras credenciais. Para saber mais, leia [Service Fabric com a descrição geral da gestão de API do Azure](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Gerir segredos da aplicação
Segredos podem ser qualquer informações confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples. Este artigo utiliza o Azure Key Vault para gerir as chaves e segredos. No entanto, *usando* segredos num aplicativo está na cloud independente de plataforma para permitir que os aplicativos a serem implantados para um cluster alojado em qualquer lugar.

A forma recomendada para gerir as definições de configuração do serviço é através de [pacotes de configuração de serviço][config-package]. Pacotes de configuração são uma versão e pode ser atualizado através de atualizações sem interrupção geridas com reversão de validação do Estado de funcionamento e automático. Isso tem preferência sobre a configuração global, pois reduz as hipóteses de uma indisponibilidade do serviço global. Os segredos encriptados não são exceção. Service Fabric tem recursos incorporados para encriptar e desencriptar valores num arquivo Settings do pacote de configuração com a encriptação de certificado.

O diagrama seguinte ilustra o fluxo básico para a gestão de segredos num aplicativo de Service Fabric:

![Descrição geral da gestão secreta][overview]

Neste fluxo, existem quatro passos principais:

1. Obter um certificado de encriptação de dados.
2. Instale o certificado no seu cluster.
3. Encriptar os valores secretos quando implementar uma aplicação com o certificado e inseri-los no arquivo de configuração de Settings de um serviço.
4. Leitura de valores encriptados Settings, descriptografando com o mesmo certificado de encriptação. 

[O Azure Key Vault] [ key-vault-get-started] serve aqui como uma localização de armazenamento seguro para certificados e como uma forma de obter certificados instalados em clusters do Service Fabric no Azure. Se não estiver a implementar para o Azure, não é necessário utilizar o Key Vault para gerir segredos em aplicações do Service Fabric.

Por exemplo, veja [gerir segredos da aplicação](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Proteger o ambiente de alojamento
Ao utilizar o Azure Service Fabric, pode proteger aplicações em execução no cluster sob diferentes contas de usuário. Service Fabric também ajuda a proteger os recursos utilizados por aplicações no momento da implantação sob as contas de utilizador – por exemplo, arquivos, diretórios e certificados. Isso faz com que aplicativos em execução, mesmo num ambiente de hospedagem compartilhado, mais seguro umas das outras.

O manifesto do aplicativo declara as entidades de segurança (utilizadores e grupos) necessárias executam o serviço ou serviços e recursos seguros.  Estas entidades são referenciadas nas políticas, por exemplo o Run, ponto final de ligação, de segurança do pacote de partilha ou políticas de acesso de segurança.  As políticas, em seguida, são aplicadas aos recursos de serviço no **ServiceManifestImport** secção de manifesto do aplicativo.

Ao declarar principais, também pode definir e criar grupos de utilizadores para que possam ser adicionados um ou mais utilizadores para cada grupo de ser geridos em conjunto. Isto é útil quando existirem vários utilizadores para os pontos de entrada de serviço diferentes e que precisam para tem determinados privilégios comuns que estão disponíveis no nível de grupo.

Por predefinição, as aplicações do Service Fabric executam sob a conta que o processo de Fabric.exe é executado em. O Service Fabric também fornece a capacidade de executar aplicativos numa conta de utilizador local ou a conta de sistema local, o que é especificada no manifesto do aplicativo. Para obter mais informações, consulte [executar um serviço como uma conta de utilizador local ou a conta sistema local](service-fabric-application-runas-security.md).  Também pode [executar um script de arranque do serviço como uma conta de utilizador ou sistema local](service-fabric-run-script-at-service-startup.md).

Quando estiver executando o Service Fabric num cluster autónomo do Windows, pode executar um serviço sob [contas de domínio do Active Directory](service-fabric-run-service-as-ad-user-or-group.md) ou [contas de serviço geridas de grupo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Proteger contentores
O Service Fabric fornece um mecanismo para serviços dentro de um contentor para aceder a um certificado que está instalado em nós num cluster do Windows ou Linux (versão 5.7 ou superior). Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou uma comunicação segura com outros serviços. Para obter mais informações, consulte [importar um certificado para um contentor](service-fabric-securing-containers.md).

Além disso, o Service Fabric suporta também a gMSA (grupo contas de serviço geridas) para contentores do Windows. Para obter mais informações, consulte [configurar o gMSA para contentores do Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Proteger a comunicação de serviço
No Service Fabric, um serviço é executado em algum lugar no cluster do Service Fabric, geralmente distribuído por várias VMs. O Service Fabric fornece várias opções para proteger as comunicações de serviço.

Pode ativar pontos finais HTTPS no seu [ASP.NET Core ou Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) serviços da web.

Pode estabelecer uma ligação segura entre o proxy inverso e serviços, permitindo um canal de segurança de ponta a ponta. Ligar a serviços seguros é suportado apenas quando o proxy inverso está configurado para escutar em HTTPS. Para informações sobre como configurar o proxy inverso, leia [proxy no Azure Service Fabric inverso](service-fabric-reverseproxy.md).  [Ligar a um serviço seguro](service-fabric-reverseproxy-configure-secure-communication.md) descreve como estabelecer uma ligação segura entre o proxy inverso e serviços.

A estrutura da aplicação de Reliable Services fornece algumas ferramentas que pode utilizar para melhorar a segurança e pilhas de comunicação pré-criados. Saiba como melhorar a segurança, quando estiver usando a comunicação remota do serviço (no [ C# ](service-fabric-reliable-services-secure-communication.md) ou [Java](service-fabric-reliable-services-secure-communication-java.md)) ou usando [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Encriptar dados da aplicação em repouso
Cada [tipo de nó](service-fabric-cluster-nodetypes.md) num cluster do Service Fabric em execução no Azure é apoiada por um [conjunto de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric.  Se os serviços de salvar dados num disco de dados anexados, pode [criptografar esses discos de dados](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) para proteger os dados da aplicação.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes
* [Executar um script de configuração na inicialização do serviço](service-fabric-run-script-at-service-startup.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Saiba mais sobre a segurança do cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png