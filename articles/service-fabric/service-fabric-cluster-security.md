---
title: Proteger um cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre cenários de segurança para um cluster do Azure Service Fabric e as várias tecnologias que pode usar para implementá-la.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: 9b36332382de1317e386af59695f993efb233e79
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108448"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança de cluster do Service Fabric
Um cluster do Azure Service Fabric é um recurso que é proprietário. É da responsabilidade do cliente para proteger os seus clusters para ajudar a impedir que os utilizadores não autorizados a ligar aos mesmos. Um cluster seguro é especialmente importante quando estiver a executar cargas de trabalho de produção no cluster. Embora seja possível criar um cluster não seguro, se o cluster expõe os pontos finais de gestão para a internet pública, usuários anônimos podem ligar à mesma. Sem segurança de clusters não é suportado para cargas de trabalho de produção. 

Este artigo é uma visão geral dos cenários de segurança para as várias tecnologias que pode usar para implementá-los e clusters autónomos e clusters do Azure:

* Segurança de nó para nó
* Segurança de cliente para nó
* Controlo de Acesso Baseado em Funções (RBAC)

## <a name="node-to-node-security"></a>Segurança de nó para nó
Nó para nó security o ajuda a comunicação segura entre VMs ou computadores num cluster. Neste cenário de segurança garante que apenas os computadores que estejam autorizados a aderir o cluster podem participar de hospedar aplicativos e serviços no cluster.

![Diagrama de comunicação de nó para nó][Node-to-Node]

Clusters em execução no Azure e autónomo clusters em execução no Windows, ambos os podem utilizar tanto [segurança de certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança do certificado de nó para nó
O Service Fabric utiliza certificados de servidor X.509 que especificar como parte da configuração do tipo de nó quando cria um cluster. No final deste artigo, pode ver uma breve descrição geral de quais estes certificados são e como pode adquirir ou criá-los.

Configure a segurança de certificado ao criar o cluster, no portal do Azure, utilizando um modelo Azure Resource Manager ou utilizando um modelo JSON autónomo. Comportamento de predefinido do SDK do Service Fabric é implementar e instalar o certificado com o furthest para o certificado expirar futuro; o comportamento clássico permitido definir dos certificados primários e secundários, para permitir sobreposições de iniciadas manualmente e não é recomendado para utilização durante a nova funcionalidade. Os certificados primários que serão o uso tem furthest no futuro prestes a expirar, deve ser diferente do cliente administrativo e certificados de cliente só de leitura que definiu para [segurança de cliente para nó](#client-to-node-security).

Para saber como configurar a segurança do certificado num cluster do Azure, veja [configurar um cluster com um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança do certificado num cluster para um cluster autónomo de Windows Server, consulte [proteger um cluster autónomo no Windows com certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Windows de nó para nó
Para saber como configurar a segurança do Windows para um cluster autónomo de Windows Server, consulte [proteger um cluster autónomo no Windows com segurança do Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Segurança de cliente para nó
Segurança de cliente para nó autentica os clientes e ajuda a comunicação segura entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados conseguem aceder ao cluster e as aplicações que são implementadas no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou as credenciais de segurança do certificado.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Clusters em execução no Azure e autónomo clusters em execução no Windows, ambos os podem utilizar tanto [segurança de certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança do certificado de cliente para nó
Configure a segurança de certificado de cliente para nó quando criar o cluster, no portal do Azure, utilizando um modelo do Resource Manager ou utilizando um modelo JSON autónomo. Para criar o certificado, especifique um certificado de cliente de administrador ou um certificado de cliente do utilizador. Como melhor prática, os administrador cliente e o utilizador certificados de cliente que especificar devem ser diferentes do que os certificados primário e secundário que especificou para [segurança de nó para nó](#node-to-node-security). Por predefinição, os certificados de cluster para o nó para nó segurança são adicionados à lista de certificados de cliente permitidas administrador.

Clientes que se ligam ao cluster com o certificado de administrador tem acesso total às capacidades de gestão. Clientes que se ligam ao cluster com o certificado de cliente de utilizador só de leitura têm acesso de leitura às capacidades de gestão. Estes certificados são utilizados para o RBAC descrita neste artigo.

Para saber como configurar a segurança do certificado num cluster do Azure, veja [configurar um cluster com um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança do certificado num cluster para um cluster autónomo de Windows Server, consulte [proteger um cluster autónomo no Windows com certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança do Azure Active Directory de nó de cliente no Azure
O Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. Aplicativos estão divididos em aquelas com uma conta baseada na web, início de sessão de interface de Usuário e os que têm uma experiência de cliente nativo o limite de tempo. Se ainda não tenha criado um inquilino, comece por ler [como obter um inquilino do Azure Active Directory][active-directory-howto-tenant].

Um cluster do Service Fabric oferece vários pontos de entrada para sua funcionalidade de gestão, incluindo baseada na web [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] e [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Como resultado, vai criar duas aplicações do Azure AD para controlar o acesso ao cluster, uma aplicação web e um aplicativo nativo.

Para clusters em execução no Azure, também pode proteger o acesso aos pontos finais de gestão com o Azure Active Directory (Azure AD). Para saber como criar necessários artefactos do Azure AD e como preenchê-los quando criar o cluster, consulte [configurar o Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Recomendações de segurança
Para clusters do Service Fabric implementados numa rede pública alojada no Azure, a recomendação para autenticação mútua do nó de cliente é:
*   Utilizar o Azure Active Directory para a identidade de cliente
*   Um certificado para encriptação SSL de comunicação http e de identidade do servidor

Para clusters do Service Fabric implementados numa rede pública alojada no Azure, a recomendação de segurança de nó para nó é utilizar um certificado de Cluster para autenticar nós. 


Para clusters do Windows Server de autónomos, se tiver o Windows Server 2012 R2 e do Active Directory do Windows, recomendamos que utilize a segurança do Windows com contas de serviço geridas de grupo. Caso contrário, utilize a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais segura. Dois tipos de controle de acesso são suportados para clientes que se ligam a um cluster: Função de administrador e a função de utilizador.

Os utilizadores que são atribuídos a função de administrador tem acesso total às capacidades de gestão, incluindo ler e escrever capacidades. Os utilizadores que estão atribuídos a função de utilizador, por predefinição, têm acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta). Eles também podem resolver aplicações e serviços.

Defina o cliente de administrador e usuário funções quando criar o cluster. Atribua funções, fornecendo identidades separadas (por exemplo, ao utilizar certificados ou o Azure AD) para cada tipo de função. Para obter mais informações sobre as definições de controlo de acesso predefinido e como alterar as predefinições, veja [controlo de acesso baseado em funções para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e o Service Fabric
Certificados digitais de X.509 normalmente são utilizados para autenticar clientes e servidores. Também são utilizados para encriptar e assinar digitalmente mensagens. O Service Fabric utiliza certificados X.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. Para obter mais informações sobre certificados digitais de X.509, consulte [trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). Utilizar [Key Vault](../key-vault/key-vault-overview.md) para gerir os certificados para clusters do Service Fabric no Azure.

Alguns aspetos importantes a considerar:

* Para criar certificados para os clusters que executem cargas de trabalho de produção, utilize um serviço de certificado de servidor Windows corretamente configurado ou um de um aprovados [autoridade de certificação (AC)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nunca utilize qualquer temporário ou certificados que criar utilizando ferramentas como MakeCert.exe num ambiente de produção de teste.
* Pode utilizar um certificado autoassinado, mas somente num cluster de teste. Não utilize um certificado autoassinado na produção.
* Ao gerar o thumbprint do certificado, certifique-se de que gerar um thumbprint de SHA1. SHA1 é o que é utilizado quando configurar os thumbprints de certificado de cliente e do Cluster.

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e servidor (obrigatório)
Estes certificados (uma primária e, opcionalmente, uma secundária) são necessários para proteger um cluster e evitar acessos não autorizados a ele. Estes certificados de disponibilizar a autenticação do cluster e servidor.

Autenticação do cluster autentica a comunicação de nó para nó para a Federação de cluster. Apenas nós que podem provar a sua identidade com este certificado podem associar o cluster. Autenticação de servidor autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gestão, para que o cliente de gestão saiba que ele está falando ao real cluster e não um "intermediário". Este certificado também fornece um SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS. Quando um cliente ou um nó efetua a autenticação de um nó, uma das verificações iniciais é o valor de nome comum da **assunto** campo. Este nome comum ou um dos nomes de alternativos do requerente dos certificados (SANs) tem de estar presente na lista de nomes comuns permitidos.

O certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada. Estes certificados têm normalmente. pfx de extensões ou. pem  
* O certificado tem de ser criado para troca de chaves, que é exportável para um ficheiro Personal Information Exchange (. pfx).
* O **nome do requerente do certificado tem de corresponder ao domínio que utiliza para aceder ao cluster do Service Fabric**. Esta correspondência é necessária para fornecer um SSL para o ponto final de gestão HTTPS do cluster e a Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o *. domínio cloudapp.azure.com. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Alguns outros aspetos a considerar:

* O **assunto** campo pode ter vários valores. Cada valor é o prefixo uma inicialização para indicar o tipo de valor. Normalmente, é a inicialização **CN** (para *nome comum*), por exemplo, **CN = www\.contoso.com**. 
* O **assunto** campo pode estar em branco. 
* Se o opcional **nome alternativo do requerente** campo é preenchido, tem de ter o nome comum do certificado e uma entrada por SAN. Estes são introduzidos como **nome de DNS** valores. Para saber como gerar os certificados que tenham SANs, veja [como adicionar um nome alternativo do requerente a um certificado de secure LDAP](https://support.microsoft.com/kb/931351).
* O valor do **fins se destina** campo do certificado deve incluir um valor adequado, tal como **autenticação de servidor de** ou **autenticação de cliente**.

### <a name="application-certificates-optional"></a>Certificados de aplicação (opcionais)
Qualquer número de certificados adicionais pode ser instalado num cluster por motivos de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicações que necessitam de um certificado para ser instalado em nós, tal como:

* Encriptação e desencriptação de valores de configuração de aplicações.
* Encriptação de dados entre os nós durante a replicação.

O conceito de criar clusters seguros é o mesmo, independentemente de eles são o Linux ou clusters do Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação de cliente (opcionais)
Qualquer número de certificados adicionais pode ser especificado para operações de cliente de administrador ou utilizador. O cliente pode utilizar este certificado quando é necessária a autenticação mútua. Normalmente, os certificados de cliente não são emitidos por uma AC de terceiros. Em vez disso, o arquivo pessoal da localização atual do usuário normalmente contém os certificados de cliente colocados lá por uma autoridade de raiz. O certificado deve ter uma **fins se destina** valor de **autenticação de cliente**.  

Por predefinição, o certificado de cluster tem privilégios de administrador do cliente. Estes certificados de cliente adicionais não devem ser instalados num cluster, mas são especificados como sendo permitido na configuração do cluster.  No entanto, os certificados de cliente tem de ser instalado nos computadores cliente para ligar ao cluster e executar operações.

> [!NOTE]
> Todas as operações de gestão no cluster do Service Fabric necessitam de certificados de servidor. Certificados de cliente não podem ser utilizados para gestão.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um cluster no Azure com um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Criar um cluster com o portal do Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
