---
title: Melhores práticas para a segurança do tecido de serviço Azure
description: Este artigo fornece um conjunto de boas práticas para a segurança do Azure Service Fabric.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 93b25e65914ce603b4a969eda7fd7c048704e466
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410017"
---
# <a name="azure-service-fabric-security-best-practices"></a>Melhores práticas de segurança do Service Fabric
Implementar uma aplicação no Azure é rápido, fácil e rentável. Antes de colocar a sua aplicação em nuvem em produção, reveja a nossa lista de boas práticas essenciais e recomendadas para implementar clusters seguros na sua aplicação.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

Para cada boa prática, explicamos:

-   Qual é a melhor prática.
-   Por que deve implementar as melhores práticas?
-   O que pode acontecer se não implementares as melhores práticas.
-   Como pode aprender a implementar as melhores práticas.

Recomendamos as seguintes boas práticas de segurança da Azure Service Fabric:

-   Utilize os modelos do Gestor de Recursos Azure e o módulo PowerShell do tecido de serviço para criar clusters seguros.
-   Utilize certificados X.509.
-   Configurar políticas de segurança.
-   Implementar a configuração de segurança de Atores Fidedigso.
-   Configure TLS para tecido de serviço Azure.
-   Utilize isolamento de rede e segurança com o Azure Service Fabric.
-   Configure Azure Key Vault para segurança.
-   Atribua os utilizadores a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Melhores práticas para garantir os seus clusters

Utilize sempre um cluster seguro:
-   Implementar a segurança do cluster utilizando certificados.
-   Fornecer acesso ao cliente (administrador e apenas leitura) utilizando o Azure Ative Directory (Azure AD).

Utilizar implementações automatizadas:
-   Use scripts para gerar, implementar e rebolar os segredos.
-   Guarde os segredos no Azure Key Vault e use a Azure AD para todos os outros acessos ao cliente.
-   Requerem autenticação para acesso humano aos segredos.

Além disso, considere as seguintes opções de configuração:
-   Criar redes de perímetro (também conhecidas como zonas desmilitarizadas, DMZs e sub-redes selecionadas) utilizando grupos de segurança da rede Azure (NSGs).
-   Aceda a máquinas virtuais de cluster (VMs) ou gere o seu cluster utilizando servidores de salto com Conexão de Ambiente de Trabalho Remoto.

Os seus agrupamentos devem ser protegidos para evitar que os utilizadores não autorizados se conectem, especialmente quando um cluster está em funcionamento. Embora seja possível criar um cluster não seguro, os utilizadores anónimos podem ligar-se ao seu cluster se o cluster expor os pontos finais de gestão para a internet pública.

Existem três [cenários](../../service-fabric/service-fabric-cluster-security.md) para implementar a segurança do cluster utilizando várias tecnologias:

-   Segurança nó-a-nó: Este cenário assegura a comunicação entre os VMs e os computadores do cluster. Esta forma de segurança garante que apenas os computadores autorizados a aderir ao cluster podem acolher aplicações e serviços no cluster.
Neste cenário, os clusters que funcionam no Azure, ou clusters autónomos que funcionam no Windows, podem utilizar a segurança dos [certificados](../../service-fabric/service-fabric-windows-cluster-x509-security.md) ou [a segurança do Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) para máquinas do Windows Server.
-   Segurança cliente-a-nó: Este cenário assegura a comunicação entre um cliente de Service Fabric e os nós individuais no cluster.
-   Role-Based Controlo de Acesso (RBAC): Este cenário utiliza identidades separadas (certificados, AZure AD, e assim por diante) para cada administrador e papel do cliente utilizador que acede ao cluster. Especifica as identidades de função quando cria o cluster.

>[!NOTE]
>**Recomendação de segurança para os clusters Azure:** Utilize a segurança Azure AD para autenticar clientes e certificados para segurança nó-a-nó.

Para configurar um cluster Windows autónomo, consulte [as definições de Configuração para um cluster Windows autónomo](../../service-fabric/service-fabric-cluster-manifest.md).

Utilize os modelos do Gestor de Recursos Azure e o módulo PowerShell do tecido de serviço para criar um cluster seguro.
Para obter instruções passo a passo para criar um cluster de tecido de serviço seguro utilizando modelos de Gestor de Recursos Azure, consulte [criar um cluster de tecido de serviço](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Utilize o modelo Azure Resource Manager:
-   Personalize o seu cluster utilizando o modelo para configurar o armazenamento gerido para discos rígidos virtuais VM (VHDs).
-   Insupro as alterações ao seu grupo de recursos utilizando o modelo para uma gestão e auditoria fáceis de configuração.

Trate a sua configuração de cluster como código:
-   Seja minucioso ao verificar as configurações de implementação.
-   Evite utilizar comandos implícitos para modificar diretamente os seus recursos.

Muitos aspetos do ciclo de vida da [aplicação Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) podem ser automatizados. O [módulo PowerShell do tecido de serviço](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiza tarefas comuns para implantação, atualização, remoção e teste de aplicações do Tecido de Serviço Azure. Estão também disponíveis APIs geridos e APIs HTTP para gestão de aplicações.

## <a name="use-x509-certificates"></a>Utilizar certificados X.509
Proteja sempre os seus clusters utilizando certificados X.509 ou segurança do Windows. A segurança só está configurada no tempo de criação do cluster. Não é possível ligar a segurança depois do aglomerado ser criado.

Para especificar um [certificado de cluster,](../../service-fabric/service-fabric-windows-cluster-x509-security.md)desloque o valor da propriedade **ClusterCredentialType** para X509. Para especificar um certificado de servidor para ligações externas, desloque a propriedade **ServerCredentialType** para X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção utilizando um serviço de certificados do Windows Server corretamente configurado. Pode ainda obter os certificados de uma autoridade de certificados aprovada (CA).
-   Nunca utilize um certificado temporário ou de teste para agrupamentos de produção se o certificado tiver sido criado utilizando MakeCert.exe ou uma ferramenta semelhante.
-   Utilize um certificado auto-assinado para agrupamentos de teste, mas não para clusters de produção.

Se o cluster não for seguro, qualquer pessoa pode ligar-se ao cluster de forma anónima e realizar operações de gestão. Por esta razão, proteja sempre os clusters de produção utilizando certificados X.509 ou segurança do Windows.

Para saber mais sobre a utilização de certificados X.509, consulte [adicionar ou remover certificados para um cluster de Tecido de Serviço.](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)

## <a name="configure-security-policies"></a>Configure políticas de segurança
A Service Fabric também assegura os recursos que são utilizados pelas aplicações. Recursos como ficheiros, diretórios e certificados são armazenados nas contas do utilizador quando a aplicação é implementada. Esta funcionalidade torna as aplicações de execução mais seguras umas das outras, mesmo num ambiente partilhado.

-   Utilize um grupo de domínio de Diretório Ativo ou utilizador: Executar o serviço sob as credenciais para um utilizador do Ative Directory ou conta de grupo. Certifique-se de que utiliza o Ative Directory no seu domínio e não o Azure Ative Directory. Aceda a outros recursos no domínio que tenham sido autorizados através da utilização de um utilizador ou grupo de domínio. Por exemplo, recursos como ações de ficheiros.

-   Atribuir uma política de acesso à segurança para pontos finais HTTP e HTTPS: Especifique a propriedade **SecurityAccessPolicy** para aplicar uma política **RunAs** a um serviço quando o manifesto de serviço declara recursos de ponto final com HTTP. As portas atribuídas aos pontos finais HTTP são listas controladas corretamente para a conta de utilizador RunAs que o serviço executa. Quando a apólice não está definida, http.sys não tem acesso ao serviço e pode obter falhas com chamadas do cliente.

Para aprender a utilizar as políticas de segurança num cluster de Tecidos de Serviço, consulte [as políticas de segurança Configure para a sua aplicação.](../../service-fabric/service-fabric-application-runas-security.md)

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança de Atores Fiáveis
Service Fabric Reliable Actors é uma implementação do padrão de design do ator. Como em qualquer padrão de design de software, a decisão de usar um padrão específico baseia-se em se um problema de software se encaixa no padrão.

Em geral, utilize o padrão de design do ator para ajudar a modelar soluções para os seguintes problemas de software ou cenários de segurança:
-   O seu espaço problemático envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e lógica.
-   Estás a trabalhar com objetos de linha única que não requerem interação significativa de componentes externos, incluindo o estado de consulta através de um conjunto de atores.
-   Os seus casos de ator não bloqueiam chamadas com atrasos imprevisíveis ao emitir operações de E/S.

No Service Fabric, os atores são implementados no quadro de aplicação de Atores Fidedigtos. Esta estrutura baseia-se no padrão do ator e construída em cima dos [Serviços De Fiabilidade](../../service-fabric/service-fabric-reliable-services-introduction.md)do Tecido de Serviço. Cada serviço de ator confiável que escreve é um serviço de confiança estatal.

Cada ator é definido como um exemplo de um tipo de ator, idêntico à forma como um objeto .NET é um exemplo de um tipo .NET. Por exemplo, um tipo de **ator** que implementa a funcionalidade de uma calculadora pode ter muitos atores desse tipo que são distribuídos em vários nós através de um cluster. Cada um dos atores distribuídos é caracterizado exclusivamente por um identificador de ator.

[As configurações de segurança do replicador](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) são usadas para proteger o canal de comunicação que é utilizado durante a replicação. Esta configuração impede que os serviços vejam o tráfego de replicação uns dos outros e garante que os dados altamente disponíveis são seguros. Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.
As configurações do replicador configuram o replicador que é responsável por tornar o Estado do Ator State Provider altamente fiável.

## <a name="configure-tls-for-azure-service-fabric"></a>Configure TLS para tecido de serviço Azure
O processo de autenticação do servidor [autentica](../../service-fabric/service-fabric-cluster-creation-via-arm.md) os pontos finais de gestão do cluster a um cliente de gestão. O cliente da gerência reconhece então que está a falar com o verdadeiro cluster. Este certificado também fornece um [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) para a API de gestão HTTPS e para o Service Fabric Explorer em HTTPS.
Tem de obter um nome de domínio personalizado para o cluster. Quando solicita um certificado a uma autoridade de certificados, o nome do certificado deve corresponder ao nome de domínio personalizado que utiliza para o seu cluster.

Para configurar o TLS para uma aplicação, primeiro precisa de obter um certificado SSL/TLS assinado por um CA. A AC é um terceiro de confiança que emite certificados para fins de segurança TLS. Se ainda não tem um certificado SSL/TLS, precisa de obter um de uma empresa que venda certificados SSL/TLS.

O certificado deve satisfazer os seguintes requisitos relativos aos certificados SSL/TLS em Azure:
-   O certificado deve conter uma chave privada.

-   O certificado deve ser criado para troca de chaves e ser exportável para um ficheiro de troca de informações pessoais (.pfx).

-   O nome do certificado deve corresponder ao nome de domínio utilizado para aceder ao seu serviço na nuvem.

    - Adquira um nome de domínio personalizado para usar para aceder ao seu serviço na nuvem.
    - Solicite um certificado a um CA com um nome de sujeito que corresponda ao nome de domínio personalizado do seu serviço. Por exemplo, se o seu nome de domínio personalizado for **contoso.com,** o certificado da sua AC deve ter o nome **de assunto .contoso.com** ou __contoso__**www.contoso.com**. __www__

    >[!NOTE]
    >Não é possível obter um certificado SSL/TLS de um CA para o domínio __cloudapp__**.net.**

-   O certificado deve utilizar um mínimo de 2.048 bits de encriptação.

O protocolo HTTP é inseguro e está sujeito a ataques de escuta. Os dados que são transmitidos através de HTTP são enviados como texto simples do navegador web para o servidor web ou entre outros pontos finais. Os atacantes podem intercetar e visualizar dados sensíveis que são enviados através de HTTP, tais como dados do cartão de crédito e logins de conta. Quando os dados são enviados ou publicados através de um navegador através de HTTPS, o SSL garante que as informações sensíveis são encriptadas e seguras a partir da interceção.

Para saber mais sobre a utilização de certificados SSL/TLS, consulte [configurar tLS para obter uma aplicação em Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Use o isolamento da rede e a segurança com o Tecido de Serviço Azure
Crie um cluster de 3 tipos seguros de tipo usando o [modelo do Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) como amostra. Controle o tráfego de rede de entrada e saída utilizando o modelo e os grupos de segurança da rede.

O modelo tem um NSG para cada um dos conjuntos de balanças de máquinas virtuais e é usado para controlar o tráfego dentro e fora do conjunto. As regras são configuradas por padrão para permitir todo o tráfego necessário para os serviços do sistema e as portas de aplicação especificadas no modelo. Reveja estas regras e faça quaisquer alterações que se adaptem às suas necessidades, incluindo a adição de novas regras para as suas aplicações.

Para obter mais informações, consulte [cenários comuns de networking para O Tecido de Serviço Azure.](../../service-fabric/service-fabric-patterns-networking.md)

## <a name="set-up-azure-key-vault-for-security"></a>Configurar o Cofre da Chave Azure para segurança
O Service Fabric utiliza certificados para fornecer autenticação e encriptação para garantir um cluster e suas aplicações.

A Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação. Você usa Azure Key Vault para [gerir certificados](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) para clusters de tecido de serviço em Azure. O fornecedor de recursos Azure que cria os clusters retira os certificados de um cofre chave. Em seguida, o fornecedor instala os certificados nos VMs quando o cluster é implantado no Azure.

Existe uma relação de certificado entre [o Azure Key Vault,](../../key-vault/general/secure-your-key-vault.md)o cluster de tecido de serviço, e o fornecedor de recursos que utiliza os certificados. Quando o cluster é criado, a informação sobre a relação com o certificado é armazenada num cofre chave.

Há dois passos básicos para montar um cofre chave:
1. Crie um grupo de recursos especificamente para o seu cofre de chaves.

    Recomendamos que coloque o cofre chave no seu próprio grupo de recursos. Esta ação ajuda a prevenir a perda das suas chaves e segredos se outros grupos de recursos forem removidos, tais como armazenamento, cálculo ou o grupo que contém o seu cluster. O grupo de recursos que contém o seu cofre-chave deve estar na mesma região que o cluster que o está a usar.

2. Crie um cofre chave no novo grupo de recursos.

    O cofre-chave deve ser ativado para a colocação. O fornecedor de recursos computacionais pode então obter os certificados do cofre e instalá-los nas instâncias VM.

Para saber mais sobre como montar um cofre chave, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções
Depois de ter criado as aplicações para representar o seu cluster, atribua os seus utilizadores às funções que são suportadas pela Service Fabric: apenas para leitura e administração. Pode atribuir estas funções utilizando o portal Azure.

>[!NOTE]
> Para obter mais informações sobre a utilização de funções em Tecido de Serviço, consulte [o Controlo de Acesso Baseado em Fun para clientes de Tecidos](../../service-fabric/service-fabric-cluster-security-roles.md)de Serviço.

A Azure Service Fabric suporta dois tipos de controlo de acesso para clientes que estão ligados a um [cluster de Tecido de Serviço](../../service-fabric/service-fabric-cluster-creation-via-arm.md): administrador e utilizador. O administrador do cluster pode usar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. O controlo de acesso torna o cluster mais seguro.

## <a name="next-steps"></a>Passos seguintes

- [Lista de verificação de segurança do tecido de serviço](../../service-fabric/service-fabric-best-practices-security.md)
- Configurar o seu [ambiente de desenvolvimento](../../service-fabric/service-fabric-get-started.md)do Tecido de Serviço.
- Saiba mais sobre [as opções de suporte do Tecido de Serviço.](../../service-fabric/service-fabric-support.md)