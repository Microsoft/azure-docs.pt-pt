---
title: Visão geral da segurança de Service Fabric do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do Azure Service Fabric Security.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 5329323122d8470b19055040b6f00b6db7c0e75f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998954"
---
# <a name="azure-service-fabric-security-overview"></a>Visão geral da segurança do Azure Service Fabric
O [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microservices escalonáveis e confiáveis. Service Fabric resolve os desafios de desenvolver e gerenciar aplicativos em nuvem. Os desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrar na implementação de cargas de trabalho críticas e exigentes que são escalonáveis e confiáveis.

Este artigo é uma visão geral das considerações de segurança para uma implantação de Service Fabric.

## <a name="secure-your-cluster"></a>Proteger o seu cluster
O Azure Service Fabric orquestra serviços em um cluster de máquinas. Os clusters devem ser protegidos para impedir que usuários não autorizados se conectem a eles, especialmente quando estão executando cargas de trabalho de produção. Embora seja possível criar um cluster não seguro, isso pode permitir que usuários anônimos se conectem ao cluster (se ele expuser pontos de extremidade de gerenciamento para a Internet pública).

Para clusters que estão sendo executados em um autônomo ou no Azure, dois cenários a considerar são segurança de nó para nó e segurança de cliente para nó. Você pode usar várias tecnologias para implementar esses cenários.

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó se aplica à comunicação entre as VMs ou computadores em um cluster. Com a segurança de nó para nó, somente os computadores autorizados a ingressar no cluster podem participar de aplicativos e serviços de hospedagem no cluster.

Os clusters que estão em execução no Azure ou clusters autônomos que estão em execução no Windows podem usar a [segurança do certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

#### <a name="node-to-node-certificate-security"></a>Segurança de certificado de nó para nó

O Service Fabric usa certificados de servidor X. 509 que você especifica ao criar um cluster. Para obter uma visão geral rápida do que são esses certificados e como você pode adquiri-los ou criá-los, consulte [trabalhando com certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Você configura a segurança do certificado ao criar o cluster por meio do portal do Azure, Azure Resource Manager modelos ou um modelo JSON autônomo. Você pode especificar um certificado primário e um certificado secundário opcional que é usado para sobreposições de certificado. Os certificados primários e secundários que você especificar devem ser diferentes do cliente administrador e dos certificados de cliente somente leitura que você especificar para [a segurança de cliente para nó](../../service-fabric/service-fabric-cluster-security.md).

### <a name="client-to-node-security"></a>Segurança de cliente para nó
Você configura a segurança de cliente para nó usando identidades de cliente. Para estabelecer a confiança entre um cliente e um cluster, você deve configurar o cluster para saber quais identidades de cliente ele pode confiar.

O Service Fabric dá suporte a dois tipos de controle de acesso para clientes que estão conectados a um Cluster Service Fabric:

-   **Administrador**: Acesso completo a recursos de gerenciamento, incluindo recursos de leitura/gravação.
-   **Usuário**: Somente acesso de leitura a recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.

Usando o controle de acesso, os administradores de cluster podem limitar o acesso a determinados tipos de operações de cluster. Isso torna o cluster mais seguro.

#### <a name="client-to-node-certificate-security"></a>Segurança de certificado de cliente para nó

Você configura a segurança de certificado de cliente para nó ao criar um cluster por meio do portal do Azure, modelos do Resource Manager ou um modelo JSON autônomo. Você precisa especificar um certificado de cliente de administrador e/ou um certificado de cliente de usuário. Certifique-se de que esses certificados sejam diferentes dos certificados primários e secundários que você especificar para a segurança de nó para nó.

Os clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento. Os clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura só têm acesso de leitura aos recursos de gerenciamento. Em outras palavras, esses certificados são usados para RBAC (controle de acesso baseado em função).

Para saber como configurar a segurança de certificado em um cluster, consulte [configurar um cluster usando um modelo de Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

#### <a name="client-to-node-azure-active-directory-security"></a>Segurança de Azure Active Directory de cliente para nó

Os clusters que estão em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o Azure Active Directory (Azure AD). Para obter informações sobre como criar os artefatos de Azure Active Directory necessários, como preenchê-los durante a criação do cluster e como se conectar a esses clusters, consulte [configurar um cluster usando um modelo de Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso do usuário aos aplicativos. Há aplicativos com uma interface de usuário de entrada baseada na Web e aplicativos com uma experiência de cliente nativo.

Um Cluster Service Fabric oferece vários pontos de entrada para sua funcionalidade de gerenciamento, incluindo o Service Fabric Explorer baseado na Web e o Visual Studio. Como resultado, você cria dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo.

Para clusters do Azure, recomendamos que você use a segurança do Azure AD para autenticar clientes e certificados para a segurança de nó para nó.

Para clusters autônomos do Windows Server com o Windows Server 2012 R2 e Active Directory, recomendamos que você use a segurança do Windows com contas de serviço gerenciado de grupo (gMSAs). Caso contrário, use a segurança do Windows com contas do Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Entender o monitoramento e o diagnóstico no Service Fabric
O [monitoramento e o diagnóstico](../../service-fabric/service-fabric-diagnostics-overview.md) são essenciais para o desenvolvimento, o teste e a implantação de aplicativos e serviços em qualquer ambiente. Service Fabric soluções funcionam melhor quando você implementa o monitoramento e o diagnóstico para garantir que os aplicativos e serviços funcionem conforme o esperado em um ambiente de desenvolvimento local ou em produção.

Do ponto de vista da segurança, os principais objetivos do monitoramento e diagnóstico são:

-   Detectar e diagnosticar problemas de hardware e infraestrutura que podem ser causados por um evento de segurança.
-   Detectar problemas de software e aplicativo que podem ser um indicador de comprometimento (IoC).
-   Entenda o consumo de recursos para ajudar a evitar a negação de serviço inadvertida.

O fluxo de trabalho de monitoramento e diagnóstico consiste em três etapas:

1.  **Geração de evento**: A geração de eventos inclui eventos (logs, rastreamentos, eventos personalizados) no nível de infraestrutura (cluster) e de aplicativo/serviço. Leia mais sobre [eventos de nível de infraestrutura](../../service-fabric/service-fabric-diagnostics-event-generation-infra.md) e [eventos de nível de aplicativo](../../service-fabric/service-fabric-diagnostics-event-generation-app.md) para entender o que é fornecido e como adicionar mais instrumentação.

2.  **Agregação de eventos**: Os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos. Normalmente, recomendamos o uso de [diagnóstico do Azure](../../service-fabric/service-fabric-diagnostics-event-aggregation-wad.md) (semelhante à coleta de logs baseada em agente) ou [EventFlow](../../service-fabric/service-fabric-diagnostics-event-aggregation-eventflow.md) (coleta de log em processo).

3.  **Análise**: Os eventos precisam ser visualizados e estar acessíveis em algum formato, para permitir a análise e a exibição. Há várias plataformas para a análise e a visualização de dados de monitoramento e diagnóstico. É recomendável [Azure monitor logs](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) e [aplicativo Azure insights](../../service-fabric/service-fabric-diagnostics-event-analysis-appinsights.md) porque eles se integram bem com o Service Fabric.

Você também pode usar [Azure monitor](../../azure-monitor/overview.md) para monitorar muitos dos recursos do Azure nos quais um Cluster Service Fabric foi criado.

Um Watchdog é um serviço separado que pode observar a integridade e a carga entre serviços e relatar a integridade de qualquer coisa na hierarquia do modelo de integridade. O uso de um Watchdog pode ajudar a evitar erros que não seriam detectados com base na exibição de um único serviço. 

Os Watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação com o usuário. Um exemplo é a limpeza dos arquivos de log no armazenamento em determinados intervalos de tempo. Você pode encontrar uma implementação de serviço WatchDog de exemplo no [Azure Service Fabric exemplo de Watchdog](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Comunicação segura usando certificados
Os certificados ajudam você a proteger a comunicação entre os vários nós do seu cluster do Windows autônomo. Usando certificados X. 509, você também pode autenticar clientes que estão se conectando a esse cluster. Isso garante que somente usuários autorizados possam acessar o cluster. Recomendamos que você habilite um certificado no cluster ao criá-lo.

Os certificados digitais X. 509 são normalmente usados para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente as mensagens.

A tabela a seguir lista os certificados necessários na configuração do cluster:

|Configuração de informações de certificado |Descrição|
|-------------------------------|-----------|
|ClusterCertificate|    Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados de cluster: um certificado primário e um secundário para atualização.|
|ServerCertificate| Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Você pode usar dois certificados de servidor: um certificado primário e um secundário para atualização.|
|ClientCertificateThumbprints|  Este é um conjunto de certificados a serem instalados nos clientes autenticados.|
|ClientCertificateCommonNames|  Este é o nome comum do primeiro certificado de cliente para CertificateCommonName. CertificateIssuerThumbprint é a impressão digital do emissor deste certificado.|
|ReverseProxyCertificate|   Esse é um certificado opcional que você pode especificar para proteger seu [proxy reverso](../../service-fabric/service-fabric-reverseproxy.md).|

Para obter mais informações sobre como proteger certificados, consulte [proteger um cluster autônomo no Windows usando certificados X. 509](../../service-fabric/service-fabric-windows-cluster-x509-security.md).

## <a name="understand-role-based-access-control"></a>Entender o controle de acesso baseado em função
Você especifica as funções de cliente de administrador e de usuário no momento da criação do cluster, fornecendo identidades separadas (incluindo certificados) para cada uma. Para obter mais informações sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [controle de acesso baseado em função para clientes Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Proteger clusters autônomos usando a segurança do Windows
Para impedir o acesso não autorizado a um Cluster Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Configure a segurança de nó para nó e de cliente para nó usando a segurança do Windows no arquivo ClusterConfig. JSON.

Quando Service Fabric precisa ser executado em um gMSA, você configura a segurança de nó para nó definindo [ClustergMSAIdentity](../../service-fabric/service-fabric-windows-cluster-windows-security.md). Para criar relações de confiança entre nós, você deve deixá-los cientes uns dos outros.

Se você quiser usar um grupo de computadores em um domínio Active Directory, configure a segurança de nó para nó definindo ClusterIdentity. Para obter mais informações, consulte [criar um grupo de computadores em Active Directory](https://msdn.microsoft.com/library/aa545347).

Configure a segurança de cliente para nó usando o ClientIdentities. Você deve configurar o cluster para reconhecer quais identidades de cliente ele pode confiar. Você pode estabelecer confiança de duas maneiras:

-   Especifique os usuários do grupo de domínio que podem se conectar.
-   Especifique os usuários do nó de domínio que podem se conectar.

## <a name="configure-application-security-in-service-fabric"></a>Configurar a segurança do aplicativo no Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gerenciar segredos em aplicativos Service Fabric
Os segredos podem ser qualquer informação confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser manipulados em texto sem formatação.

Você pode usar [Azure Key Vault](../../key-vault/key-vault-overview.md) para gerenciar chaves e segredos. No entanto, o uso de segredos em um aplicativo não depende de uma plataforma de nuvem específica. Você pode implantar aplicativos em um cluster hospedado em qualquer lugar. Há quatro etapas principais neste fluxo:

1.  Obter um certificado de codificação de dados.
2.  Instale o certificado em seu cluster.
3.  Criptografe valores secretos ao implantar um aplicativo com o certificado e insira-os no arquivo de configuração Settings. XML de um serviço.
4.  Leia os valores criptografados de Settings. xml descriptografando-os com o mesmo certificado de codificação.

Para obter mais informações, consulte [gerenciar segredos em Service Fabric aplicativos](../../service-fabric/service-fabric-application-secret-management.md).

### <a name="configure-security-policies-for-an-application"></a>Configurar políticas de segurança para um aplicativo
Usando o Azure Service Fabric Security, você pode ajudar a proteger aplicativos em execução no cluster em diferentes contas de usuário. Service Fabric segurança também ajuda a proteger os recursos que os aplicativos usam no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais seguros.

As tarefas para configurar as políticas de segurança incluem:

-   Configurando a política para um ponto de entrada de instalação do serviço
-   Iniciando comandos do PowerShell de um ponto de entrada de instalação
-   Usando o redirecionamento de console para depuração local
-   Configurando uma política para pacotes de código de serviço
-   Atribuindo uma política de acesso de segurança para pontos de extremidade HTTP e HTTPS

## <a name="secure-communication-for-services"></a>Comunicação segura para serviços
A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Para obter mais informações, consulte [proteger comunicações remotas de serviço para um serviço](../../service-fabric/service-fabric-reliable-services-secure-communication.md).

## <a name="next-steps"></a>Passos Seguintes
- Para obter informações conceituais sobre a segurança do cluster, consulte [criar um Service Fabric cluster usando Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md) e [criar um Cluster Service Fabric usando o portal do Azure](../../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Para saber mais sobre a segurança de cluster no Service Fabric, consulte [Service Fabric cenários de segurança de cluster](../../service-fabric/service-fabric-cluster-security.md).
