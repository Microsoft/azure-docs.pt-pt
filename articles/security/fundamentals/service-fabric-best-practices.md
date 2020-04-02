---
title: Boas práticas para segurança azure service tecido
description: Este artigo fornece um conjunto de boas práticas para a segurança do Tecido de Serviço Azure.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: befe8945468d220a04ec7f0b515f22159cb72b0f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549236"
---
# <a name="azure-service-fabric-security-best-practices"></a>Melhores práticas de segurança do Service Fabric
A implementação de uma aplicação no Azure é rápida, fácil e rentável. Antes de implementar a sua aplicação na nuvem em produção, reveja a nossa lista de boas práticas essenciais e recomendadas para implementar clusters seguros na sua aplicação.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

Para cada melhor prática, explicamos:

-   Qual é a melhor prática.
-   Por que deve implementar as melhores práticas.
-   O que pode acontecer se não implementar as melhores práticas.
-   Como pode aprender a implementar as melhores práticas.

Recomendamos as seguintes boas práticas de segurança do Tecido de Serviço Azure:

-   Utilize os modelos do Gestor de Recursos Azure e o módulo PowerShell de tecido de serviço para criar clusters seguros.
-   Utilize certificados X.509.
-   Configure as políticas de segurança.
-   Implementar a configuração de segurança de Atores Fiáveis.
-   Configure TLS para tecido de serviço Azure.
-   Utilize o isolamento da rede e a segurança com o Tecido de Serviço Azure.
-   Configure o Cofre chave Azure para segurança.
-   Atribuir utilizadores a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Boas práticas para garantir os seus clusters

Utilize sempre um cluster seguro:
-   Implementar a segurança do cluster utilizando certificados.
-   Fornecer acesso ao cliente (administrador e apenas leitura) utilizando o Azure Ative Directory (Azure AD).

Utilize implementações automatizadas:
-   Use scripts para gerar, implantar e reverter os segredos.
-   Guarde os segredos no Azure Key Vault e utilize o Azure AD para todos os outros acessos ao cliente.
-   Exigir autenticação para o acesso humano aos segredos.

Além disso, considere as seguintes opções de configuração:
-   Criar redes de perímetro (também conhecidas como zonas desmilitarizadas, DMZs e subredes selecionadas) utilizando grupos de segurança de rede Azure (NSGs).
-   Aceda a máquinas virtuais do cluster (VMs) ou gere o seu cluster utilizando servidores de salto com ligação remota de ambiente de trabalho.

Os seus clusters devem ser protegidos para evitar que utilizadores não autorizados se conectem, especialmente quando um cluster está em funcionamento em produção. Embora seja possível criar um cluster não seguro, os utilizadores anónimos podem ligar-se ao seu cluster se o cluster expor pontos finais de gestão à internet pública.

Existem três [cenários](../../service-fabric/service-fabric-cluster-security.md) para implementar a segurança do cluster utilizando várias tecnologias:

-   Segurança nó-ao-nó: Este cenário assegura a comunicação entre os VMs e os computadores do cluster. Esta forma de segurança garante que apenas os computadores autorizados a aderir ao cluster podem acolher aplicações e serviços no cluster.
Neste cenário, os clusters que funcionam no Azure, ou clusters autónomos que funcionam no Windows, podem utilizar a segurança do [certificado](../../service-fabric/service-fabric-windows-cluster-x509-security.md) ou a [segurança do Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) para máquinas do Windows Server.
-   Segurança cliente-a-nó: Este cenário assegura a comunicação entre um cliente de Tecido de Serviço e os nós individuais no cluster.
-   Controlo de Acesso baseado em funções (RBAC): Este cenário utiliza identidades separadas (certificados, Azure AD, e assim por diante) para cada administrador e função de cliente do utilizador que acede ao cluster. Especifica as identidades de papel quando cria o cluster.

>[!NOTE]
>**Recomendação de segurança para clusters Azure:** Utilize a segurança da AD Azure para autenticar clientes e certificados para segurança nó-a-nó.

Para configurar um cluster windows autónomo, consulte [as definições de Configurar para um cluster Windows autónomo](../../service-fabric/service-fabric-cluster-manifest.md).

Utilize os modelos do Gestor de Recursos Azure e o módulo PowerShell de tecido de serviço para criar um cluster seguro.
Para instruções passo a passo para criar um cluster de tecido de serviço seguro utilizando modelos de Gestor de Recursos Azure, consulte [criar um cluster de tecido](../../service-fabric/service-fabric-cluster-creation-via-arm.md)de serviço .

Utilize o modelo de Gestor de Recursos Azure:
-   Personalize o seu cluster utilizando o modelo para configurar o armazenamento gerido para discos rígidos virtuais VM (VHDs).
-   Conduza alterações no seu grupo de recursos utilizando o modelo para uma fácil gestão de configuração e auditoria.

Trate a configuração do seu cluster como código:
-   Seja minuciosa ao verificar as configurações de implementação.
-   Evite utilizar comandos implícitos para modificar diretamente os seus recursos.

Muitos aspetos do ciclo de vida da [aplicação Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) podem ser automatizados. O [módulo PowerShell](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) de tecido de serviço automatiza tarefas comuns para a implementação, modernização, remoção e teste de aplicações azure service fabric. Estão também disponíveis APIs geridos e APIs http para gestão de aplicações.

## <a name="use-x509-certificates"></a>Utilize certificados X.509
Proteja sempre os seus clusters utilizando certificados X.509 ou segurança do Windows. A segurança só está configurada no momento da criação do cluster. Não é possível ligar a segurança depois do aglomerado ser criado.

Para especificar um certificado de [cluster,](../../service-fabric/service-fabric-windows-cluster-x509-security.md)dejuste o valor da propriedade **ClusterCredentialType** para X509. Para especificar um certificado de servidor para ligações externas, detete a propriedade **ServerCredentialType** para X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção utilizando um serviço de certificados Windows Server corretamente configurado. Pode também obter os certificados de uma autoridade de certificados aprovada (CA).
-   Nunca utilize um certificado temporário ou de teste para aglomerados de produção se o certificado tiver sido criado utilizando o MakeCert.exe ou uma ferramenta semelhante.
-   Utilize um certificado auto-assinado para clusters de ensaio, mas não para aglomerados de produção.

Se o cluster não for seguro, qualquer pessoa pode ligar-se ao cluster de forma anónima e realizar operações de gestão. Por esta razão, proteja sempre os clusters de produção utilizando certificados X.509 ou segurança do Windows.

Para saber mais sobre a utilização de certificados X.509, consulte [Adicionar ou remover certificados para um cluster de Tecido de Serviço](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
O Service Fabric também assegura os recursos que são utilizados pelas aplicações. Recursos como ficheiros, diretórios e certificados são armazenados nas contas do utilizador quando a aplicação é implementada. Esta funcionalidade torna as aplicações de execução mais seguras umas das outras, mesmo num ambiente partilhado.

-   Utilize um grupo ou utilizador de domínio de Diretório Ativo: Executar o serviço sob as credenciais de um utilizador ou conta de grupo do Diretório Ativo. Certifique-se de que utiliza o Ative Directory no local dentro do seu domínio e não o Diretório Ativo Azure. Aceda a outros recursos no domínio que tenham sido autorizados através de um utilizador ou grupo de domínio. Por exemplo, recursos como ações de ficheiros.

-   Atribuir uma política de acesso à segurança para os pontos finais HTTP e HTTPS: Especifique a propriedade **SecurityAccessPolicy** para aplicar uma política **de RunAs** a um serviço quando o manifesto de serviço declarar recursos de ponto final com HTTP. As portas atribuídas aos pontos finais http são listas corretamente controladas pelo acesso para a conta de utilizador RunAs que o serviço é executado. Quando a apólice não está definida, http.sys não tem acesso ao serviço e você pode obter falhas com chamadas do cliente.

Para aprender a usar as políticas de segurança num cluster de Tecido de Serviço, consulte as políticas de [segurança da Configuração para](../../service-fabric/service-fabric-application-runas-security.md)a sua aplicação .

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança de Atores Fiáveis
Service Fabric Reliable Actors é uma implementação do padrão de design de ator. Como em qualquer padrão de design de software, a decisão de usar um padrão específico baseia-se em se um problema de software se encaixa no padrão.

Em geral, utilize o padrão de design do ator para ajudar a modelar soluções para os seguintes problemas de software ou cenários de segurança:
-   O seu espaço problemático envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e lógica.
-   Estás a trabalhar com objetos de roscar que não requerem interação significativa a partir de componentes externos, incluindo o estado de consulta através de um conjunto de atores.
-   Os seus casos de ator não bloqueiam chamadas com atrasos imprevisíveis ao emitir operações de I/O.

No Service Fabric, os atores são implementados no quadro de aplicação de Atores Fiáveis. Esta estrutura baseia-se no padrão do ator e construída em cima de [Serviços Fiáveis](../../service-fabric/service-fabric-reliable-services-introduction.md)de Tecidos. Cada serviço de ator fiável que escreve é um serviço de confiança estatal dividido.

Cada ator é definido como um exemplo de um tipo de ator, idêntico à forma como um objeto .NET é um exemplo de um tipo .NET. Por exemplo, um tipo de **ator** que implementa a funcionalidade de uma calculadora pode ter muitos atores desse tipo que são distribuídos em vários nós através de um cluster. Cada um dos atores distribuídos é caracterizado exclusivamente por um identificador de ator.

[As configurações](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) de segurança do replicador são usadas para fixar o canal de comunicação que é utilizado durante a replicação. Esta configuração impede que os serviços vejam o tráfego de replicação uns dos outros e garante que os dados altamente disponíveis são seguros. Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.
As configurações do replicador configuram o replicador que é responsável por tornar o Estado fornecedor de ator altamente fiável.

## <a name="configure-tls-for-azure-service-fabric"></a>Configure TLS para tecido de serviço Azure
O processo de autenticação do servidor [autentica](../../service-fabric/service-fabric-cluster-creation-via-arm.md) os pontos finais da gestão do cluster a um cliente de gestão. O cliente de gestão reconhece então que está a falar com o verdadeiro cluster. Este certificado também fornece um [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) para a API de gestão HTTPS e para service Fabric Explorer em HTTPS.
Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a uma autoridade de certificado, o nome do certificado deve corresponder ao nome de domínio personalizado que utiliza para o seu cluster.

Para configurar o TLS para uma aplicação, é necessário obter primeiro um certificado SSL/TLS que tenha sido assinado por um CA. A AC é um terceiro de confiança que emite certificados para fins de segurança TLS. Se ainda não tem um certificado SSL/TLS, precisa de obter um de uma empresa que venda certificados SSL/TLS.

O certificado deve satisfazer os seguintes requisitos relativos aos certificados SSL/TLS em Azure:
-   O certificado deve conter uma chave privada.

-   O certificado deve ser criado para troca de chaves e ser exportável para um ficheiro de intercâmbio de informações pessoais (.pfx).

-   O nome do certificado deve corresponder ao nome de domínio utilizado para aceder ao seu serviço na nuvem.

    - Adquira um nome de domínio personalizado para aceder ao seu serviço na nuvem.
    - Solicite um certificado de uma AC com um nome de assunto que corresponda ao nome de domínio personalizado do seu serviço. Por exemplo, se o seu nome de domínio personalizado for**contoso.com,** o certificado da sua CA deve ter o nome de assunto **.contoso.com** ou __contoso__**www.contoso.com**. __www__

    >[!NOTE]
    >Não é possível obter um certificado SSL/TLS de um CA para o domínio __cloudapp__**.net.**

-   O certificado deve usar um mínimo de encriptação de 2.048 bits.

O protocolo HTTP é inseguro e está sujeito a ataques de escuta. Os dados que são transmitidos através do HTTP são enviados como texto simples do navegador web para o servidor web ou entre outros pontos finais. Os atacantes podem intercetar e visualizar dados sensíveis que são enviados via HTTP, tais como dados do cartão de crédito e logins de conta. Quando os dados são enviados ou publicados através de um browser via HTTPS, o SSL garante que as informações sensíveis são encriptadas e seguras de interceção.

Para saber mais sobre a utilização de certificados SSL/TLS, consulte [Configurar TLS para uma aplicação no Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Utilize o isolamento e a segurança da rede com o Tecido de Serviço Azure
Configurar um cluster seguro de 3 nódétipo utilizando o modelo do Gestor de [Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) como amostra. Controle o tráfego de rede de entrada e saída utilizando o modelo e os grupos de segurança da rede.

O modelo tem um NSG para cada um dos conjuntos de escala de máquina virtual e é usado para controlar o tráfego dentro e fora do conjunto. As regras são configuradas por padrão para permitir todo o tráfego necessário para os serviços do sistema e as portas de aplicação especificadas no modelo. Reveja estas regras e efaça quaisquer alterações para se adaptar às suas necessidades, incluindo a adição de novas regras para as suas aplicações.

Para mais informações, consulte [cenários comuns de networking para tecido](../../service-fabric/service-fabric-patterns-networking.md)de serviço Azure .

## <a name="set-up-azure-key-vault-for-security"></a>Configurar o Cofre chave Azure para segurança
Service Fabric utiliza certificados para fornecer autenticação e encriptação para garantir um cluster e suas aplicações.

Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação. Usa o Cofre chave Azure para [gerir certificados](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) para clusters de tecido de serviço em Azure. O fornecedor de recursos Azure que cria os clusters retira os certificados de um cofre chave. O fornecedor instala então os certificados nos VMs quando o cluster é implantado no Azure.

Existe uma relação de certificado entre o [Azure Key Vault,](../../key-vault/key-vault-secure-your-key-vault.md)o cluster Service Fabric, e o fornecedor de recursos que utiliza os certificados. Quando o cluster é criado, a informação sobre a relação de certificado é armazenada num cofre chave.

Há dois passos básicos para montar um cofre chave:
1. Crie um grupo de recursos especificamente para o seu cofre chave.

    Recomendamos que coloque o cofre chave no seu próprio grupo de recursos. Esta ação ajuda a prevenir a perda das suas chaves e segredos se outros grupos de recursos forem removidos, tais como armazenamento, computação ou o grupo que contém o seu cluster. O grupo de recursos que contém o seu cofre chave deve estar na mesma região que o cluster que o está a usar.

2. Crie um cofre chave no novo grupo de recursos.

    O cofre da chave deve ser ativado para a implantação. O fornecedor de recursos computacionais pode então obter os certificados do cofre e instalá-los nas instâncias VM.

Para saber mais sobre como montar um cofre chave, veja [o que é o Cofre chave Azure?](../../key-vault/key-vault-overview.md)

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções
Depois de ter criado as aplicações para representar o seu cluster, atribua aos seus utilizadores as funções suportadas pelo Service Fabric: apenas leitura e administrador. Pode atribuir estas funções utilizando o portal Azure.

>[!NOTE]
> Para obter mais informações sobre a utilização de funções em Tecido de Serviço, consulte [o Controlo de Acesso baseado em Funções para clientes de Tecido de Serviço](../../service-fabric/service-fabric-cluster-security-roles.md).

O Azure Service Fabric suporta dois tipos de controlo de acesso para clientes ligados a um [cluster de Tecido de Serviço](../../service-fabric/service-fabric-cluster-creation-via-arm.md): administrador e utilizador. O administrador do cluster pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. O controlo de acesso torna o cluster mais seguro.

## <a name="next-steps"></a>Passos seguintes

- [Lista de verificação de segurança de tecido de serviço](service-fabric-checklist.md)
- Instale o seu ambiente de [desenvolvimento](../../service-fabric/service-fabric-get-started.md)de Tecido de Serviço.
- Saiba mais sobre as opções de suporte do [Tecido de Serviço.](../../service-fabric/service-fabric-support.md)
