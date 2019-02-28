---
title: Descrição geral da segurança na geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Compreender como Gen1 de armazenamento do Azure Data Lake é um armazenamento de grandes volumes de dados mais seguro
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a310851819f70d138a4980b1ab61891fb0b2c311
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959617"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Segurança no Azure Data Lake Storage Gen1
Muitas empresas estão a tirar partido da análise de macrodados para as informações de negócio para os ajudar a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulado, com um número crescente de diversos usuários. É fundamental para uma empresa para se certificar de que a dados empresariais críticos são armazenados de forma mais segura, com o nível correto de acesso concedido a utilizadores individuais. Geração de armazenamento 1 do Azure Data Lake foi concebido para ajudar a satisfazer esses requisitos de segurança. Neste artigo, saiba mais sobre as capacidades de segurança de geração 1 de armazenamento do Data Lake, incluindo:

* Authentication
* Autorização
* Isolamento de rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Gestão de identidades e autenticação
A autenticação é o processo pelo qual a identidade do utilizador é verificada quando o usuário interage com a geração 1 de armazenamento do Data Lake ou com qualquer serviço que se liga a geração 1 de armazenamento do Data Lake. Gerenciamento de identidade e autenticação, usa geração 1 de armazenamento do Data Lake [do Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), uma identidade e acesso gestão solução na cloud completa que simplifica a gestão de utilizadores e grupos.

Cada subscrição do Azure pode ser associada uma instância do Azure Active Directory. Apenas os utilizadores e identidades de serviço são definidas no seu serviço do Azure Active Directory podem aceder à sua conta de geração 1 de armazenamento do Data Lake, utilizando o portal do Azure, ferramentas de linha de comandos, ou por meio de aplicativos de cliente sua organização baseia-se ao utilizar o Data Lake Armazenamento Gen1 SDK. Principais vantagens da utilização do Azure Active Directory como um mecanismo de controlo de acesso centralizado são:

* Gestão de ciclo de vida de identidade simplificada. A identidade de um utilizador ou um serviço (uma identidade principal de serviço) pode ser criada rapidamente e rapidamente revogada simplesmente eliminando ou desabilitar a conta no diretório.
* Autenticação multifator. [Autenticação multifator](../active-directory/authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança de inícios de sessão de utilizador e transações.
* Autenticação a partir de qualquer cliente por meio de um protocolo padrão aberto, como o OAuth ou OpenID.
* Federação com os serviços de diretório empresarial e fornecedores de identidade na cloud.

## <a name="authorization-and-access-control"></a>Controlo de acesso e autorização
Depois do Azure Active Directory autentica um usuário para que o usuário pode acessar a geração 1 de armazenamento do Data Lake, controles de autorização permissões de acesso para geração 1 de armazenamento do Data Lake. Geração 1 de armazenamento do Data Lake separa autorização para atividades relacionadas com a conta e dados relacionados com a da seguinte forma:

* [Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta
* POSIX ACL para aceder aos dados no arquivo

### <a name="rbac-for-account-management"></a>RBAC para a gestão de conta
Quatro funções básicas são definidas para geração 1 do Data Lake armazenamento por predefinição. As funções permitem operações diferentes numa conta de geração 1 de armazenamento do Data Lake através do portal do Azure, cmdlets do PowerShell e REST APIs. As funções de proprietário e contribuinte podem realizar uma variedade de funções de administração na conta. Pode atribuir a função de leitor aos utilizadores que apenas ver os dados de gestão de conta.

![Funções RBAC](./media/data-lake-store-security-overview/rbac-roles.png "funções RBAC")

Observe que embora funções são atribuídas para gestão de conta, algumas funções de afetam o acesso aos dados. Tem de utilizar as ACLs para controlar o acesso para operações que um utilizador pode realizar no sistema de ficheiros. A tabela seguinte mostra um resumo dos direitos de gestão e direitos de acesso de dados para as funções predefinidas.

| Funções | Gestão de direitos | Direitos de acesso de dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhuma |Regidos por ACL |O utilizador não é possível utilizar o portal do Azure ou cmdlets do Azure PowerShell para procurar a geração 1 de armazenamento do Data Lake. O utilizador pode utilizar apenas as ferramentas da linha de comandos. |
| Proprietário |Todos |Todos |A função de proprietário é um Superutilizador. Esta função pode gerir tudo e tem acesso total aos dados. |
| Leitor |Só de leitura |Regidos por ACL |A função de leitor pode ver tudo sobre a gestão de conta, por exemplo, o que é atribuída a que função do utilizador. A função de leitor não pode efetuar quaisquer alterações. |
| Contribuinte |Tudo, exceto adicionar e remover funções |Regidos por ACL |A função Contribuidor pode gerir alguns aspectos de uma conta, por exemplo, implementações e a criação e a gestão de alertas. A função Contribuidor não é possível adicionar ou remover funções. |
| Administrador de Acesso de Utilizador |Adicionar e remover funções |Regidos por ACL |A função de administrador de acesso de utilizador pode gerir o acesso de utilizador para as contas. |

Para obter instruções, consulte [atribuir utilizadores ou grupos de segurança para contas de geração 1 de armazenamento do Data Lake](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Usando as ACLs para operações em sistemas de ficheiros
Geração 1 de armazenamento do Data Lake é um sistema de ficheiros hierárquico, como Hadoop Distributed File System (HDFS) e suporta [POSIX ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ele controla a leitura (r), escrita (w) e executar (x) permissões para recursos para a função de proprietário para o grupo proprietários e para outros utilizadores e grupos. No Data Lake Storage Gen1, as ACLs podem ser ativadas na pasta raiz, em subpastas e em ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto da geração 1 de armazenamento do Data Lake, veja [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).

Recomendamos que definir ACLs para vários utilizadores através da utilização [grupos de segurança](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Adicionar utilizadores a um grupo de segurança e, em seguida, atribua as ACLs para um ficheiro ou pasta para o grupo de segurança. Isto é útil quando deseja fornecer permissões atribuídas, uma vez que é limitado a um máximo de 28 entradas de permissões atribuídas. Para obter mais informações sobre como proteger melhor os dados armazenados no Data Lake Storage Gen1 utilizando grupos de segurança do Azure Active Directory, consulte [atribuir utilizadores ou grupo de segurança como ACLs ao sistema de arquivos de geração 1 de armazenamento do Data Lake](data-lake-store-secure-data.md#filepermissions).

![Lista de permissões de acesso](./media/data-lake-store-security-overview/adl.acl.2.png "permissões de acesso de lista")

## <a name="network-isolation"></a>Isolamento de rede
Utilize a geração 1 de armazenamento do Data Lake para o ajudar a controlar o acesso ao seu arquivo de dados ao nível da rede. Pode estabelecer firewalls e definir um intervalo de endereços IP para os seus clientes fidedignos. Com um intervalo de endereços IP, apenas os clientes que tenham um endereço IP no intervalo definido podem ligar-se a geração 1 de armazenamento do Data Lake.

![As definições da firewall e IP de acesso](./media/data-lake-store-security-overview/firewall-ip-access.png "endereço IP e definições da Firewall")

## <a name="data-protection"></a>Proteção de dados
Geração 1 de armazenamento do Data Lake protege os dados em todo o seu ciclo de vida. Para os dados em trânsito, Gen1 de armazenamento do Data Lake utiliza o protocolo Transport Layer Security (TLS 1.2) de norma da indústria para proteger dados através da rede.

![Encriptação no Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "encriptação no Data Lake Storage Gen1")

Geração 1 de armazenamento do Data Lake também fornece encriptação para dados armazenados na conta. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Se optar por encriptação, os dados armazenados no Data Lake Storage Gen1 são encriptados antes de serem armazenados em suportes de dados persistentes. Nesse caso, Gen1 de armazenamento do Data Lake automaticamente encripta os dados antes de persistir e desencripta-os antes da obtenção, pelo que é completamente transparente para o cliente a aceder aos dados. Não há nenhuma alteração de código necessária do lado do cliente para dados de encriptação/desencriptação.

Gestão de chaves, Gen1 de armazenamento do Data Lake fornece dois modos para gerir as chaves de encriptação mestra (MEKs), que são necessárias para desencriptar todos os dados que são armazenados na geração 1 de armazenamento do Data Lake. Pode optar por permitir que os Data Lake Storage Gen1, faça a gestão de MEKs para, ou optar por manter a propriedade de MEKs com a sua conta do Azure Key Vault. Especifique o modo de gestão de chaves ao criar uma conta de geração 1 de armazenamento do Data Lake. Para obter mais informações sobre como fornecer configuração relacionada com encriptação, consulte [introdução ao Azure Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Registos de atividade e de diagnóstico
Pode usar a atividade ou registos de diagnóstico, dependendo se estiver à procura de registos de atividades relacionadas com a gestão de conta ou atividades relacionadas com dados.

* Atividades relacionadas com a gestão da conta utilizam APIs do Azure Resource Manager e são apresentadas no portal do Azure através de registos de atividades.
* Dados relacionados com atividades utilizar WebHDFS REST APIs e são apresentadas no portal do Azure através de registos de diagnóstico.

### <a name="activity-log"></a>Registo de atividades
Para estar em conformidade com as normas, uma organização poderá necessitar de registos de auditoria adequada das atividades de gestão de conta se precisar de se aprofundar em incidentes específicos. Geração 1 de armazenamento do Data Lake tem monitorização incorporada e regista todas as atividades de gestão de conta.

Para registos de auditoria da gestão de conta, ver e escolha as colunas que pretende iniciar sessão. Também pode exportar os registos de atividades para o armazenamento do Azure.

![Registo de atividades](./media/data-lake-store-security-overview/activity-logs.png "registo de atividades")

Para obter mais informações sobre como trabalhar com registos de atividades, consulte [ver registos de atividades para auditar as ações em recursos](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Registos de diagnósticos
Pode ativar a auditoria de acesso de dados e log de diagnóstico no portal do Azure e enviar os registos para uma conta de armazenamento de Blobs do Azure, um hub de eventos ou os registos do Azure Monitor.

![Os registos de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "os registos de diagnóstico")

Para obter mais informações sobre como trabalhar com registos de diagnóstico com geração 1 de armazenamento do Data Lake, veja [aceder a registos de diagnóstico para a geração 1 de armazenamento do Data Lake](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo
Os clientes empresariais exigem uma plataforma de cloud de análise de dados que é seguro e fácil de usar. Geração 1 de armazenamento do Data Lake é projetado para ajudar a solucionar esses requisitos através da gestão de identidade e autenticação através de integração do Active Directory do Azure, com base na ACL autorização, isolamento de rede, a encriptação de dados em trânsito e em inatividade e auditoria.

Se quiser ver os novos recursos do Data Lake Storage Gen1, envie-nos seus comentários [fórum do UserVoice de geração 1 do Data Lake Storage](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

