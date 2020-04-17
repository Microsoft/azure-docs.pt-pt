---
title: Proteja um cluster de tecido de serviço Azure
description: Conheça os cenários de segurança para um cluster Azure Service Fabric, e as várias tecnologias que pode usar para implementá-los.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: c43cfbd4468a64867d50482d9c8055622602f159
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461587"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança do cluster de tecido de serviço

Um cluster Azure Service Fabric é um recurso que possui. É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster. É possível criar um cluster não seguro, no entanto, se o cluster expor pontos finais de gestão à internet pública, os utilizadores anónimos podem ligar-se ao mesmo. Os clusters não seguros não são suportados para cargas de trabalho de produção. 

Este artigo é uma visão geral dos cenários de segurança para clusters Azure e clusters autónomos, e as várias tecnologias que você pode usar para implementá-los:

* Segurança nó-ao-nó
* Segurança cliente-a-nó
* Controlo de Acesso Baseado em Funções (RBAC)

## <a name="node-to-node-security"></a>Segurança nó-ao-nó

A segurança do nó ao nó ajuda a garantir a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster.

![Diagrama de comunicação nó-ao-nó][Node-to-Node]

Os clusters que estão a funcionar em Clusters Azure e autónomos que executam o Windows podem utilizar a segurança do [certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Do Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança de certificado de nó ao nó

O Service Fabric utiliza certificados de servidor X.509 que especifica como parte da configuração do tipo nó quando cria um cluster. No final deste artigo, pode ver uma breve visão geral do que são estes certificados e como pode adquiri-los ou criá-los.

Configurar a segurança do certificado quando criar o cluster, quer no portal Azure, utilizando um modelo de Gestor de Recursos Azure, quer utilizando um modelo JSON autónomo. O comportamento predefinido da SDK de serviço é implantar e instalar o certificado com o mais longe no futuro certificado de caducidade; o comportamento clássico permitiu a definição de certificados primários e secundários, para permitir capotamentos iniciados manualmente, e não é recomendado para uso sobre a nova funcionalidade. Os certificados primários que serão utilizados têm o mais longe na data de validade futura, devem ser diferentes dos certificados de cliente administrativos e apenas de leitura que definiu para [a segurança cliente-a-nó](#client-to-node-security).

Para aprender a configurar a segurança do certificado num cluster para o Azure, consulte [a configuração de um cluster utilizando um modelo](service-fabric-cluster-creation-via-arm.md)de Gestor de Recursos Azure .

Para aprender a configurar a segurança do certificado num cluster para um cluster de Servidor Windows autónomo, consulte [O Seguro um cluster autónomo no Windows utilizando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Windows de nó ao nó

Para aprender a configurar a segurança do Windows para um cluster de Servidor Windows autónomo, consulte [o Secure um cluster autónomo no Windows utilizando](service-fabric-windows-cluster-windows-security.md)a segurança do Windows .

## <a name="client-to-node-security"></a>Segurança cliente-a-nó

A segurança cliente-a-nó autentica clientes e ajuda a garantir a comunicação entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados podem aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados de forma única através das suas credenciais de segurança do Windows ou das suas credenciais de segurança de certificados.

![Diagrama da comunicação cliente-a-nó][Client-to-Node]

Os clusters em funcionamento em Azure e clusters autónomos em execução no Windows ambos podem usar [a segurança](https://msdn.microsoft.com/library/ff649801.aspx) do certificado ou [do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança de certificado cliente-a-nó

Configurar a segurança do certificado cliente-a-nó quando criar o cluster, quer no portal Azure, utilizando um modelo de Gestor de Recursos, quer utilizando um modelo JSON autónomo. Para criar o certificado, especifique um certificado de cliente administrativo ou um certificado de cliente de utilizador. Como uma boa prática, os certificados de cliente de administrador e cliente de utilizador que especifica devem ser diferentes dos certificados primários e secundários que especifica para [a segurança nó-ao-nó](#node-to-node-security). Os certificados de cluster têm os mesmos direitos que os certificados de administração do cliente. No entanto, devem ser utilizados apenas por cluster e não por utilizadores administrativos como uma boa prática de segurança.

Os clientes que se ligam ao cluster utilizando o certificado de administração têm acesso total às capacidades de gestão. Os clientes que se ligam ao cluster utilizando o certificado de cliente apenas para leitura só leram o acesso às capacidades de gestão. Estes certificados são utilizados para o RBAC que é descrito mais tarde neste artigo.

Para aprender a configurar a segurança do certificado num cluster para o Azure, consulte [a configuração de um cluster utilizando um modelo](service-fabric-cluster-creation-via-arm.md)de Gestor de Recursos Azure .

Para aprender a configurar a segurança do certificado num cluster para um cluster de Servidor Windows autónomo, consulte [O Seguro um cluster autónomo no Windows utilizando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança do Diretório Ativo Do Cliente-a-Nó Azure no Azure

A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso dos utilizadores às aplicações. As aplicações dividem-se em pessoas com uma UI baseada na web e aquelas com experiência de cliente nativo. Se ainda não criou um inquilino, comece por ler [Como obter um inquilino azure Ative Diretório.][active-directory-howto-tenant]

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o Visual [Studio.][service-fabric-manage-application-in-visual-studio] Como resultado, cria duas aplicações Azure AD para controlar o acesso ao cluster, uma aplicação web e uma aplicação nativa.

Para clusters em funcionamento no Azure, também pode garantir o acesso aos pontos finais de gestão utilizando o Azure Ative Directory (Azure AD). Para aprender a criar os artefactos AD Azure necessários e como povoá-los quando criar o cluster, consulte [AD Azure para autenticar clientes.](service-fabric-cluster-creation-setup-aad.md)

## <a name="security-recommendations"></a>Recomendações de segurança

Para os clusters service Fabric implantados numa rede pública alojada no Azure, a recomendação para a autenticação mútua cliente-a-nó é:

* Utilizar o Diretório Ativo Azure para a identidade do cliente
* Um certificado para identidade do servidor e encriptação TLS de comunicação http

Para os clusters service Fabric implantados numa rede pública alojada no Azure, a recomendação para a segurança nó-ao-nó é utilizar um certificado cluster para autenticar nós.

Para clusters autónomos do Windows Server, se tiver o Windows Server 2012 R2 e o Windows Ative Diretório, recomendamos que utilize a segurança do Windows com contas de serviço geridas pelo grupo. Caso contrário, utilize a segurança do Windows com contas Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

Pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. São suportados dois tipos de controlo de acesso para clientes que se ligam a um cluster: função de administrador e função de utilizador.

Os utilizadores a quem é atribuída a função De Administrador têm acesso total às capacidades de gestão, incluindo capacidades de leitura e escrita. Os utilizadores a quem é atribuída a função Utilizador, por padrão, só leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta). Também podem resolver aplicações e serviços.

Detete as funções de Cliente do Administrador e do Utilizador quando criar o cluster. Atribuir funções fornecendo identidades separadas (por exemplo, utilizando certificados ou AD Azure) para cada tipo de função. Para obter mais informações sobre as definições de controlo de acesso predefinidos e como alterar as definições predefinidas, consulte o [Controlo de Acesso baseado em Funções para clientes de Tecido de Serviço](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509 certificados e tecido de serviço

Os certificados digitais X.509 são geralmente usados para autenticar clientes e servidores. Também são usados para encriptar e assinar mensagens digitalmente. Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação. Para obter mais informações sobre os certificados digitais X.509, consulte [Trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). Usa [o Key Vault](../key-vault/general/overview.md) para gerir certificados para clusters de tecido de serviço em Azure.

Algumas coisas importantes a considerar:

* Para criar certificados para clusters que estejam a executar cargas de trabalho de produção, utilize um serviço de certificado sulista Do Windows Server corretamente configurado ou um de uma autoridade de certificados aprovada [(CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nunca utilize certificados temporários ou de teste que crie utilizando ferramentas como MakeCert.exe num ambiente de produção.
* Pode usar um certificado auto-assinado, mas apenas num agrupamento de testes. Não utilize um certificado auto-assinado em produção.
* Ao gerar a impressão digital do certificado, certifique-se de gerar uma impressão digital DE polegar SHA1. SHA1 é o que é usado para configurar as impressões digitais do certificado cliente e cluster.

### <a name="cluster-and-server-certificate-required"></a>Cluster e certificado de servidor (obrigatório)

Estes certificados (um primário e opcionalmente secundário) são necessários para assegurar um cluster e impedir o acesso não autorizado ao mesmo. Estes certificados fornecem a autenticação do cluster e do servidor.

A autenticação do cluster autentica a comunicação nó-ao-nó para a federação de clusters. Só nós que possam provar a sua identidade com este certificado podem aderir ao cluster. A autenticação do servidor autentica os pontos finais da gestão do cluster a um cliente de gestão, para que o cliente de gestão saiba que está a falar com o cluster real e não com um "homem no meio". Este certificado também fornece um TLS para a API de gestão HTTPS e para service Fabric Explorer em HTTPS. Quando um cliente ou nó autentica um nó, uma das verificações iniciais é o valor do nome comum no campo **Assunto.** Ou este nome comum ou um dos nomes alternativos sujeitos dos certificados (SANs) devem estar presentes na lista de nomes comuns permitidos.

O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada. Estes certificados normalmente têm extensões .pfx ou .pem  
* O certificado deve ser criado para troca de chaves, que é exportável para um ficheiro de Intercâmbio de Informações Pessoais (.pfx).
* O **nome do certificado deve corresponder ao domínio que utiliza para aceder ao cluster Service Fabric**. Esta correspondência é necessária para fornecer um TLS para o ponto final de gestão HTTPS do cluster e explorador de tecido de serviço. Não é possível obter um certificado TLS/SSL de uma autoridade de certificados (CA) para o domínio de *.cloudapp.azure.com. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Algumas outras coisas a considerar:

* O campo **Assunto** pode ter vários valores. Cada valor é pré-fixado com uma inicialização para indicar o tipo de valor. Normalmente, a inicialização é **CN** (para *nome comum);* por exemplo, **CN\.= www contoso.com**.
* O campo **do Assunto** pode estar em branco.
* Se o campo de **nome** alternativo sujeito opcional for povoado, deve ter o nome comum do certificado e uma entrada por SAN. Estes são inseridos como valores **de nome DNS.** Para aprender a gerar certificados que tenham SANs, consulte [como adicionar um Nome Alternativo sujeito a um certificado LDAP seguro](https://support.microsoft.com/kb/931351).
* O valor do campo **de finalidades previstas** do certificado deve incluir um valor adequado, como **a autenticação** do servidor ou **a autenticação do cliente.**

### <a name="application-certificates-optional"></a>Certificados de candidatura (opcional)

Qualquer número de certificados adicionais pode ser instalado num cluster para efeitos de segurança da aplicação. Antes de criar o seu cluster, considere os cenários de segurança da aplicação que exigem a instalação de um certificado nos nós, tais como:

* Encriptação e desencriptação dos valores de configuração da aplicação.
* Encriptação de dados em nódosos durante a replicação.

O conceito de criação de clusters seguros é o mesmo, sejam eles linux ou clusters Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação do cliente (opcional)

Qualquer número de certificados adicionais pode ser especificado para operações de administração ou cliente do utilizador. O cliente pode utilizar este certificado quando for necessária autenticação mútua. Os certificados de cliente normalmente não são emitidos por um CA de terceiros. Em vez disso, a loja pessoal da localização atual do utilizador normalmente contém certificados de cliente colocados lá por uma autoridade de raiz. O certificado deve ter um valor **de Autenticação** do **Cliente.**  

Por padrão, o certificado de cluster tem privilégios de cliente administrativos. Estes certificados adicionais de cliente não devem ser instalados no cluster, mas são especificados como sendo permitidos na configuração do cluster.  No entanto, os certificados de cliente precisam de ser instalados nas máquinas do cliente para se conectarem ao cluster e realizarem quaisquer operações.

> [!NOTE]
> Todas as operações de gestão num cluster de Tecido de Serviço requerem certificados de servidor. Os certificados de cliente não podem ser utilizados para gestão.

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster em Azure usando um modelo de Gestor de Recursos](service-fabric-cluster-creation-via-arm.md)
* [Utilizar o portal do Azure para criar um cluster](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
