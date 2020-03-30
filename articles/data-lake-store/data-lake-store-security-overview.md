---
title: Visão geral da segurança em Azure Data Lake Storage Gen1 [ Microsoft Docs
description: Entenda como azure Data Lake Storage Gen1 é uma loja de big data mais segura
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 4e640aa1cb02174c935c0f7c1d61ab2fca5ea046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974581"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Segurança em Azure Data Lake Storage Gen1
Muitas empresas estão a aproveitar-se da análise de big data para insights empresariais para ajudá-las a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulado, com um número crescente de utilizadores diversos. É vital que uma empresa garanta que os dados empresariais críticos sejam armazenados de forma mais segura, com o nível correto de acesso concedido a utilizadores individuais. O Azure Data Lake Storage Gen1 foi concebido para ajudar a satisfazer estes requisitos de segurança. Neste artigo, conheça as capacidades de segurança do Data Lake Storage Gen1, incluindo:

* Autenticação
* Autorização
* Isolamento de rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Autenticação e gestão de identidade
A autenticação é o processo pelo qual a identidade de um utilizador é verificada quando o utilizador interage com data Lake Storage Gen1 ou com qualquer serviço que se ligue ao Data Lake Storage Gen1. Para gestão e autenticação de identidade, data Lake Storage Gen1 utiliza [o Azure Ative Directory,](../active-directory/fundamentals/active-directory-whatis.md)uma solução abrangente de identidade e gestão de acesso solução cloud que simplifica a gestão de utilizadores e grupos.

Cada subscrição do Azure pode ser associada a uma instância de Diretório Ativo Azure. Apenas utilizadores e identidades de serviço que são definidas no seu serviço de Diretório Ativo Azure podem aceder à sua conta Data Lake Storage Gen1, utilizando o portal Azure, ferramentas de linha de comando ou através de aplicações de clientes que a sua organização constrói utilizando o Data Lake Armazenamento Gen1 SDK. As principais vantagens da utilização do Diretório Ativo Azure como mecanismo centralizado de controlo de acesso são:

* Gestão simplificada do ciclo de vida da identidade. A identidade de um utilizador ou de um serviço (uma identidade principal de serviço) pode ser rapidamente criada e rapidamente revogada simplesmente apagando ou desativando a conta no diretório.
* Autenticação multi-factor. [A autenticação multifactor](../active-directory/authentication/multi-factor-authentication.md) proporciona uma camada adicional de segurança para os sintetantes e transações do utilizador.
* Autenticação de qualquer cliente através de um protocolo aberto padrão, como o OAuth ou o OpenID.
* Federação com serviços de diretório empresarial e fornecedores de identidade na nuvem.

## <a name="authorization-and-access-control"></a>Controlo de autorização e acesso
Depois de o Azure Ative Directory autenticar um utilizador para que o utilizador possa aceder ao Data Lake Storage Gen1, a autorização controla as permissões de acesso para data Lake Storage Gen1. Data Lake Storage Gen1 separa a autorização para atividades relacionadas com contas e relacionadas com dados da seguinte forma:

* [Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecido pelo Azure para gestão de contas
* POSIX ACL para aceder a dados na loja

### <a name="rbac-for-account-management"></a>RBAC para gestão de conta
Quatro funções básicas são definidas para Data Lake Storage Gen1 por padrão. As funções permitem diferentes operações numa conta Gen1 de Armazenamento de Data Lake através do portal Azure, powerShell cmdlets e APIs REST. As funções de Proprietário e Colaborador podem desempenhar uma variedade de funções de administração na conta. Pode atribuir a função De Leitor a utilizadores que apenas visualizam dados de gestão de contas.

![Funções RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funções RBAC")

Note que, embora as funções sejam atribuídas para a gestão de conta, algumas funções afetam o acesso aos dados. É necessário utilizar ACLs para controlar o acesso a operações que um utilizador pode realizar no sistema de ficheiros. A tabela que se segue apresenta um resumo dos direitos de gestão e dos direitos de acesso aos dados para as funções predefinidas.

| Funções | Direitos de gestão | Direitos de acesso a dados | Explicação |
| --- | --- | --- | --- |
| Nenhum papel atribuído |Nenhuma |Governado pela ACL |O utilizador não pode utilizar o portal Azure ou os cmdlets Azure PowerShell para navegar na Data Lake Storage Gen1. O utilizador só pode utilizar ferramentas de linha de comando. |
| Proprietário |Todos |Todos |O papel do Proprietário é um superutilizador. Este papel pode gerir tudo e tem acesso total aos dados. |
| Leitor |Só de leitura |Governado pela ACL |O papel do Leitor pode ver tudo no que diz respeito à gestão de conta, como por exemplo, qual utilizador é atribuído a que papel. O papel do Leitor não pode fazer alterações. |
| Contribuinte |Tudo exceto adicionar e remover funções |Governado pela ACL |O papel do Colaborador pode gerir alguns aspetos de uma conta, tais como implementações e criação e gestão de alertas. A função Contributiva não pode adicionar ou remover funções. |
| Administrador de Acesso de Utilizador |Adicionar e remover funções |Governado pela ACL |A função de Administrador de Acesso ao Utilizador pode gerir o acesso do utilizador às contas. |

Para obter instruções, consulte [utilizadores ou grupos de segurança para as contas data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Utilização de ACLs para operações em sistemas de ficheiros
Data Lake Storage Gen1 é um sistema hierárquico de ficheiros como hadoop Distributed File System (HDFS), e suporta [ACLs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controla a leitura (r), a escrita (w) e a execução (x) permissões aos recursos para o papel de Proprietário, para o grupo Proprietários, e para outros utilizadores e grupos. Em Data Lake Storage Gen1, os ACLs podem ser ativados na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto do Data Lake Storage Gen1, consulte o controlo de [acesso em Data Lake Storage Gen1](data-lake-store-access-control.md).

Recomendamos que defina ACLs para vários utilizadores utilizando [grupos](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)de segurança . Adicione os utilizadores a um grupo de segurança e, em seguida, atribua os ACLs para um ficheiro ou pasta a esse grupo de segurança. Isto é útil quando pretende fornecer permissões atribuídas, porque está limitado a um máximo de 28 entradas para permissões atribuídas. Para obter mais informações sobre como proteger melhor os dados armazenados no Data Lake Storage Gen1 utilizando grupos de segurança do Diretório Ativo Azure, consulte os utilizadores ou grupos de [segurança como ACLs para o sistema de ficheiros Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Lista de permissões de acesso](./media/data-lake-store-security-overview/adl.acl.2.png "Lista de permissões de acesso")

## <a name="network-isolation"></a>Isolamento de rede
Utilize data Lake Storage Gen1 para ajudar a controlar o acesso à sua loja de dados ao nível da rede. Pode estabelecer firewalls e definir um intervalo de endereçoIP para os seus clientes de confiança. Com uma gama de endereços IP, apenas os clientes que possuem um endereço IP dentro da gama definida podem ligar-se ao Data Lake Storage Gen1.

![Definições de firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Definições de firewall e endereço IP")

## <a name="data-protection"></a>Proteção de dados
Data Lake Storage Gen1 protege os seus dados ao longo do seu ciclo de vida. Para dados em trânsito, data Lake Storage Gen1 utiliza o protocolo de segurança da camada de transporte (TLS 1.2) padrão da indústria para proteger dados sobre a rede.

![Encriptação em Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Encriptação em Data Lake Storage Gen1")

Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Se optar pela encriptação, os dados armazenados no Data Lake Storage Gen1 são encriptados antes de armazenar em meios persistentes. Neste caso, o Data Lake Storage Gen1 encripta automaticamente os dados antes de persistir e desencripta dados antes da recuperação, pelo que é completamente transparente para o cliente aceder aos dados. Não é necessária qualquer alteração de código no lado do cliente para encriptar/desencriptar dados.

Para a gestão de chaves, data Lake Storage Gen1 fornece dois modos para gerir as suas chaves de encriptação master (MEKs), que são necessárias para desencriptar quaisquer dados que sejam armazenados em Data Lake Storage Gen1. Pode deixar o Data Lake Storage Gen1 gerir os MEKs para si, ou optar por manter a propriedade dos MEKs utilizando a sua conta Azure Key Vault. Especifica o modo de gestão da chave enquanto cria uma conta Gen1 de Armazenamento de Data Lake. Para obter mais informações sobre como fornecer configuração relacionada com encriptação, consulte [Iniciar-se com o Azure Data Lake Storage Gen1 utilizando o Portal Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Registos de atividade e de diagnóstico
Pode utilizar registos de atividade ou diagnóstico, dependendo se está à procura de registos para atividades relacionadas com a gestão de contas ou atividades relacionadas com dados.

* As atividades relacionadas com a gestão de conta utilizam APIs do Gestor de Recursos Azure e surgem no portal Azure através de registos de atividade.
* As atividades relacionadas com dados utilizam APIs DE REPOUSO WebHDFS e são surgidas no portal Azure através de registos de diagnóstico.

### <a name="activity-log"></a>Registo de atividades
Para cumprir os regulamentos, uma organização pode exigir pistas de auditoria adequadas das atividades de gestão de contas se precisar de investigar incidentes específicos. Data Lake Storage Gen1 tem monitorização incorporada e regista todas as atividades de gestão de conta.

Para os trilhos de auditoria de gestão de contas, veja e escolha as colunas que pretende registar. Também pode exportar registos de atividade para o Armazenamento Azure.

![Registo de atividades](./media/data-lake-store-security-overview/activity-logs.png "Registo de atividades")

Para obter mais informações sobre o trabalho com registos de atividade, consulte ver registos de [atividade sonoridades para auditar ações sobre recursos](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Registos de diagnósticos
Pode ativar a auditoria de acesso a dados e o registo de diagnóstico no portal Azure e enviar os registos para uma conta de armazenamento Azure Blob, um hub de eventos ou registos do Monitor Azure.

![Registos de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registos de diagnósticos")

Para obter mais informações sobre o trabalho com registos de diagnóstico com data lake storage Gen1, consulte [Aceder a registos de diagnóstico para Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo
Os clientes empresariais exigem uma plataforma de nuvem de análise de dados que seja segura e fácil de usar. Data Lake Storage Gen1 foi projetado para ajudar a resolver estes requisitos através da gestão e autenticação de identidade através da integração do Diretório Ativo Azure, autorização baseada em ACL, isolamento de rede, encriptação de dados em trânsito e em repouso, e auditoria.

Se quiser ver novas funcionalidades no Data Lake Storage Gen1, envie-nos o seu feedback no [fórum Data Lake Storage Gen1 UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Começar com Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
