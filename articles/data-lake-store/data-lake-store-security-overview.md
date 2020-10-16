---
title: Visão geral da segurança no Azure Data Lake Storage Gen1 Microsoft Docs
description: Conheça as capacidades de segurança da Azure Data Lake Storage Gen1, incluindo autenticação, autorização, isolamento de rede, proteção de dados e auditoria.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 0e10afa9293bbebbb68a6fc3eae4bc3f75813ad0
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106829"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Segurança em Azure Data Lake Storage Gen1

Muitas empresas estão a aproveitar-se da análise de big data para obter insights de negócios para ajudá-las a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulado, com um número crescente de utilizadores diversos. É vital que uma empresa garanta que os dados empresariais críticos são armazenados de forma mais segura, com o nível correto de acesso concedido a utilizadores individuais. A Azure Data Lake Storage Gen1 foi projetada para ajudar a satisfazer estes requisitos de segurança. Neste artigo, conheça as capacidades de segurança da Data Lake Storage Gen1, incluindo:

* Autenticação
* Autorização
* Isolamento da rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Autenticação e gestão de identidades

A autenticação é o processo pelo qual a identidade de um utilizador é verificada quando o utilizador interage com a Data Lake Storage Gen1 ou com qualquer serviço que se conecte à Data Lake Storage Gen1. Para gestão de identidade e autenticação, a Data Lake Storage Gen1 utiliza [o Azure Ative Directory,](../active-directory/fundamentals/active-directory-whatis.md)uma solução abrangente de nuvem de gestão de identidade e acesso que simplifica a gestão de utilizadores e grupos.

Cada subscrição do Azure pode ser associada a um caso de Diretório Ativo Azure. Apenas os utilizadores e identidades de serviço que são definidas no seu serviço Azure Ative Directory podem aceder à sua conta Desarrume Desejamento Gen1, utilizando o portal Azure, ferramentas de linha de comando ou através de aplicações de clientes que a sua organização constrói utilizando o Data Lake Storage Gen1 SDK. As principais vantagens da utilização do Azure Ative Directory como mecanismo centralizado de controlo de acessos são:

* Gestão simplificada do ciclo de vida da identidade. A identidade de um utilizador ou de um serviço (identidade principal de serviço) pode ser rapidamente criada e rapidamente revogada simplesmente eliminando ou desativando a conta no diretório.
* Autenticação multi-factor. [A autenticação multi-factor](../active-directory/authentication/concept-mfa-howitworks.md) proporciona uma camada adicional de segurança para os insentus em termos de utilizador e transações.
* Autenticação de qualquer cliente através de um protocolo aberto padrão, como OAuth ou OpenID.
* Federação com serviços de diretório de empresas e fornecedores de identidade na nuvem.

## <a name="authorization-and-access-control"></a>Autorização e controlo de acessos

Depois de o Azure Ative Directory autenticar um utilizador para que o utilizador possa aceder à Data Lake Storage Gen1, a autorização controla as permissões de acesso para data lake storage gen1. Data Lake Storage Gen1 separa a autorização para atividades relacionadas com contas e dados da seguinte forma:

* [Controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) para gestão de conta
* POSIX ACL para aceder a dados na loja

### <a name="rbac-for-account-management"></a>RBAC para gestão de contas

Quatro funções básicas são definidas para data lake storage gen1 por padrão. As funções permitem diferentes operações numa conta da Data Lake Storage Gen1 através do portal Azure, cmdlets PowerShell e REST APIs. As funções de Proprietário e Contribuinte podem desempenhar uma variedade de funções de administração na conta. Pode atribuir a função Reader a utilizadores que apenas visualizam dados de gestão de conta.

![Funções do Azure](./media/data-lake-store-security-overview/rbac-roles.png "Funções do Azure")

Note que, embora as funções sejam atribuídas para a gestão de conta, algumas funções afetam o acesso aos dados. É necessário utilizar ACLs para controlar o acesso a operações que um utilizador pode realizar no sistema de ficheiros. A tabela que se segue mostra um resumo dos direitos de gestão e dos direitos de acesso aos dados para as funções predefinidos.

| Funções | Direitos de gestão | Direitos de acesso a dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhum |Regido pela ACL |O utilizador não pode utilizar o portal Azure ou os cmdlets Azure PowerShell para navegar no Data Lake Storage Gen1. O utilizador só pode utilizar ferramentas de linha de comando. |
| Proprietário |Todos |Todos |O papel de Proprietário é um super-ador. Esta função pode gerir tudo e tem acesso total aos dados. |
| Leitor |Só de leitura |Regido pela ACL |A função Reader pode ver tudo no que diz respeito à gestão de conta, como qual o utilizador que é atribuído a que papel. O papel de Leitor não pode fazer alterações. |
| Contribuinte |Todos, exceto adicionar e remover funções |Regido pela ACL |O papel de Contribuinte pode gerir alguns aspetos de uma conta, como implementações e criação e gestão de alertas. A função Contribuinte não pode adicionar ou remover funções. |
| Administrador de Acesso dos Utilizadores |Adicionar e remover funções |Regido pela ACL |A função de Administrador de Acesso ao Utilizador pode gerir o acesso do utilizador às contas. |

Para obter instruções, consulte [atribuir utilizadores ou grupos de segurança às contas da Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Utilização de ACLs para operações em sistemas de ficheiros

Data Lake Storage Gen1 é um sistema de ficheiros hierárquico como o Hadoop Distributed File System (HDFS), e suporta [ACLs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controla ler (r), escrever (w) e executar (x) permissões de recursos para o papel proprietário, para o grupo Proprietários, e para outros utilizadores e grupos. Na Data Lake Storage Gen1, os ACLs podem ser ativados na pasta raiz, nas sub-dobradeiras e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto da Data Lake Storage Gen1, consulte o controlo de [acesso na Data Lake Storage Gen1](data-lake-store-access-control.md).

Recomendamos que defina ACLs para vários utilizadores utilizando [grupos de segurança.](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) Adicione os utilizadores a um grupo de segurança e, em seguida, atribua os ACLs para um ficheiro ou pasta a esse grupo de segurança. Isto é útil quando pretende fornecer permissões atribuídas, porque está limitado a um máximo de 28 entradas para permissões atribuídas. Para obter mais informações sobre como proteger melhor os dados armazenados na Data Lake Storage Gen1 utilizando grupos de segurança do Azure Ative Directory, consulte [os utilizadores ou grupos de segurança como ACLs para o sistema de ficheiros Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Listar permissões de acesso](./media/data-lake-store-security-overview/adl.acl.2.png "Listar permissões de acesso")

## <a name="network-isolation"></a>Isolamento da rede

Utilize a Data Lake Storage Gen1 para ajudar a controlar o acesso à sua loja de dados ao nível da rede. Pode estabelecer firewalls e definir um intervalo de endereços IP para os seus clientes de confiança. Com uma gama de endereços IP, apenas os clientes que tenham um endereço IP dentro do intervalo definido podem ligar-se à Data Lake Storage Gen1.

![Definições de firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Definições de firewall e endereço IP")

As tags de serviço de suporte de redes virtuais Azure (VNet) para Data Lake Gen 1. Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. Para mais informações, consulte as etiquetas de [serviço da Azure.](../virtual-network/service-tags-overview.md)

## <a name="data-protection"></a>Proteção de dados

Data Lake Storage Gen1 protege os seus dados ao longo do seu ciclo de vida. Para dados em trânsito, a Data Lake Storage Gen1 utiliza o protocolo de Segurança da Camada de Transporte (TLS 1.2) padrão da indústria para proteger dados sobre a rede.

![Encriptação em Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Encriptação em Data Lake Storage Gen1")

Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Se optar pela encriptação, os dados armazenados na Data Lake Storage Gen1 são encriptados antes de armazenar em meios persistentes. Neste caso, a Data Lake Storage Gen1 encripta automaticamente os dados antes de persistir e desencriptar dados antes da recuperação, pelo que é completamente transparente para o cliente que acede aos dados. Não é necessária nenhuma alteração de código no lado do cliente para encriptar/desencriptar dados.

Para uma gestão chave, a Data Lake Storage Gen1 fornece dois modos para gerir as suas chaves de encriptação principal (MEKs), que são necessárias para desencriptar quaisquer dados que sejam armazenados na Data Lake Storage Gen1. Pode permitir que a Data Lake Storage Gen1 gere os MEKs para si, ou opte por manter a propriedade dos MEKs usando a sua conta Azure Key Vault. Especifica o modo de gestão de chaves ao criar uma conta Desagénero de Armazenamento de Dados. Para obter mais informações sobre como fornecer configuração relacionada com encriptação, consulte [Começar com a Azure Data Lake Storage Gen1 utilizando o Portal Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Registos de atividade e de diagnóstico

Pode utilizar registos de atividade ou de diagnóstico, dependendo se está à procura de registos para atividades relacionadas com gestão de conta ou atividades relacionadas com dados.

* As atividades relacionadas com a gestão de conta utilizam as APIs do Gestor de Recursos Azure e são surgidas no portal Azure através de registos de atividade.
* As atividades relacionadas com os dados utilizam as APIs do Rest da WebHDFS e são surgidas no portal Azure através de registos de diagnóstico.

### <a name="activity-log"></a>Registo de atividades

Para cumprir os regulamentos, uma organização pode exigir pistas de auditoria adequadas das atividades de gestão de contas, se precisar de investigar incidentes específicos. A Data Lake Storage Gen1 tem monitorização incorporada e regista todas as atividades de gestão de conta.

Para pistas de auditoria de gestão de contas, veja e escolha as colunas que pretende registar. Também pode exportar registos de atividades para o Azure Storage.

![Registo de atividades](./media/data-lake-store-security-overview/activity-logs.png "Registo de atividades")

Para obter mais informações sobre o trabalho com registos de atividades, consulte [os registos de atividades para auditar ações de recursos.](../azure-resource-manager/management/view-activity-logs.md)

### <a name="diagnostics-logs"></a>Registos de diagnósticos

Pode ativar a auditoria de acesso a dados e o registo de diagnóstico no portal Azure e enviar os registos para uma conta de armazenamento Azure Blob, um centro de eventos ou registos do Azure Monitor.

![Registos de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registos de diagnósticos")

Para obter mais informações sobre o trabalho com registos de diagnóstico com a Data Lake Storage Gen1, consulte [aceder aos registos de diagnóstico para data lake storage gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo

Os clientes da empresa exigem uma plataforma de nuvem de análise de dados que seja segura e fácil de usar. A Data Lake Storage Gen1 foi concebida para ajudar a resolver estes requisitos através da gestão de identidade e autenticação através da integração do Azure Ative Directory, autorização baseada em ACL, isolamento de rede, encriptação de dados em trânsito e em repouso, e auditoria.

Se quiser ver novas funcionalidades na Data Lake Storage Gen1, envie-nos o seu feedback no [fórum UserVoice do Data Lake Storage Gen1.](https://feedback.azure.com/forums/327234-data-lake)

## <a name="see-also"></a>Ver também

* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Começar com data lake storage gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)