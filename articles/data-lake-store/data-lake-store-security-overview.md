---
title: Visão geral da segurança no Azure Data Lake Storage Gen1 | Microsoft Docs
description: Entenda como Azure Data Lake Storage Gen1 é um armazenamento de Big Data mais seguro
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974581"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Segurança em Azure Data Lake Storage Gen1
Muitas empresas estão aproveitando o Big Data Analytics for Business insights para ajudá-los a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulamentado, com um número crescente de usuários diversificados. É vital que uma empresa Verifique se os dados críticos de negócios são armazenados de forma mais segura, com o nível correto de acesso concedido a usuários individuais. O Azure Data Lake Storage Gen1 foi projetado para ajudar a atender a esses requisitos de segurança. Neste artigo, saiba mais sobre os recursos de segurança do Data Lake Storage Gen1, incluindo:

* Autenticação
* Autorização
* Isolamento de rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Gerenciamento de identidade e autenticação
A autenticação é o processo pelo qual a identidade de um usuário é verificada quando o usuário interage com Data Lake Storage Gen1 ou com qualquer serviço que se conecta ao Data Lake Storage Gen1. Para gerenciamento de identidade e autenticação, o Data Lake Storage Gen1 usa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), uma solução de nuvem de gerenciamento de acesso e identidade abrangente que simplifica o gerenciamento de usuários e grupos.

Cada assinatura do Azure pode ser associada a uma instância do Azure Active Directory. Somente usuários e identidades de serviço que são definidos em seu serviço de Azure Active Directory podem acessar sua conta de Data Lake Storage Gen1, usando o portal do Azure, ferramentas de linha de comando ou por meio de aplicativos cliente que sua organização cria usando o Data Lake SDK do Gen1 de armazenamento. As principais vantagens de usar Azure Active Directory como um mecanismo de controle de acesso centralizado são:

* Gerenciamento simplificado do ciclo de vida da identidade. A identidade de um usuário ou um serviço (uma identidade de entidade de serviço) pode ser rapidamente criada e revogada rapidamente simplesmente excluindo ou desabilitando a conta no diretório.
* Autenticação multifator. A [autenticação multifator](../active-directory/authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para entradas e transações do usuário.
* Autenticação de qualquer cliente por meio de um protocolo aberto padrão, como OAuth ou OpenID.
* Federação com serviços de diretório da empresa e provedores de identidade de nuvem.

## <a name="authorization-and-access-control"></a>Autorização e controle de acesso
Depois que Azure Active Directory autentica um usuário para que o usuário possa acessar Data Lake Storage Gen1, a autorização controla permissões de acesso para Data Lake Storage Gen1. Data Lake Storage Gen1 separa a autorização para atividades relacionadas à conta e relacionadas a dados da seguinte maneira:

* [Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta
* ACL de POSIX para acessar dados no repositório

### <a name="rbac-for-account-management"></a>RBAC para gerenciamento de conta
Quatro funções básicas são definidas para Data Lake Storage Gen1 por padrão. As funções permitem diferentes operações em uma conta de Data Lake Storage Gen1 por meio do portal do Azure, cmdlets do PowerShell e APIs REST. As funções de proprietário e colaborador podem executar uma variedade de funções de administração na conta. Você pode atribuir a função leitor a usuários que só exibem dados de gerenciamento de conta.

![Funções de RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funções de RBAC")

Observe que embora as funções sejam atribuídas para gerenciamento de conta, algumas funções afetam o acesso aos dados. Você precisa usar ACLs para controlar o acesso a operações que um usuário pode executar no sistema de arquivos. A tabela a seguir mostra um resumo dos direitos de gerenciamento e direitos de acesso de dados para as funções padrão.

| Funções | Direitos de gerenciamento | Direitos de acesso a dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhuma |Governado pela ACL |O usuário não pode usar os cmdlets portal do Azure ou Azure PowerShell para procurar Data Lake Storage Gen1. O usuário pode usar apenas as ferramentas de linha de comando. |
| Proprietário |Tudo |Tudo |A função de proprietário é um superusuário. Essa função pode gerenciar tudo e tem acesso completo aos dados. |
| Leitor |Só de leitura |Governado pela ACL |A função leitor pode exibir tudo sobre o gerenciamento de conta, como qual usuário é atribuído a qual função. A função leitor não pode fazer nenhuma alteração. |
| Contribuinte |Todos, exceto adicionar e remover funções |Governado pela ACL |A função Colaborador pode gerenciar alguns aspectos de uma conta, como implantações e criar e gerenciar alertas. A função colaborador não pode adicionar ou remover funções. |
| Administrador de Acesso de Utilizador |Adicionar e remover funções |Governado pela ACL |A função Administrador de acesso do usuário pode gerenciar o acesso do usuário às contas. |

Para obter instruções, consulte [atribuir usuários ou grupos de segurança a contas de data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Usando ACLs para operações em sistemas de arquivos
Data Lake Storage Gen1 é um sistema de arquivos hierárquico, como o Sistema de Arquivos Distribuído do Hadoop (HDFS), e dá suporte a [ACLs de POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ele controla permissões de leitura (r), gravação (w) e execução (x) para recursos para a função proprietário, para o grupo proprietários e para outros usuários e grupos. No Data Lake Storage Gen1, as ACLs podem ser habilitadas na pasta raiz, em subpastas e em arquivos individuais. Para obter mais informações sobre como as ACLs funcionam no contexto de Data Lake Storage Gen1, consulte [controle de acesso em data Lake Storage Gen1](data-lake-store-access-control.md).

Recomendamos que você defina ACLs para vários usuários usando [grupos de segurança](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Adicione usuários a um grupo de segurança e, em seguida, atribua as ACLs para um arquivo ou pasta a esse grupo de segurança. Isso é útil quando você deseja fornecer permissões atribuídas, porque você está limitado a um máximo de 28 entradas para permissões atribuídas. Para obter mais informações sobre como proteger melhor os dados armazenados em Data Lake Storage Gen1 usando Azure Active Directory grupos de segurança, consulte [atribuir usuários ou grupo de segurança como ACLs ao sistema de arquivos data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Listar permissões de acesso](./media/data-lake-store-security-overview/adl.acl.2.png "Listar permissões de acesso")

## <a name="network-isolation"></a>Isolamento de rede
Use Data Lake Storage Gen1 para ajudar a controlar o acesso ao seu armazenamento de dados no nível da rede. Você pode estabelecer firewalls e definir um intervalo de endereços IP para seus clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que têm um endereço IP dentro do intervalo definido podem se conectar a Data Lake Storage Gen1.

![Configurações de firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configurações de firewall e endereço IP")

## <a name="data-protection"></a>Proteção de dados
O Data Lake Storage Gen1 protege seus dados durante todo o ciclo de vida. Para os dados em trânsito, o Data Lake Storage Gen1 usa o protocolo TLS 1,2 (segurança de camada de transporte) padrão do setor para proteger os dados pela rede.

![Criptografia em Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Criptografia em Data Lake Storage Gen1")

O Data Lake Storage Gen1 também fornece criptografia para os dados armazenados na conta. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Se você optar pela criptografia, os dados armazenados no Data Lake Storage Gen1 serão criptografados antes do armazenamento em mídia persistente. Nesse caso, Data Lake Storage Gen1 criptografa automaticamente os dados antes de persistir e descriptografar os dados antes da recuperação, portanto, é completamente transparente para o cliente que está acessando os dados. Não há nenhuma alteração de código necessária no lado do cliente para criptografar/descriptografar dados.

Para o gerenciamento de chaves, Data Lake Storage Gen1 fornece dois modos para gerenciar suas chaves de criptografia mestras (MEKs), que são necessárias para descriptografar todos os dados armazenados no Data Lake Storage Gen1. Você pode permitir que Data Lake Storage Gen1 gerenciar o MEKs para você ou optar por manter a propriedade do MEKs usando sua conta de Azure Key Vault. Você especifica o modo de gerenciamento de chaves ao criar uma conta de Data Lake Storage Gen1. Para obter mais informações sobre como fornecer configuração relacionada à criptografia, consulte Introdução [ao Azure data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Registos de atividade e de diagnóstico
Você pode usar logs de atividade ou de diagnóstico, dependendo se você está procurando logs para atividades relacionadas ao gerenciamento de conta ou atividades relacionadas a dados.

* As atividades relacionadas ao gerenciamento de contas usam Azure Resource Manager APIs e são apresentadas na portal do Azure por meio de logs de atividade.
* As atividades relacionadas a dados usam as APIs REST do WebHDFS e são exibidas na portal do Azure por meio dos logs de diagnóstico.

### <a name="activity-log"></a>Registo de atividades
Para obedecer às normas, uma organização pode exigir trilhas de auditoria adequadas de atividades de gerenciamento de conta se precisar se aprofundar em incidentes específicos. Data Lake Storage Gen1 tem monitoramento interno e registra todas as atividades de gerenciamento de conta.

Para trilhas de auditoria de gerenciamento de conta, exiba e escolha as colunas que você deseja registrar em log. Você também pode exportar logs de atividade para o armazenamento do Azure.

![Registo de atividades](./media/data-lake-store-security-overview/activity-logs.png "Registo de atividades")

Para obter mais informações sobre como trabalhar com logs de atividade, consulte [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Registos de diagnóstico
Você pode habilitar a auditoria de acesso a dados e o log de diagnóstico no portal do Azure e enviar os logs para uma conta de armazenamento de BLOBs do Azure, um hub de eventos ou logs de Azure Monitor.

![Registos de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registos de diagnóstico")

Para obter mais informações sobre como trabalhar com logs de diagnóstico com Data Lake Storage Gen1, consulte [acessando logs de diagnóstico para data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo
Os clientes corporativos exigem uma plataforma de nuvem de análise de dados que seja segura e fácil de usar. O Data Lake Storage Gen1 foi projetado para ajudar a resolver esses requisitos por meio de gerenciamento de identidades e autenticação por meio da integração de Azure Active Directory, autorização baseada em ACL, isolamento de rede, criptografia de dados em trânsito e em repouso e auditoria.

Se você quiser ver novos recursos no Data Lake Storage Gen1, envie-nos seus comentários no [fórum data Lake Storage Gen1 UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Ver também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
