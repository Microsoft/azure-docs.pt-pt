---
title: O Azure Service Fabric melhores práticas de segurança | Documentos da Microsoft
description: Este artigo fornece um conjunto de melhores práticas para segurança do Azure Service Fabric.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611527"
---
# <a name="azure-service-fabric-security-best-practices"></a>O Azure Service Fabric melhores práticas de segurança
Implementar uma aplicação no Azure é rápida, fácil e económica. Antes de implementar a sua aplicação na cloud em produção, consulte a nossa lista de recomendados e essenciais melhores práticas para a implementação de clusters seguros em seu aplicativo.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

Para cada melhor prática, Explicamos:

-   O que é a melhor prática.
-   Por que deve implementar a melhor prática.
-   O que pode acontecer se não implementar a melhor prática.
-   Como pode aprender a implementar a melhor prática.

Recomendamos a segurança do Azure Service Fabric seguinte melhores práticas:

-   Utilize modelos Azure Resource Manager e o módulo do PowerShell do Service Fabric para criar clusters seguros.
-   Utilize certificados X.509.
-   Configure políticas de segurança.
-   Implemente a configuração de segurança de Reliable Actors.
-   Configure SSL para o Azure Service Fabric.
-   Utilize a segurança e isolamento de rede com o Azure Service Fabric.
-   Configure o Azure Key Vault para segurança.
-   Atribua utilizadores a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Melhores práticas para proteger os seus clusters

Utilize sempre um cluster seguro:
-   Implementar a segurança de cluster utilizando certificados.
-   Fornecer acesso de cliente (administração e só de leitura) com o Azure Active Directory (Azure AD).

Utilize implementações automatizadas:
-   Utilize scripts para gerar, implementar e os segredos são acumuladas.
-   Store os segredos no Cofre de chaves do Azure e utilizar o Azure AD para todos os outro acesso de cliente.
-   Exigir a autenticação para acesso humano para os segredos.

Além disso, considere as seguintes opções de configuração:
-   Utilizar grupos de segurança de rede do Azure (NSG) para criar redes de perímetro (também conhecido como desmilitarizada zonas, DMZs e sub-redes filtradas).
-   Aceder a máquinas de virtuais de cluster (VMs) ou gerir o cluster ao utilizar servidores de ligação com a conexão de área de trabalho remoto.

Os clusters tem de ser protegidos para impedir que os utilizadores não autorizados a ligar, especialmente quando um cluster está em execução na produção. Embora seja possível criar um cluster não seguro, usuários anônimos podem ligar ao cluster, se o cluster expõe os pontos finais de gestão para a internet pública.

Existem três [cenários](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para a implementação de segurança do cluster, utilizando várias tecnologias:

-   Segurança de nó para nó: Este cenário protege a comunicação entre as VMs e computadores no cluster. Essa forma de segurança garante que apenas os computadores que estejam autorizados a aderir o cluster podem alojar aplicações e serviços no cluster.
Neste cenário, os clusters que executam no Azure ou clusters autónomos que são executados no Windows, podem utilizar [segurança de certificado](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou [segurança do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) para máquinas do Windows Server.
-   Segurança de cliente para nó: Este cenário protege a comunicação entre um cliente do Service Fabric e os nós individuais no cluster.
-   Controlo de acesso baseado em funções (RBAC): Este cenário utiliza identidades separadas (certificados, o Azure AD, e assim por diante) para cada função de cliente de administrador e utilizador que acede ao cluster. Especificar as identidades de função quando criar o cluster.

>[!NOTE]
>**Recomendação de segurança para clusters do Azure:** Utilize a segurança do Azure AD para autenticar clientes e certificados para a segurança de nó para nó.

Para configurar um cluster autónomo do Windows, consulte [configurar definições para um cluster autónomo de Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Utilize modelos Azure Resource Manager e o módulo do PowerShell do Service Fabric para criar um cluster seguro.
Para obter instruções passo a passo Criar um cluster do Service Fabric seguro com modelos Azure Resource Manager, consulte [criar um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Utilize o modelo Azure Resource Manager:
-   Personalize o seu cluster com o modelo para configurar o armazenamento gerido para a VM de discos rígidos virtuais (VHDs).
-   Orientar alterações ao seu grupo de recursos, utilizando o modelo para a gestão de configuração fácil e auditoria.

Trate a configuração do cluster como o código:
-   Ser completa ao verificar as configurações de implementação.
-   Evite utilizar comandos implícitos para modificar diretamente os seus recursos.

Muitos aspetos dos [ciclo de vida do Service Fabric application](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) pode ser automatizado. O [módulo do PowerShell do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiza tarefas comuns para implementar, atualizar, remover e testar aplicações do Azure Service Fabric. APIs e APIs de HTTP para gestão de aplicações também estão disponíveis.

## <a name="use-x509-certificates"></a>Utilizar certificados X.509
Proteja sempre os seus clusters utilizando certificados X.509 ou segurança do Windows. Segurança só está configurada no momento de criação do cluster. Não é possível ativar a segurança depois do cluster ser criado.

Para especificar uma [certificado de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), defina o valor da **ClusterCredentialType** propriedade para X509. Para especificar um certificado de servidor para conexões externas, defina o **ServerCredentialType** propriedade para X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção utilizando um serviço de certificado de servidor Windows corretamente configurado. Também pode obter os certificados de uma autoridade de certificação aprovados (AC).
-   Nunca utilize uma temporária ou certificado para clusters de produção de teste, se o certificado foi criado utilizando MakeCert.exe ou uma ferramenta semelhante.
-   Utilize um certificado autoassinado para clusters de teste, mas não para clusters de produção.

Se o cluster não é seguro, qualquer pessoa pode ligar ao cluster anonimamente e realizar operações de gestão. Por esse motivo, proteja sempre os clusters de produção ao utilizar certificados X.509 ou segurança do Windows.

Para saber mais sobre como utilizar certificados X.509, veja [adicionar ou remover certificados para um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
Service Fabric também protege os recursos que são utilizados pelas aplicações. Recursos como arquivos, diretórios e certificados são armazenados em contas de utilizador quando a aplicação é implementada. Esta funcionalidade torna aplicativos em execução mais segura entre si, mesmo num ambiente de hospedagem compartilhado.

-   Utilize um grupo de domínio do Active Directory ou o utilizador: Execute o serviço com as credenciais para uma conta de grupo ou utilizador do Active Directory. Certifique-se de que utiliza o Active Directory no local dentro de seu domínio e não do Azure Active Directory. Aceda a outros recursos no domínio que foram concedidos permissões através de um utilizador de domínio ou grupo. Por exemplo, recursos, como partilhas de ficheiros.

-   Atribua uma política de acesso de segurança para pontos finais HTTP e HTTPS: Especifique a **SecurityAccessPolicy** propriedade para aplicar uma **RunAs** política a um serviço ao manifesto do serviço declara os recursos de ponto final com HTTP. As portas alocadas para os pontos finais HTTP são listas corretamente controlado de acesso para a conta de utilizador de RunAs que o serviço é executado em. Quando a política não estiver configurada, o HTTP. sys não tem acesso ao serviço e pode obter falhas com chamadas do cliente.

Para saber como utilizar políticas de segurança num cluster do Service Fabric, veja [configurar políticas de segurança para a sua aplicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança de Reliable Actors
Reliable Actors do Service Fabric é uma implementação do padrão de design de ator. Tal como acontece com qualquer padrão de design de software, a decisão de usar um padrão específico baseia-se um problema de software se encaixa o padrão.

Em geral, utilize o padrão de design de ator para soluções de modelo para os seguintes problemas de software ou a cenários de segurança:
-   Seu espaço de problema envolve um grande número (milhares ou mais) de pequenos, de forma independentes e isoladas unidades de estado e lógica.
-   Está trabalhando com objetos de thread único de mensagens em fila que não exigem significativa interação de componentes externos, incluindo consultas de estado entre um conjunto de atores.
-   As instâncias de ator não bloqueiam os chamadores com atrasos imprevisíveis através da emissão de operações de e/s.

No Service Fabric, atores são implementados na estrutura da aplicação de Reliable Actors. Essa estrutura é com base no padrão de ator e criada sobre [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Cada serviço reliable actor que escrever é um serviço fiável com estado particionado.

Cada ator é definido como uma instância de um tipo de ator, idêntica à forma como um objeto .NET é uma instância de um tipo .NET. Por exemplo, um **tipo de ator** que implementa a funcionalidade de uma Calculadora pode ter vários atores desse tipo que são distribuídas em vários nós num cluster. Cada um dos atores distribuídos exclusivamente é caracterizada por um identificador de ator.

[Configurações de segurança do replicador](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) são utilizados para proteger o canal de comunicação que é utilizado durante a replicação. Esta configuração impede que os serviços a ver o tráfego de replicação entre si e assegura que os dados de elevada disponibilidade são seguros. Por predefinição, uma seção de configuração de segurança vazio impede que a segurança de replicação.
Configurações do replicador configurar o replicator, que é responsável por verificar o estado de fornecedor de estado do Ator altamente confiável.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurar SSL para o Azure Service Fabric
O processo de autenticação de servidor [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pontos finais de gestão de cluster para um cliente de gestão. O cliente de gestão, em seguida, reconhece que está falando ao real cluster. Este certificado também fornece uma [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS.
Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado a partir de uma autoridade de certificação, nome do requerente do certificado tem de corresponder ao nome de domínio personalizado que utiliza para o seu cluster.

Para configurar SSL para uma aplicação, tem primeiro de obter um certificado SSL assinado por uma AC. A AC é um terceiro confiável que emite certificados SSL para fins de segurança. Se ainda não tiver um certificado SSL, precisa de obter uma de uma empresa que vende certificados SSL.

O certificado tem de cumprir os seguintes requisitos para certificados SSL no Azure:
-   O certificado tem de conter uma chave privada.

-   O certificado tem de ser criado para troca de chaves e de ser exportável para um ficheiro do exchange (. pfx) de informações pessoais.

-   Nome do requerente do certificado tem de corresponder ao nome de domínio que é utilizado para aceder ao seu serviço cloud.

    - Adquira um nome de domínio personalizado para utilizar para aceder ao seu serviço cloud.
    - Pedir um certificado a partir de uma AC com um nome de requerente que corresponde ao nome de domínio personalizado do seu serviço. Por exemplo, se o nome de domínio personalizado for __contoso__ **.com**, o certificado da AC deve ter o nome do requerente **. contoso.com** ou __www__ **. contoso.com**.

    >[!NOTE]
    >Não é possível obter um certificado SSL a partir de uma AC para o __cloudapp__ **.net** domínio.

-   O certificado tem de utilizar um mínimo de encriptação 2.048 bits.

O protocolo HTTP é inseguro e sujeitos a ataques de espionagem. Dados transmitidos através de HTTP são enviados como texto simples a partir do browser para o servidor web ou entre outros pontos de extremidade. Os atacantes podem interceptar e ver dados confidenciais que são enviados por HTTP, como detalhes de cartão de crédito e inícios de sessão de conta. Quando são enviadas ou publicado através de um browser através de HTTPS, SSL garante que as informações confidenciais estão encriptados e segura de interceptação.

Para saber mais sobre como utilizar certificados SSL, veja [configurar o SSL para aplicações do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Utilizar a segurança e isolamento de rede com o Azure Service Fabric
Configurar um cluster seguro 3 nodetype com o [modelo Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) como um exemplo. Controle o tráfego de rede de entrada e saída com o modelo e os grupos de segurança de rede.

O modelo tem um NSG para cada um dos conjuntos de dimensionamento de máquina virtual e é utilizado para controlar o tráfego que entra e sai do conjunto. As regras são configuradas por predefinição, para permitir todo o tráfego necessário para os serviços do sistema e as portas de aplicação especificadas no modelo. Reveja estas regras e faça quaisquer alterações às suas necessidades, incluindo a adição de novas regras para as suas aplicações.

Para obter mais informações, consulte [cenários comuns de redes para o Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Configurar o Azure Key Vault para segurança
O Service Fabric utiliza certificados para fornecer autenticação e encriptação para proteger um cluster e as respetivas aplicações.

O Service Fabric utiliza certificados X.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. Utilizar o Azure Key Vault para [gerir certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) para clusters do Service Fabric no Azure. O fornecedor de recursos do Azure que cria os clusters obtém os certificados de um cofre de chaves. O fornecedor, em seguida, instala os certificados nas VMs quando o cluster é implementado no Azure.

Existe uma relação de certificado entre [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), o cluster do Service Fabric e o fornecedor de recursos que utiliza os certificados. Quando o cluster é criado, informações sobre a relação de certificado são armazenadas num cofre de chaves.

Existem dois passos básicos para configurar um cofre de chaves:
1. Crie um grupo de recursos especificamente para o seu Cofre de chaves.

    Recomendamos que coloque o Cofre de chaves no seu próprio grupo de recursos. Esta ação ajuda a impedir a perda das chaves e segredos, se forem removidos outros grupos de recursos, como o armazenamento, computação ou o grupo que contém o cluster. O grupo de recursos que contém o seu Cofre de chaves tem de ser na mesma região que o cluster que está a utilizar.

2. Crie um cofre de chaves no novo grupo de recursos.

    O Cofre de chaves tem de estar ativado para a implementação. O fornecedor de recursos de computação, em seguida, pode obter os certificados a partir do cofre e instalá-las nas instâncias de VM.

Para saber mais sobre como configurar um cofre de chaves, consulte [o que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções
Depois de criar os aplicativos para representar o cluster, atribuir os seus utilizadores para as funções que são suportadas pelo Service Fabric: só de leitura e administrador. Pode atribuir essas funções com o portal do Azure.

>[!NOTE]
> Para obter mais informações sobre como utilizar funções no Service Fabric, veja [controlo de acesso baseado em funções para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

O Azure Service Fabric suporta dois tipos de controle de acesso para clientes que estão ligados a um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrador e usuário. O administrador de cluster pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Controlo de acesso torna o cluster mais seguro.

## <a name="next-steps"></a>Passos Seguintes

- [Lista de verificação de segurança de Service Fabric](azure-service-fabric-security-checklist.md)
- Configurar o Service Fabric [ambiente de desenvolvimento](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Saiba mais sobre [opções de suporte do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
