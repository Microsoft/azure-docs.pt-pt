---
title: Proteger um cluster de Service Fabric do Azure
description: Saiba mais sobre cenários de segurança para um cluster de Service Fabric do Azure e as várias tecnologias que você pode usar para implementá-los.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: 92d2c4d03075eaafce039f94b4f03c0791985b40
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746930"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança de Cluster Service Fabric

Um Cluster Service Fabric do Azure é um recurso que você possui. É sua responsabilidade proteger seus clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster. É possível criar um cluster não seguro, no entanto, se o cluster expõe pontos de extremidade de gerenciamento para a Internet pública, os usuários anônimos podem se conectar a ele. Não há suporte para clusters não seguros para cargas de trabalho de produção. 

Este artigo é uma visão geral dos cenários de segurança para clusters do Azure e clusters autônomos e as várias tecnologias que você pode usar para implementá-los:

* Segurança de nó para nó
* Segurança de cliente para nó
* Controlo de Acesso Baseado em Funções (RBAC)

## <a name="node-to-node-security"></a>Segurança de nó para nó

A segurança de nó para nó ajuda a proteger a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança garante que apenas os computadores autorizados a ingressar no cluster possam participar da Hospedagem de aplicativos e serviços no cluster.

![Diagrama de comunicação de nó para nó][Node-to-Node]

Os clusters em execução no Azure e clusters autônomos em execução no Windows podem usar a [segurança do certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança de certificado de nó para nó

O Service Fabric usa certificados de servidor X. 509 que você especifica como parte da configuração de tipo de nó ao criar um cluster. No final deste artigo, você pode ver uma breve visão geral do que são esses certificados e como você pode adquiri-los ou criá-los.

Configure a segurança do certificado ao criar o cluster, seja na portal do Azure, usando um modelo de Azure Resource Manager ou usando um modelo JSON autônomo. Service Fabric comportamento padrão do SDK é implantar e instalar o certificado com o mais distante no certificado expirando futuramente; o comportamento clássico permitia a definição de certificados primários e secundários, para permitir substituições iniciadas manualmente e não é recomendado para uso sobre a nova funcionalidade. Os certificados primários que serão usados têm o mais distante da data de expiração futura, devem ser diferentes do cliente administrador e dos certificados de cliente somente leitura que você definiu para [a segurança de cliente para nó](#client-to-node-security).

Para saber como configurar a segurança de certificado em um cluster para o Azure, consulte [configurar um cluster usando um modelo de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança de certificado em um cluster para um cluster autônomo do Windows Server, consulte [proteger um cluster autônomo no Windows usando certificados X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Windows de nó para nó

Para saber como configurar a segurança do Windows para um cluster autônomo do Windows Server, consulte [proteger um cluster autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Segurança de cliente para nó

A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a garantir que somente usuários autorizados possam acessar o cluster e os aplicativos que são implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou suas credenciais de segurança de certificado.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Os clusters em execução no Azure e clusters autônomos em execução no Windows podem usar a [segurança do certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança de certificado de cliente para nó

Configure a segurança de certificado de cliente para nó ao criar o cluster, seja na portal do Azure, usando um modelo do Resource Manager ou usando um modelo JSON autônomo. Para criar o certificado, especifique um certificado de cliente de administrador ou um certificado de cliente de usuário. Como prática recomendada, os certificados do cliente administrador e do cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários especificados para a [segurança de nó para nó](#node-to-node-security). Os certificados de cluster têm os mesmos direitos que os certificados de administrador de cliente. No entanto, eles devem ser usados apenas pelo cluster e não por usuários administrativos como uma prática recomendada de segurança.

Os clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento. Os clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura só têm acesso de leitura aos recursos de gerenciamento. Esses certificados são usados para o RBAC que é descrito posteriormente neste artigo.

Para saber como configurar a segurança de certificado em um cluster para o Azure, consulte [configurar um cluster usando um modelo de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança de certificado em um cluster para um cluster autônomo do Windows Server, consulte [proteger um cluster autônomo no Windows usando certificados X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança de Azure Active Directory de cliente para nó no Azure

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso do usuário aos aplicativos. Os aplicativos são divididos neles com uma interface de usuário de entrada baseada na Web e com uma experiência de cliente nativo. Se você ainda não criou um locatário, comece lendo [como obter um locatário Azure Active Directory][active-directory-howto-tenant].

Um Cluster Service Fabric oferece vários pontos de entrada para sua funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] baseado na Web e o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você cria dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para clusters em execução no Azure, você também pode proteger o acesso a pontos de extremidade de gerenciamento usando o Azure Active Directory (Azure AD). Para saber como criar os artefatos do Azure AD necessários e como preenchê-los ao criar o cluster, consulte [Configurar o Azure ad para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Recomendações de segurança

Para Service Fabric clusters implantados em uma rede pública hospedada no Azure, a recomendação para a autenticação mútua de cliente para nó é:

* Usar Azure Active Directory para a identidade do cliente
* Um certificado para a identidade do servidor e criptografia SSL da comunicação http

Para Service Fabric clusters implantados em uma rede pública hospedada no Azure, a recomendação para segurança de nó para nó é usar um certificado de cluster para autenticar nós.

Para clusters autônomos do Windows Server, se você tiver o Windows Server 2012 R2 e o Windows Active Directory, recomendamos que você use a segurança do Windows com contas de serviço gerenciado de grupo. Caso contrário, use a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

Você pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Há suporte para dois tipos de controle de acesso para clientes que se conectam a um cluster: função de administrador e função de usuário.

Os usuários que recebem a função de administrador têm acesso completo aos recursos de gerenciamento, incluindo recursos de leitura e gravação. Por padrão, os usuários que recebem a função de usuário têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta). Eles também podem resolver aplicativos e serviços.

Defina as funções de cliente de administrador e de usuário ao criar o cluster. Atribua funções fornecendo identidades separadas (por exemplo, usando certificados ou o Azure AD) para cada tipo de função. Para obter mais informações sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [controle de acesso baseado em função para clientes Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Service Fabric e certificados X. 509

Os certificados digitais X. 509 normalmente são usados para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente as mensagens. O Service Fabric usa certificados X. 509 para proteger um cluster e fornecer recursos de segurança de aplicativo. Para obter mais informações sobre certificados digitais X. 509, consulte [trabalhando com certificados](https://msdn.microsoft.com/library/ms731899.aspx). Você usa [Key Vault](../key-vault/key-vault-overview.md) para gerenciar certificados para Service Fabric clusters no Azure.

Algumas coisas importantes a considerar:

* Para criar certificados para clusters que estão executando cargas de trabalho de produção, use um serviço de certificado do Windows Server configurado corretamente ou um de uma [autoridade de certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority)aprovada.
* Nunca use nenhum certificado temporário ou de teste criado usando ferramentas como MakeCert. exe em um ambiente de produção.
* Você pode usar um certificado autoassinado, mas apenas em um cluster de teste. Não use um certificado autoassinado na produção.
* Ao gerar a impressão digital do certificado, certifique-se de gerar uma impressão digital SHA1. SHA1 é o que é usado ao configurar o cliente e as impressões digitais do certificado de cluster.

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (obrigatório)

Esses certificados (um primário e opcionalmente um secundário) são necessários para proteger um cluster e impedir o acesso não autorizado a ele. Esses certificados fornecem autenticação de cluster e de servidor.

A autenticação de cluster autentica a comunicação de nó para nó para a Federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster. A autenticação de servidor autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o cliente de gerenciamento saiba que ele está se comunicando com o cluster real e não um "Man no meio". Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para Service Fabric Explorer por HTTPS. Quando um cliente ou nó autentica um nó, uma das verificações iniciais é o valor do nome comum no campo **assunto** . Esse nome comum ou um dos nomes alternativos da entidade (SANs) dos certificados deve estar presente na lista de nomes comuns permitidos.

O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada. Esses certificados normalmente têm extensões. pfx ou. pem  
* O certificado deve ser criado para troca de chaves, que é exportável para um arquivo de troca de informações pessoais (. pfx).
* O **nome da entidade do certificado deve corresponder ao domínio que você usa para acessar o cluster de Service Fabric**. Essa correspondência é necessária para fornecer um SSL para o ponto de extremidade de gerenciamento HTTPS do cluster e Service Fabric Explorer. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio *. cloudapp.azure.com. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Algumas outras coisas a considerar:

* O campo **assunto** pode ter vários valores. Cada valor é prefixado com uma inicialização para indicar o tipo de valor. Normalmente, a inicialização é **CN** (para *nome comum*); por exemplo, **CN = www\.contoso.com**.
* O campo **assunto** pode ficar em branco.
* Se o campo **nome alternativo da entidade** opcional for preenchido, ele deverá ter o nome comum do certificado e uma entrada por San. Eles são inseridos como valores de **nome DNS** . Para saber como gerar certificados com SANs, consulte [como adicionar um nome alternativo da entidade a um certificado LDAP seguro](https://support.microsoft.com/kb/931351).
* O valor do campo **finalidades pretendidas** do certificado deve incluir um valor apropriado, como **autenticação de servidor** ou **autenticação de cliente**.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcional)

Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança de aplicativo. Antes de criar o cluster, considere os cenários de segurança do aplicativo que exigem a instalação de um certificado nos nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo.
* Criptografia de dados entre nós durante a replicação.

O conceito de criação de clusters seguros é o mesmo, sejam eles clusters Linux ou Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação de cliente (opcional)

Qualquer número de certificados adicionais pode ser especificado para operações de cliente de administrador ou de usuário. O cliente pode usar esse certificado quando a autenticação mútua é necessária. Os certificados de cliente normalmente não são emitidos por uma CA de terceiros. Em vez disso, o repositório pessoal do local do usuário atual geralmente contém certificados de cliente colocados ali por uma autoridade raiz. O certificado deve ter um valor **final desejado** de **autenticação do cliente**.  

Por padrão, o certificado de cluster tem privilégios de cliente de administrador. Esses certificados de cliente adicionais não devem ser instalados no cluster, mas são especificados como sendo permitidos na configuração do cluster.  No entanto, os certificados do cliente precisam ser instalados nos computadores cliente para se conectarem ao cluster e executar qualquer operação.

> [!NOTE]
> Todas as operações de gerenciamento em um Cluster Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster no Azure usando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Criar um cluster usando o portal do Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
