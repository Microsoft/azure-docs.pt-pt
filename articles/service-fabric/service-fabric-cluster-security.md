---
title: Garantir um cluster de tecido de serviço Azure
description: Conheça os cenários de segurança para um cluster de tecidos de serviço Azure, e as várias tecnologias que pode usar para implementá-los.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: 258a6dd141ccc31516e37dac9f265328f981bbf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261067"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança do cluster de tecido de serviço

Um cluster Azure Service Fabric é um recurso que possui. É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster. É possível criar um cluster não seguro, no entanto, se o cluster expor os pontos finais de gestão para a internet pública, os utilizadores anónimos podem ligar-se ao mesmo. Os aglomerados não protegidos não são apoiados para cargas de trabalho de produção. 

Este artigo é uma visão geral dos cenários de segurança para clusters Azure e clusters autónomos, e as várias tecnologias que você pode usar para implementá-los:

* Segurança nó-a-nó
* Segurança cliente-nó
* Controlo de Acesso Baseado em Funções (RBAC)

## <a name="node-to-node-security"></a>Segurança nó-a-nó

A segurança nó-a-nó ajuda a garantir a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster.

![Diagrama de comunicação nó-a-nó][Node-to-Node]

Os clusters em execução em Azure e clusters autónomos em execução no Windows podem utilizar a [segurança](/previous-versions/msp-n-p/ff649801(v=pandp.10)) do certificado ou [a segurança do Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)) para computadores Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança do certificado nó-a-nó

O Service Fabric utiliza certificados de servidor X.509 que especifica como parte da configuração do tipo nó quando cria um cluster. No final deste artigo, pode ver uma breve visão geral do que são estes certificados e como pode adquiri-los ou criá-los.

Configa a segurança do certificado quando criar o cluster, quer no portal Azure, utilizando um modelo de Gestor de Recursos Azure, quer utilizando um modelo JSON autónomo. O comportamento padrão da Service Fabric SDK é implantar e instalar o certificado com o mais longe na data de validade futura; o comportamento clássico permitiu a definição de certificados primários e secundários, para permitir capotamentos iniciados manualmente, e não é recomendado para uso sobre a nova funcionalidade. Os certificados primários que serão utilizados têm o mais longe na data de validade futura, devem ser diferentes dos certificados de cliente administração e apenas de leitura que definiu [para a segurança do cliente-a-nó.](#client-to-node-security)

Para aprender a configurar a segurança do certificado num cluster para o Azure, consulte [configurar um cluster utilizando um modelo de Gestor de Recursos Azure](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar a segurança do certificado num cluster para um cluster autónomo do Windows Server, consulte [Secure a standalone cluster no Windows utilizando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Nó-a-nó Windows

> [!NOTE]
> A autenticação do Windows é baseada em Kerberos. A NTLM não é suportada como um tipo de autenticação.
>
> Sempre que possível, utilize a autenticação do certificado X.509 para clusters de Tecido de Serviço.

Para aprender a configurar a segurança do Windows para um cluster autónomo do Windows Server, consulte [Secure a standalone cluster no Windows utilizando a segurança do Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Segurança cliente-nó

A segurança entre clientes e nó autentica os clientes e ajuda a garantir a comunicação entre um cliente e os nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados possam aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados exclusivamente através das suas credenciais de segurança windows ou das suas credenciais de segurança de certificado.

![Diagrama de comunicação cliente-a-nó][Client-to-Node]

Os clusters em execução em Azure e clusters autónomos em execução no Windows podem utilizar a segurança do [certificado](/previous-versions/msp-n-p/ff649801(v=pandp.10)) ou a segurança do [Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)), embora a recomendação seja utilizar a autenticação do certificado X.509 sempre que possível.

### <a name="client-to-node-certificate-security"></a>Garantia do certificado cliente-nó

Configa a segurança do certificado cliente-nó quando criar o cluster, quer no portal Azure, utilizando um modelo de Gestor de Recursos, quer utilizando um modelo JSON autónomo. Para criar o certificado, especifique um certificado de cliente administrativo ou um certificado de cliente do utilizador. Como uma boa prática, os certificados de cliente administrativo e cliente do utilizador que especifica devem ser diferentes dos certificados primários e secundários que especifica para [a segurança do nó- nó](#node-to-node-security). Os certificados de cluster têm os mesmos direitos que os certificados de administração de clientes. No entanto, devem ser utilizados apenas por agrupamentos e não por utilizadores administrativos como uma boa prática de segurança.

Os clientes que se ligam ao cluster utilizando o certificado de administração têm acesso total às capacidades de gestão. Os clientes que se ligam ao cluster utilizando o certificado de cliente apenas de leitura apenas leram o acesso às capacidades de gestão. Estes certificados são utilizados para o RBAC que é descrito mais tarde neste artigo.

Para aprender a configurar a segurança do certificado num cluster para o Azure, consulte [configurar um cluster utilizando um modelo de Gestor de Recursos Azure](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar a segurança do certificado num cluster para um cluster autónomo do Windows Server, consulte [Secure a standalone cluster no Windows utilizando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança do Diretório Ativo Azure do cliente no Azure

A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso do utilizador às aplicações. As aplicações são divididas em pessoas com uma UI baseada na web e aquelas com uma experiência de cliente nativo. Se ainda não criou um inquilino, comece por ler [Como obter um inquilino do Azure Ative Directory.][active-directory-howto-tenant]

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o Visual [Studio.][service-fabric-manage-application-in-visual-studio] Como resultado, cria duas aplicações AD Azure para controlar o acesso ao cluster, uma aplicação web e uma aplicação nativa.

Para clusters em execução em Azure, também pode garantir o acesso aos pontos finais de gestão utilizando o Azure Ative Directory (Azure AD). Para aprender a criar os artefactos AD AD necessários e como povoá-los quando criar o cluster, consulte [Configurar a AD Azure para autenticar os clientes.](service-fabric-cluster-creation-setup-aad.md)

## <a name="security-recommendations"></a>Recomendações de segurança

Para os clusters de tecidos de serviço implantados numa rede pública hospedada no Azure, a recomendação para a autenticação mútua cliente-nó é:

* Use o Diretório Ativo Azure para a identidade do cliente
* Um certificado para identidade do servidor e encriptação TLS de comunicação http

Para os clusters de tecidos de serviço implantados numa rede pública hospedada no Azure, a recomendação para a segurança do nó-a-nó é usar um certificado cluster para autenticar nós.

Para clusters autónomos do Windows Server, se tiver o Windows Server 2012 R2 e o Windows Ative Directory, recomendamos que utilize a segurança do Windows com contas de serviço geridas do grupo. Caso contrário, utilize a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

Pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. Dois tipos de controlo de acesso são suportados para clientes que se conectam a um cluster: função de administrador e papel do Utilizador.

Os utilizadores a quem é atribuída a função de Administrador têm acesso total às capacidades de gestão, incluindo capacidades de leitura e escrita. Os utilizadores a quem é atribuída a função Utilizador, por padrão, apenas leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta). Também podem resolver aplicações e serviços.

Desa estação as funções de administrador e cliente do utilizador quando criar o cluster. Atribua funções fornecendo identidades separadas (por exemplo, utilizando certificados ou Azure AD) para cada tipo de função. Para obter mais informações sobre as definições de controlo de acesso predefinido e como alterar as definições predefinidos, consulte [o Controlo de Acesso baseado em funções para clientes do Tecido de Serviço](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e Tecido de Serviço

Os certificados digitais X.509 são normalmente utilizados para autenticar clientes e servidores. Também são usados para encriptar e assinar digitalmente mensagens. O Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação. Para obter mais informações sobre os certificados digitais X.509, consulte [Trabalhar com certificados.](/dotnet/framework/wcf/feature-details/working-with-certificates) Você usa [o Key Vault](../key-vault/general/overview.md) para gerir certificados para clusters de tecido de serviço em Azure.

Algumas coisas importantes a considerar:

* Para criar certificados para clusters que estão a executar cargas de trabalho de produção, utilize um serviço de certificados do Windows Server corretamente configurado, ou um de uma autoridade de certificados aprovada [(CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nunca utilize certificados temporários ou de teste que crie utilizando ferramentas como MakeCert.exe num ambiente de produção.
* Pode usar um certificado auto-assinado, mas apenas num aglomerado de testes. Não utilize um certificado auto-assinado em produção.
* Ao gerar a impressão digital do certificado, certifique-se de gerar uma impressão digital SHA1. SHA1 é o que é usado ao configurar as impressões digitais do certificado cliente e cluster.

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e servidor (obrigatório)

Estes certificados (um primário e opcionalmente secundário) são necessários para proteger um cluster e impedir o acesso não autorizado ao mesmo. Estes certificados fornecem autenticação de cluster e servidor.

A autenticação do cluster autentica comunicação nó-a-nó para a federação de cluster. Só os nós que possam provar a sua identidade com este certificado podem juntar-se ao cluster. A autenticação do servidor autentica os pontos finais de gestão do cluster para um cliente de gestão, para que o cliente de gestão saiba que está a falar com o cluster real e não com um "homem no meio". Este certificado também fornece um TLS para a API de gestão HTTPS e para o Service Fabric Explorer em HTTPS. Quando um cliente ou nó autentica um nó, uma das verificações iniciais é o valor do nome comum no campo **Assunto.** Este nome comum ou um dos nomes alternativos sujeitos dos certificados (SANs) devem estar presentes na lista de nomes comuns permitidos.

O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada. Estes certificados normalmente têm extensões .pfx ou .pem  
* O certificado deve ser criado para troca de chaves, que é exportável para um ficheiro De troca de informações pessoais (.pfx).
* O **nome do certificado deve coincidir com o domínio que utiliza para aceder ao cluster de Tecido de Serviço.** Esta correspondência é necessária para fornecer um TLS para o ponto final de gestão HTTPS do cluster e explorador de tecido de serviço. Não é possível obter um certificado TLS/SSL de uma autoridade de certificados (CA) para o domínio *.cloudapp.azure.com. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Outras coisas a considerar:

* O campo **Assunto** pode ter múltiplos valores. Cada valor é pré-fixado com uma inicialização para indicar o tipo de valor. Normalmente, a inicialização é **CN** (para *nome comum);* por exemplo, **CN = www \. contoso.com**.
* O campo **subjecto** pode estar em branco.
* Se o campo de **nome alternativo sujeito** opcional for preenchido, deve ter o nome comum do certificado e uma entrada por SAN. Estes são introduzidos como **valores DNS Name.** Para aprender a gerar certificados que tenham SANs, consulte [Como adicionar um Nome Alternativo ao Assunto a um certificado LDAP seguro](https://support.microsoft.com/kb/931351).
* O valor do campo **final** do certificado deve incluir um valor adequado, como **autenticação do servidor** ou **autenticação do cliente.**

### <a name="application-certificates-optional"></a>Certificados de inscrição (opcional)

Qualquer número de certificados adicionais pode ser instalado num cluster para fins de segurança da aplicação. Antes de criar o seu cluster, considere os cenários de segurança da aplicação que requerem que um certificado seja instalado nos nós, tais como:

* Encriptação e desencriptação dos valores de configuração da aplicação.
* Encriptação de dados através de nós durante a replicação.

O conceito de criar clusters seguros é o mesmo, sejam eles clusters Linux ou Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação do cliente (opcional)

Qualquer número de certificados adicionais pode ser especificado para operações de administração ou cliente do utilizador. O cliente pode usar este certificado quando for necessária autenticação mútua. Normalmente, os certificados de cliente não são emitidos por um CA de terceiros. Em vez disso, a loja Pessoal da localização atual do utilizador normalmente contém certificados de cliente colocados lá por uma autoridade de raiz. O certificado deve ter um valor **de Autenticação** do **Cliente.**  

Por predefinição, o certificado de cluster tem privilégios de administração do cliente. Estes certificados de cliente adicionais não devem ser instalados no cluster, mas são especificados como sendo permitidos na configuração do cluster.  No entanto, os certificados de cliente precisam de ser instalados nas máquinas cliente para se conectarem ao cluster e realizarem quaisquer operações.

> [!NOTE]
> Todas as operações de gestão num cluster de Tecido de Serviço requerem certificados de servidor. Os certificados de cliente não podem ser usados para gestão.

## <a name="next-steps"></a>Passos seguintes

* [Crie um cluster em Azure usando um modelo de Gestor de Recursos](service-fabric-cluster-creation-via-arm.md)
* [Utilizar o portal do Azure para criar um cluster](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
