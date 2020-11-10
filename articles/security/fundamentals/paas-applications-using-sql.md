---
title: Segurança das Bases de Dados paas em Azure Microsoft Docs
description: 'Saiba mais sobre a Azure SQL Database e a Azure Synapse Analytics as melhores práticas de segurança para garantir as suas aplicações web e móveis PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 278812754c636d434bf579c0408832f1e99d3445
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408078"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Melhores práticas para assegurar bases de dados paaS em Azure

Neste artigo, discutimos uma coleção de bases de [dados Azure SQL](../../azure-sql/database/sql-database-paas-overview.md) e a [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) para as melhores práticas de segurança para garantir as suas aplicações web e móveis de plataforma como serviço (PaaS). Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

A Azure SQL Database e a Azure Synapse Analytics fornecem um serviço de base de dados relacional para as suas aplicações baseadas na Internet. Vamos olhar para os serviços que ajudam a proteger as suas aplicações e dados ao utilizar a Base de Dados Azure SQL e a Azure Synapse Analytics numa implementação paaS:

- Autenticação do Azure Ative Directory (em vez da autenticação do SQL Server)
- Firewall Azure SQL
- Encriptação de Dados Transparente (TDE)

## <a name="use-a-centralized-identity-repository"></a>Use um repositório de identidade centralizado

A base de dados Azure SQL pode ser configurada para utilizar um de dois tipos de autenticação:

- **A autenticação SQL** utiliza um nome de utilizador e uma palavra-passe. Quando criou o servidor para a sua base de dados, especificou um login de "administrador do servidor" com um nome de utilizador e senha. Utilizando estas credenciais, pode autenticar qualquer base de dados nesse servidor como o proprietário da base de dados.

- **A autenticação do Azure Ative Directory** utiliza identidades geridas pelo Azure Ative Directory e é suportada por domínios geridos e integrados. Para utilizar a Autenticação do Diretório Ativo Azure, tem de criar outro administrador de servidor chamado "Administrador AD AZure", que é permitido administrar utilizadores e grupos de AD Azure. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz.

[A autenticação do Azure Ative Directory](../../active-directory/develop/authentication-vs-authorization.md) é um mecanismo de ligação à Base de Dados Azure SQL e à Azure Synapse Analytics utilizando identidades no Azure Ative Directory (AD). O Azure AD fornece uma alternativa à autenticação do SQL Server para que possa parar a proliferação de identidades dos utilizadores através dos servidores de bases de dados. A autenticação AZure AD permite-lhe gerir centralmente as identidades dos utilizadores de bases de dados e outros serviços da Microsoft numa localização central. A gestão de IDs centralizada disponibiliza um único local para gerir utilizadores da base de dados e simplifica a gestão de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Benefícios da utilização do Azure AD em vez da autenticação SQL

- Permite a rotação da palavra-passe num único local.
- Gere permissões de base de dados utilizando grupos Azure AD externos.
- Elimina o armazenamento de palavras-passe, permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure AD.
- Utiliza utilizadores de bases de dados contidos para autenticar identidades ao nível da base de dados.
- Suporta a autenticação baseada em fichas para aplicações que se ligam à Base de Dados SQL.
- Suporta a federação de domínio com serviços da Federação de Diretórios Ativos (ADFS) ou autenticação nativa do utilizador/palavra-passe para um AD Azure local sem sincronização de domínio.
- Suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Diretório Ativo, que inclui [autenticação multi-factor (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md). A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte [a Autenticação Universal com Base de Dados SQL e Azure Synapse Analytics.](../../azure-sql/database/authentication-mfa-ssms-overview.md)

Para saber mais sobre a autenticação AZure AD, consulte:

- [Utilize autenticação de diretório ativo Azure para autenticação com base de dados SQL, Instância Gerida ou Azure Synapse Analytics](../../azure-sql/database/authentication-aad-overview.md)
- [Autenticação para Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Suporte de autenticação baseado em token para Azure SQL Database usando a autenticação AD Azure](../../azure-sql/database/authentication-aad-overview.md)

> [!NOTE]
> Para garantir que o Azure Ative Directory é um bom ajuste para o seu ambiente, consulte [as funcionalidades e limitações da AD Azure](../../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP

Pode criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Estas regras podem ser direcionadas tanto para os níveis do servidor como para a base de dados. Recomendamos a utilização de regras de firewall ao nível da base de dados sempre que possível para melhorar a segurança e tornar a sua base de dados mais portátil. As regras de firewall ao nível do servidor são mais utilizadas para administradores e quando tem muitas bases de dados que têm os mesmos requisitos de acesso, mas não quer gastar tempo a configurar cada base de dados individualmente.

As restrições de endereço IP de fonte padrão SQL Database permitem o acesso a partir de qualquer endereço Azure, incluindo outras subscrições e inquilinos. Pode restringir isto apenas para permitir que os seus endereços IP acedam ao caso. Mesmo com as restrições de firewall e IP SQL, a autenticação forte ainda é necessária. Veja as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de Firewall e IP do Azure SQL, consulte:

- [Azure SQL Database e Azure Synapse Analytics controle de acesso](../../azure-sql/database/logins-create-manage.md)
- [Azure SQL Database e regras de firewall Azure Synapse Analytics](../../azure-sql/database/firewall-configure.md)

## <a name="encrypt-data-at-rest"></a>Encriptar dados inativos

[A Encriptação de Dados Transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) é ativada por padrão. O TDE encripta de forma transparente os dados do SQL Server, Azure SQL Database e Azure Synapse Analytics e ficheiros de registo. O TDE protege contra um compromisso de acesso direto aos ficheiros ou à sua cópia de segurança. Isto permite-lhe encriptar dados em repouso sem alterar as aplicações existentes. O TDE deve manter-se sempre ativado; no entanto, isto não irá parar um intruso usando o caminho de acesso normal. O TDE fornece a capacidade de cumprir muitas leis, regulamentos e diretrizes estabelecidas em várias indústrias.

A Azure SQL gere questões relacionadas com o TDE. Tal como acontece com o TDE, devem ser tomados cuidados especiais no local para garantir a recuperabilidade e ao deslocar bases de dados. Em cenários mais sofisticados, as chaves podem ser geridas explicitamente no Azure Key Vault através de uma gestão de chaves extensível. Consulte [o Enable TDE no SQL Server utilizando o EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Isto também permite trazer a sua própria chave (BYOK) através da capacidade byok key Key Vaults Azure.

O Azure SQL fornece encriptação para colunas através [de Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isto permite apenas acesso a colunas sensíveis a aplicações autorizadas. A utilização deste tipo de encriptação limita as consultas SQL para colunas encriptadas a valores baseados na igualdade.

A encriptação do nível da aplicação também deve ser usada para dados seletivos. As preocupações com a soberania dos dados podem por vezes ser atenuadas através da encriptação de dados com uma chave que é mantida no país/região correto. Isto evita mesmo que a transferência acidental de dados cause um problema, uma vez que é impossível desencriptar os dados sem a chave, assumindo que é utilizado um algoritmo forte (como o AES 256).

Pode usar precauções adicionais para ajudar a proteger a base de dados, como desenhar um sistema seguro, encriptar ativos confidenciais e construir uma firewall em torno dos servidores da base de dados.

## <a name="next-steps"></a>Passos seguintes

Este artigo apresentou-o a uma coleção de bases de dados SQL e a azure Synapse Analytics para as melhores práticas de segurança para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre a segurança das suas implementações paaS, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Garantir aplicações web e móveis paaS usando serviços de aplicações Azure](paas-applications-using-app-services.md)