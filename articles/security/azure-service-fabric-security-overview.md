---
title: Descrição geral de segurança do Azure Service Fabric | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da segurança do Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241396"
---
# <a name="azure-service-fabric-security-overview"></a>Descrição geral de segurança do Azure Service Fabric
[O Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) é uma plataforma de sistemas distribuídos que facilita o empacotamento, implementação e a gestão de microsserviços dimensionáveis e fiáveis. Service Fabric face aos desafios de desenvolvimento e gestão de aplicações na cloud. Os desenvolvedores e administradores, podem evitar problemas complexos de infraestrutura e se concentrem na implementação de missão crítica, exigentes cargas de trabalho que são dimensionáveis e fiáveis.

Este artigo é uma descrição geral das considerações de segurança para uma implementação do Service Fabric.

## <a name="secure-your-cluster"></a>Proteger o seu cluster
O Azure Service Fabric orquestra os serviços num cluster de máquinas. Clusters tem de ser protegidos para impedir que os utilizadores não autorizados a ligar aos mesmos, especialmente quando estiver a executar cargas de trabalho de produção. Embora seja possível criar um cluster não seguro, isso pode permitir que os utilizadores anónimos ligar ao cluster (se ele expõe os pontos finais de gestão para a internet pública).

Para clusters que sejam executados de forma autónoma ou no Azure, dois cenários a considerar são a segurança de nó para nó e de segurança de cliente para nó. Pode usar várias tecnologias para implementar esses cenários.

### <a name="node-to-node-security"></a>Segurança de nó para nó
Segurança de nó para nó se aplica a comunicação entre VMs ou máquinas num cluster. Com a segurança de nó para nó, apenas os computadores que estejam autorizados a aderir o cluster podem participar hospedar aplicativos e serviços no cluster.

Os clusters que estão em execução em clusters do Azure ou autónomos, que estão em execução no Windows podem utilizar qualquer um [segurança de certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para máquinas do Windows Server.

#### <a name="node-to-node-certificate-security"></a>Segurança do certificado de nó para nó

O Service Fabric utiliza certificados de servidor X.509 que especificou ao criar um cluster. Para uma visão geral de quais estes certificados são e como pode adquirir ou criá-los, consulte [trabalhar com certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Configurar segurança de certificado quando criar o cluster através do portal do Azure, modelos Azure Resource Manager ou um modelo JSON autónomo. Pode especificar um certificado principal e um certificado secundário opcional que é utilizado para sobreposições de certificado. Os certificados primários e secundários que especificar devem ser diferentes do cliente administrativo e certificados de cliente só de leitura que especificou para [segurança de cliente para nó](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Segurança de cliente para nó
Configurar segurança de cliente para nó usando identidades de cliente. Para estabelecer confiança entre um cliente e um cluster, tem de configurar o cluster para saber qual cliente identidades pode confiar.

Service Fabric suporta dois tipos de controle de acesso para clientes que estão ligados a um cluster do Service Fabric:

-   **Administrador**: Acesso total às capacidades de gestão, incluindo capacidades de leitura/escrita.
-   **Utilizador**: Acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços.

Ao utilizar o controlo de acesso, os administradores de cluster podem limitar o acesso a determinados tipos de operações de cluster. Desta forma, o cluster mais seguro.

#### <a name="client-to-node-certificate-security"></a>Segurança do certificado de cliente para nó

Configurar segurança de certificado de cliente para nó quando cria um cluster através do portal do Azure, modelos do Resource Manager ou um modelo JSON autónomo. Tem de especificar um certificado de cliente administrativo e/ou um certificado de cliente do utilizador. Certifique-se de que estes certificados são diferentes dos certificados primários e secundários que especificou para segurança de nó para nó.

Clientes que se ligam ao cluster com o certificado de administrador tem acesso total às capacidades de gestão. Clientes que se ligam ao cluster com o certificado de cliente de utilizador só de leitura têm acesso de leitura às capacidades de gestão. Em outras palavras, estes certificados são utilizados para controlo de acesso baseado em funções (RBAC).

Para saber como configurar a segurança de certificado num cluster, veja [configurar um cluster com um modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Segurança do nó de cliente do Azure Active Directory

Clusters que estão em execução no Azure também podem proteger o acesso para os pontos finais de gestão com o Azure Active Directory (Azure AD). Para obter informações sobre como criar os artefactos necessários do Azure Active Directory, como preenchê-los durante a criação do cluster e como ligar a esses grupos, consulte [configurar um cluster com um modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

O Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. Existem aplicativos com um baseada na web início de sessão da interface do Usuário e aplicativos com uma experiência de cliente nativo.

Um cluster do Service Fabric oferece vários pontos de entrada para sua funcionalidade de gestão, incluindo o Service Fabric Explorer e Visual Studio baseado na web. Como resultado, vai criar duas aplicações do Azure AD para controlar o acesso ao cluster: aplicação web de um e um aplicativo nativo.

Para os clusters do Azure, recomendamos que utilize a segurança do Azure AD para autenticar clientes e certificados para a segurança de nó para nó.

Para clusters do Windows Server de autónomos com o Windows Server 2012 R2 e o Active Directory, recomendamos que de usar a segurança do Windows com contas de serviço geridas de grupo (gMSAs). Caso contrário, utilize a segurança do Windows com contas do Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Compreender a monitorização e diagnóstico no Service Fabric
[Monitorização e diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são fundamentais para o desenvolvimento, teste e implantação de aplicativos e serviços em qualquer ambiente. Soluções do Service Fabric funcionam melhor quando implementa a monitorização e diagnóstico para se certificar de que as aplicações e serviços funcionam conforme esperado num ambiente de desenvolvimento local ou na produção.

Da perspectiva de segurança, as principais metas da monitorização e diagnóstico são:

-   Detetar e diagnosticar problemas de hardware e a infraestrutura que podem ser causados por um evento de segurança.
-   Detete problemas de software e aplicação que podem ser um indicador de comprometimento (IoC).
-   Compreenda o consumo de recursos para ajudar a evitar inadvertida negação de serviço.

O fluxo de trabalho de monitorização e diagnóstico consiste em três passos:

1.  **Geração de evento**: Geração de eventos inclui eventos (registos, rastreios, eventos personalizados), no nível da infraestrutura (cluster) e o nível de aplicação/serviço. Leia mais sobre [eventos de nível de infra-estrutura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) e [eventos de nível de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para compreender o que é fornecido e como adicionar mais instrumentação.

2.  **Agregação de eventos**: Eventos gerados tem de ser recolhidos e agregados antes de poderem ser apresentados. Normalmente, recomendamos que utilize [diagnóstico do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (semelhante a recolha de registos com base em agente) ou [o EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (-processo de recolha de registos).

3.  **Análise**: Eventos têm de ser visualizadas e acessíveis em algum formato, para permitir a análise e visualização. Existem várias plataformas para a análise e visualização de dados de monitorização e diagnóstico. Recomendamos [registos do Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) porque eles se integram bem com o Service Fabric.

Também pode utilizar [do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) para monitorizar muitos dos recursos do Azure em que é criado um cluster do Service Fabric.

Um watchdog é um serviço separado que pode ver o estado de funcionamento e de carga em serviços de estado de funcionamento do relatório e qualquer coisa na hierarquia de modelo de estado de funcionamento. Usar um watchdog pode ajudar a evitar erros que não seriam detetados com base na vista de um único serviço. 

Watchdogs também são um bom lugar para código de anfitrião que executa as ações corretivas sem interação do utilizador. Um exemplo é limpeza dos ficheiros de registo no armazenamento em determinados intervalos de tempo. Pode encontrar uma implementação de serviços de watchdog de exemplo na [exemplo de watchdog de Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Proteger a comunicação com certificados
Certificados de ajudar a proteger a comunicação entre os vários nós do seu cluster autónomo do Windows. Ao utilizar certificados X.509, também pode autenticar clientes que estão a ligar a este cluster. Isto garante que apenas os utilizadores autorizados podem aceder ao cluster. Recomendamos que ative um certificado no cluster ao criá-la.

Certificados digitais de X.509 são utilizados para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente mensagens.

A tabela seguinte lista os certificados de que precisa no seu programa de configuração do cluster:

|Definição de informações do certificado |Descrição|
|-------------------------------|-----------|
|ClusterCertificate|    Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode usar dois certificados de cluster: um certificado principal e um secundário para atualização.|
|ServerCertificate| Este certificado é apresentado para o cliente quando tentar ligar a este cluster. Pode usar dois certificados de servidor: um certificado principal e um secundário para atualização.|
|ClientCertificateThumbprints|  Este é um conjunto de certificados a instalar nos clientes autenticados.|
|ClientCertificateCommonNames|  Este é o nome comum do certificado de cliente primeiro CertificateCommonName. CertificateIssuerThumbprint é o thumbprint para o emissor deste certificado.|
|ReverseProxyCertificate|   Este é um certificado opcional que pode especificar a proteger os seus [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para obter mais informações sobre como proteger a certificados, consulte [proteger um cluster autónomo no Windows com certificados X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Compreender o controlo de acesso baseado em funções
Especifique as funções de administrador e utilizador cliente no momento da criação do cluster ao fornecer identidades separadas (incluindo certificados) para cada um. Para obter mais informações sobre as definições de controlo de acesso predefinido e como alterar as definições predefinidas, consulte [controlo de acesso baseado em funções para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Proteger clusters autónomos com a segurança do Windows
Para impedir acesso não autorizado ao cluster do Service Fabric, tem de proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Configurar a segurança de nó para nó e o nó de cliente através de segurança do Windows no arquivo ClusterConfig.JSON.

Quando o Service Fabric tem de ser executado sob uma gMSA, configurar a segurança de nó para nó definindo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Para criar relações de confiança entre os nós, deve fazê-las com reconhecimento de umas das outras.

Se pretender utilizar um grupo de computador dentro de um domínio do Active Directory, configurar a segurança de nó para nó definindo ClusterIdentity. Para obter mais informações, consulte [criar um grupo de computador no Active Directory](https://msdn.microsoft.com/library/aa545347).

Configurar segurança de cliente para nó com ClientIdentities. Tem de configurar o cluster para reconhecer as identidades de cliente pode confiar. Pode estabelecer confiança de duas formas:

-   Especifique os utilizadores do grupo de domínio que podem ligar-se.
-   Especifique os utilizadores de nó de domínio podem ligar-se.

## <a name="configure-application-security-in-service-fabric"></a>Configurar a segurança de aplicação no Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gerir segredos em aplicações do Service Fabric
Segredos podem ser qualquer informações confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples.

Pode usar [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para gerir as chaves e segredos. No entanto, a utilização de segredos num aplicativo não depende de uma plataforma de cloud específico. Pode implementar aplicações para um cluster que está alojada em qualquer lugar. Neste fluxo, existem quatro passos principais:

1.  Obtenha um certificado de encriptação de dados.
2.  Instale o certificado no seu cluster.
3.  Encriptar os valores secretos quando implementar uma aplicação com o certificado e inseri-los no arquivo de configuração de Settings de um serviço.
4.  Leitura de valores encriptados Settings, descriptografando-los com o mesmo certificado de encriptação.

Para obter mais informações, consulte [gerir segredos em aplicações do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Configurar políticas de segurança para uma aplicação
Ao utilizar a segurança do Azure Service Fabric, pode ajudar aplicativos seguros que estão em execução no cluster sob diferentes contas de usuário. Segurança do Service Fabric também ajuda a proteger os recursos que os aplicativos usar no momento da implantação sob as contas de utilizador – por exemplo, arquivos, diretórios e certificados. Isso faz com que aplicativos em execução, mesmo num ambiente de hospedagem compartilhado, mais seguro.

As tarefas para configurar políticas de segurança incluem:

-   Configurar a política para um ponto de entrada de configuração de serviço
-   A iniciar comandos do PowerShell a partir de um ponto de entrada de configuração
-   Utilizar o redirecionamento de consola para depuração local
-   Configurar uma política para pacotes de código de serviço
-   Atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS

## <a name="secure-communication-for-services"></a>Proteger a comunicação de serviços
A segurança é um dos aspectos mais importantes de comunicação. A estrutura da aplicação de Reliable Services fornece algumas ferramentas que pode utilizar para melhorar a segurança e pilhas de comunicação pré-criados. Para obter mais informações, consulte [proteger as comunicações de comunicação remota do serviço para um serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Passos Seguintes
- Para obter informações concetuais sobre segurança do cluster, consulte [criar um cluster do Service Fabric com o Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e [criar um cluster do Service Fabric com o portal do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para saber mais sobre a segurança de cluster no Service Fabric, veja [cenários de segurança de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
