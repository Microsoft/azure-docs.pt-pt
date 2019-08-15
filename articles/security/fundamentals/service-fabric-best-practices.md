---
title: Práticas recomendadas de segurança do Azure Service Fabric | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas para o Azure Service Fabric Security.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: dc063621e6b3e1d0d3e1a51d744ca9d9a6ef8c8d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934631"
---
# <a name="azure-service-fabric-security-best-practices"></a>Melhores práticas de segurança do Service Fabric
Implantar um aplicativo no Azure é rápido, fácil e econômico. Antes de implantar seu aplicativo de nuvem em produção, examine nossa lista de práticas recomendadas essenciais e recomendadas para implementar clusters seguros em seu aplicativo.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

Para cada prática recomendada, explicamos:

-   Qual é a prática recomendada.
-   Por que você deve implementar a prática recomendada.
-   O que pode acontecer se você não implementar a prática recomendada.
-   Como você pode aprender a implementar a prática recomendada.

Recomendamos as seguintes práticas recomendadas de segurança do Azure Service Fabric:

-   Use modelos de Azure Resource Manager e o módulo Service Fabric PowerShell para criar clusters seguros.
-   Use certificados X. 509.
-   Configurar políticas de segurança.
-   Implemente a configuração de segurança Reliable Actors.
-   Configure o SSL para o Service Fabric do Azure.
-   Use o isolamento de rede e segurança com o Azure Service Fabric.
-   Configure Azure Key Vault para segurança.
-   Atribuir usuários a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Práticas recomendadas para proteger seus clusters

Sempre usar um cluster seguro:
-   Implementar a segurança do cluster usando certificados.
-   Fornecer acesso de cliente (administrador e somente leitura) usando Azure Active Directory (Azure AD).

Usar implantações automatizadas:
-   Use scripts para gerar, implantar e sobrepor os segredos.
-   Armazene os segredos em Azure Key Vault e use o Azure AD para todos os outros acessos de cliente.
-   Exigir autenticação para acesso humano aos segredos.

Além disso, considere as seguintes opções de configuração:
-   Crie redes de perímetro (também conhecidas como zonas desmilitarizadas, DMZs e sub-redes filtradas) usando os NSGs (grupos de segurança de rede) do Azure.
-   Acesse máquinas virtuais de cluster (VMs) ou Gerencie seu cluster usando servidores de salto com Conexão de Área de Trabalho Remota.

Seus clusters devem ser protegidos para impedir que usuários não autorizados se conectem, especialmente quando um cluster está sendo executado em produção. Embora seja possível criar um cluster não seguro, os usuários anônimos poderão se conectar ao cluster se o cluster expuser pontos de extremidade de gerenciamento para a Internet pública.

Há três [cenários](../../service-fabric/service-fabric-cluster-security.md) para implementar a segurança de cluster usando várias tecnologias:

-   Segurança de nó para nó: Esse cenário protege a comunicação entre as VMs e os computadores no cluster. Essa forma de segurança garante que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.
Nesse cenário, os clusters que são executados no Azure ou clusters autônomos executados no Windows podem usar a segurança do [certificado](../../service-fabric/service-fabric-windows-cluster-x509-security.md) ou a [segurança do Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) para computadores Windows Server.
-   Segurança de cliente para nó: Esse cenário protege a comunicação entre um cliente Service Fabric e os nós individuais no cluster.
-   RBAC (controle de acesso baseado em função): Esse cenário usa identidades separadas (certificados, Azure AD e assim por diante) para cada função de cliente de administrador e usuário que acessa o cluster. Você especifica as identidades de função ao criar o cluster.

>[!NOTE]
>**Recomendação de segurança para clusters do Azure:** Use a segurança do Azure AD para autenticar clientes e certificados para a segurança de nó para nó.

Para configurar um cluster autônomo do Windows, consulte [definir configurações para um cluster autônomo do Windows](../../service-fabric/service-fabric-cluster-manifest.md).

Use modelos de Azure Resource Manager e o módulo Service Fabric PowerShell para criar um cluster seguro.
Para obter instruções detalhadas sobre como criar um cluster de Service Fabric seguro usando modelos de Azure Resource Manager, consulte [criando um cluster de Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Use o modelo de Azure Resource Manager:
-   Personalize seu cluster usando o modelo para configurar o armazenamento gerenciado para VHDs (discos rígidos virtuais) da VM.
-   Conduza alterações ao seu grupo de recursos usando o modelo para facilitar o gerenciamento de configuração e a auditoria.

Trate sua configuração de cluster como código:
-   Seja completo ao verificar suas configurações de implantação.
-   Evite usar comandos implícitos para modificar seus recursos diretamente.

Muitos aspectos do [ciclo de vida do aplicativo Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) podem ser automatizados. O [módulo Service Fabric PowerShell](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiza tarefas comuns para implantar, atualizar, remover e testar aplicativos do Azure Service Fabric. APIs gerenciadas e APIs HTTP para gerenciamento de aplicativos também estão disponíveis.

## <a name="use-x509-certificates"></a>Usar certificados X. 509
Sempre proteja seus clusters usando certificados X. 509 ou a segurança do Windows. A segurança só é configurada no momento da criação do cluster. Não é possível ativar a segurança após a criação do cluster.

Para especificar um [certificado de cluster](../../service-fabric/service-fabric-windows-cluster-x509-security.md), defina o valor da propriedade **ClusterCredentialType** como X509. Para especificar um certificado de servidor para conexões externas, defina a propriedade **ServerCredentialType** como X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção usando um serviço de certificado do Windows Server configurado corretamente. Você também pode obter os certificados de uma AC (autoridade de certificação) aprovada.
-   Nunca use um certificado temporário ou de teste para clusters de produção se o certificado tiver sido criado usando MakeCert. exe ou uma ferramenta semelhante.
-   Use um certificado autoassinado para clusters de teste, mas não para clusters de produção.

Se o cluster não for seguro, qualquer pessoa poderá se conectar ao cluster anonimamente e executar operações de gerenciamento. Por esse motivo, sempre proteja os clusters de produção usando certificados X. 509 ou a segurança do Windows.

Para saber mais sobre como usar certificados X. 509, consulte [Adicionar ou remover certificados para um cluster Service Fabric](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
O Service Fabric também protege os recursos que são usados por aplicativos. Recursos como arquivos, diretórios e certificados são armazenados nas contas de usuário quando o aplicativo é implantado. Esse recurso torna os aplicativos em execução mais seguros uns dos outros, mesmo em um ambiente hospedado compartilhado.

-   Usar um Active Directory grupo de domínio ou usuário: Execute o serviço sob as credenciais para uma conta de usuário ou grupo Active Directory. Certifique-se de usar Active Directory local em seu domínio e não Azure Active Directory. Acesse outros recursos no domínio que receberam permissões usando um usuário ou grupo de domínio. Por exemplo, recursos como compartilhamentos de arquivos.

-   Atribuir uma política de acesso de segurança para pontos de extremidade HTTP e HTTPS: Especifique a propriedade **SecurityAccessPolicy** para aplicar uma política **runas** a um serviço quando o manifesto do serviço declarar recursos de ponto de extremidade com http. As portas alocadas aos pontos de extremidade HTTP são listas controladas com acesso correto para a conta de usuário RunAs em que o serviço é executado. Quando a política não está definida, o http. sys não tem acesso ao serviço e você pode obter falhas com chamadas do cliente.

Para saber como usar as políticas de segurança em um Cluster Service Fabric, consulte [Configurar políticas de segurança para seu aplicativo](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança de Reliable Actors
Service Fabric Reliable Actors é uma implementação do padrão de design de ator. Assim como acontece com qualquer padrão de design de software, a decisão de usar um padrão específico se baseia em se um problema de software se encaixa no padrão.

Em geral, use o padrão de design de ator para ajudar a modelar soluções para os seguintes problemas de software ou cenários de segurança:
-   O espaço do problema envolve um grande número (milhares ou mais) de unidades pequenas, independentes e isoladas de estado e lógica.
-   Você está trabalhando com objetos de thread único que não exigem interação significativa de componentes externos, incluindo o estado de consulta em um conjunto de atores.
-   Suas instâncias de ator não bloqueiam os chamadores com atrasos imprevisíveis emitindo operações de e/s.

No Service Fabric, os atores são implementados na estrutura do aplicativo Reliable Actors. Essa estrutura se baseia no padrão de ator e foi criada sobre o [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Cada serviço de ator confiável que você escreve é um serviço confiável com estado particionado.

Cada ator é definido como uma instância de um tipo de ator, idêntico ao modo como um objeto .NET é uma instância de um tipo .NET. Por exemplo, um **tipo de ator** que implementa a funcionalidade de uma calculadora pode ter muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada um dos atores distribuídos é caracterizado exclusivamente por um identificador de ator.

[As configurações de segurança](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) do replicador são usadas para proteger o canal de comunicação usado durante a replicação. Essa configuração impede que os serviços vejam o tráfego de replicação uns dos outros e garantem que dados altamente disponíveis sejam seguros. Por padrão, uma seção de configuração de segurança vazia impede a segurança da replicação.
As configurações do replicador configuram o replicador que é responsável por tornar o estado do provedor de estado do ator altamente confiável.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurar o SSL para o Azure Service Fabric
O processo de autenticação do servidor [autentica](../../service-fabric/service-fabric-cluster-creation-via-arm.md) os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento. Em seguida, o cliente de gerenciamento reconhece que está se comunicando com o cluster real. Esse certificado também fornece um [SSL](../../service-fabric/service-fabric-cluster-creation-via-arm.md) para a API de gerenciamento de HTTPS e para Service Fabric Explorer por HTTPS.
Tem de obter um nome de domínio personalizado para o cluster. Quando você solicita um certificado de uma autoridade de certificação, o nome da entidade do certificado deve corresponder ao nome de domínio personalizado que você usa para o cluster.

Para configurar o SSL para um aplicativo, primeiro você precisa obter um certificado SSL que tenha sido assinado por uma autoridade de certificação. A CA é uma terceira parte confiável que emite certificados para fins de segurança SSL. Se você ainda não tiver um certificado SSL, precisará obter um de uma empresa que vende certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:
-   O certificado deve conter uma chave privada.

-   O certificado deve ser criado para troca de chaves e ser exportável para um arquivo de troca de informações pessoais (. pfx).

-   O nome da entidade do certificado deve corresponder ao nome de domínio usado para acessar seu serviço de nuvem.

    - Adquira um nome de domínio personalizado a ser usado para acessar seu serviço de nuvem.
    - Solicite um certificado de uma AC com um nome de entidade que corresponda ao nome de domínio personalizado do seu serviço. Por exemplo, se o nome de domínio personalizado for __contoso__ **. com**, o certificado de sua autoridade de certificação deverá ter o nome da entidade **. contoso.com** ou __www__ **. contoso.com**.

    >[!NOTE]
    >Você não pode obter um certificado SSL de uma autoridade de certificação para o domínio do __cloudapp__ **.net** .

-   O certificado deve usar um mínimo de criptografia de 2.048 bits.

O protocolo HTTP não é seguro e está sujeito a ataques de interceptação. Os dados transmitidos por HTTP são enviados como texto sem formatação do navegador da Web para o servidor Web ou entre outros pontos de extremidade. Os invasores podem interceptar e exibir dados confidenciais enviados por meio de HTTP, como detalhes de cartão de crédito e logons de conta. Quando os dados são enviados ou postados por meio de um navegador via HTTPS, o SSL garante que as informações confidenciais sejam criptografadas e protegidas contra interceptação.

Para saber mais sobre como usar certificados SSL, confira [Configurar SSL para aplicativos do Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Usar o isolamento de rede e segurança com o Azure Service Fabric
Configure um cluster seguro de 3 NodeType usando o [modelo de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) como um exemplo. Controle o tráfego de rede de entrada e saída usando o modelo e os grupos de segurança de rede.

O modelo tem um NSG para cada um dos conjuntos de dimensionamento de máquinas virtuais e é usado para controlar o tráfego dentro e fora do conjunto. As regras são configuradas por padrão para permitir todo o tráfego necessário para os serviços do sistema e as portas de aplicativo especificadas no modelo. Examine essas regras e faça as alterações adequadas às suas necessidades, incluindo a adição de novas regras para seus aplicativos.

Para obter mais informações, consulte [Common Networking Scenarios for Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Configurar Azure Key Vault para segurança
Service Fabric usa certificados para fornecer autenticação e criptografia para proteger um cluster e seus aplicativos.

O Service Fabric usa certificados X. 509 para proteger um cluster e fornecer recursos de segurança de aplicativo. Você usa Azure Key Vault para [gerenciar certificados](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) para Service Fabric clusters no Azure. O provedor de recursos do Azure que cria os clusters extrai os certificados de um cofre de chaves. Em seguida, o provedor instala os certificados nas VMs quando o cluster é implantado no Azure.

Existe uma relação de certificado entre [Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md), o cluster de Service Fabric e o provedor de recursos que usa os certificados. Quando o cluster é criado, as informações sobre a relação de certificado são armazenadas em um cofre de chaves.

Há duas etapas básicas para configurar um cofre de chaves:
1. Crie um grupo de recursos especificamente para o cofre de chaves.

    Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação ajuda a evitar a perda de suas chaves e segredos se outros grupos de recursos forem removidos, como armazenamento, computação ou o grupo que contém o cluster. O grupo de recursos que contém o cofre de chaves deve estar na mesma região que o cluster que o está usando.

2. Crie um cofre de chaves no novo grupo de recursos.

    O cofre de chaves deve estar habilitado para implantação. O provedor de recursos de computação pode obter os certificados do cofre e instalá-los nas instâncias de VM.

Para saber mais sobre como configurar um cofre de chaves, confira [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md).

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções
Depois de criar os aplicativos para representar o cluster, atribua os usuários às funções com suporte pelo Service Fabric: somente leitura e administrador. Você pode atribuir essas funções usando o portal do Azure.

>[!NOTE]
> Para obter mais informações sobre como usar funções no Service Fabric, consulte [controle de acesso baseado em função para clientes Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md).

O Azure Service Fabric dá suporte a dois tipos de controle de acesso para clientes que estão conectados a um [cluster Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md): administrador e usuário. O administrador de cluster pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. O controle de acesso torna o cluster mais seguro.

## <a name="next-steps"></a>Passos seguintes

- [Lista de verificação de segurança Service Fabric](service-fabric-checklist.md)
- Configure seu ambiente de [desenvolvimento](../../service-fabric/service-fabric-get-started.md)de Service Fabric.
- Saiba mais sobre [as opções de suporte do Service Fabric](../../service-fabric/service-fabric-support.md).
