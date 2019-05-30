---
title: Protegendo bancos de dados de PaaS no Azure | Documentos da Microsoft
description: 'Saiba mais sobre segurança de base de dados do Azure SQL e SQL Data Warehouse melhores práticas para proteger as suas aplicações móveis e web de PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 057ec69db300c3ec95cbc63bd5056a78c24d5660
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601655"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Melhores práticas para proteger as bases de dados de PaaS no Azure

Neste artigo, discutimos a uma coleção de [Azure SQL Database](../sql-database/sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) melhores práticas de segurança para proteger a sua plataforma-como-serviço (PaaS) aplicações web e móveis. Essas práticas recomendadas são derivadas da nossa experiência com o Azure e as experiências dos clientes, como.

Base de dados SQL do Azure e o SQL Data Warehouse fornecem um serviço de base de dados relacional para seus aplicativos baseados na internet. Vamos examinar os serviços que ajudam a proteger os seus dados e aplicações ao utilizar a base de dados do Azure SQL e SQL Data Warehouse numa implementação de PaaS:

- Autenticação do Active Directory do Azure (em vez da autenticação do SQL Server)
- Firewall do SQL do Azure
- Encriptação de Dados Transparente (TDE)

## <a name="use-a-centralized-identity-repository"></a>Utilizar um repositório centralizado de identidades
Bases de dados SQL do Azure podem ser configurados para utilizar um dos dois tipos de autenticação:

- **Autenticação do SQL** utiliza um nome de utilizador e palavra-passe. Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, pode autenticar qualquer base de dados nesse servidor como o proprietário da base de dados.

- **Autenticação do Azure Active Directory** utiliza identidades geridas pelo Azure Active Directory e é suportada para domínios geridos e integrados. Para utilizar autenticação do Active Directory do Azure, tem de criar outro administrador de servidor chamado o "administrador do Azure AD," o que tem permissão para administrar grupos e utilizadores do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz.

[Autenticação do Azure Active Directory](../active-directory/develop/authentication-scenarios.md) é um mecanismo de ligação a base de dados do Azure SQL e SQL Data Warehouse, utilizando as identidades no Azure Active Directory (AD). O Azure AD fornece uma alternativa à autenticação do SQL Server para que pode parar a proliferação de identidades de utilizador em servidores de base de dados. Autenticação do Azure AD permite-lhe gerir centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft num local central. Gerenciamento de ID central fornece um único local para gerir utilizadores de base de dados e simplifica a gestão de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Vantagens da utilização do Azure AD em vez da autenticação de SQL
- Permite a rotação da palavra-passe num único local.
- Gere as permissões de base de dados a utilizar o Azure externo grupos do AD.
- Elimina a armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportado pelo Azure AD.
- Utilizadores de base de dados utiliza contido para autenticar identidades ao nível da base de dados.
- Suporta a autenticação baseada em tokens para as aplicações ligarem à base de dados SQL.
- Suporta a Federação de domínio com o Active Directory Federation Services (ADFS) ou autenticação de utilizador/palavra-passe nativo para um local do Azure AD sem sincronização de domínio.
- Oferece suporte a conexões do SQL Server Management Studio, que utilizam a autenticação do Active Directory Universal, que inclui [multi-factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA inclui uma autenticação segura com uma variedade de opções de verificação simples — telefonema, mensagem de texto, smart cards com pin ou notificação de aplicação móvel. Para obter mais informações, consulte [autenticação Universal com a base de dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação do Azure AD, veja:

- [Utilizar autenticação do Active Directory do Azure para a autenticação com base de dados SQL, instância gerida ou SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Suporte de autenticação baseada em tokens para o Azure SQL DB utilizando a autenticação do Azure AD](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Para garantir que o Azure Active Directory é uma boa opção para o seu ambiente, veja [funcionalidades do Azure AD e limitações](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Pode criar regras de firewall que especificam intervalos de endereços IP aceitáveis. Estas regras podem ser visadas nos níveis do servidor e a base de dados. Recomendamos que utilize regras de firewall ao nível da base de dados sempre que possível para melhorar a segurança e tornar a sua base de dados mais portátil. Regras de firewall ao nível do servidor são melhor utilizadas para os administradores e quando tiver muitas bases de dados que têm os mesmos requisitos de acesso, mas não quer perder tempo a configurar individualmente a cada base de dados.

Restrições de endereço IP de origem de padrão de base de dados SQL permitem o acesso a partir de qualquer endereço do Azure, incluindo outras subscrições e os inquilinos. Pode restringir a esta opção para permitir apenas os endereços IP acessar a instância. Mesmo com a firewall do SQL e restrições de endereço IP, a autenticação forte é ainda necessária. Ver as recomendações feitas anteriormente no artigo.

Para saber mais sobre as restrições de IP e Firewall do SQL Azure, veja:

- [Controlo de acesso de base de dados SQL e SQL Data Warehouse do Azure](../sql-database/sql-database-control-access.md)
- [Regras de firewall de base de dados SQL e SQL Data Warehouse do Azure](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Encriptar dados Inativos
[Encriptação de dados transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) está ativada por predefinição. TDE transparente encripta os ficheiros de registo e de dados do SQL Server, SQL Database do Azure e Azure SQL Data Warehouse. TDE protege contra um comprometimento de acesso direto à sua cópia de segurança ou os ficheiros. Isto permite-lhe encriptar dados Inativos sem alterar os aplicativos existentes. TDE deve sempre ficar ativado; No entanto, isto não irá parar um atacante a utilizar o caminho de acesso normal. TDE fornece a capacidade para estar em conformidade com várias leis, regulamentações e diretrizes estabelecidas em vários setores.

SQL do Azure gere problemas relacionados com chaves para TDE. Como com TDE, no local especial deve ter cuidado para garantir a capacidade de recuperação e quando mover bases de dados. Cenários mais sofisticados, as chaves podem ser explicitamente gerenciadas no Azure Key Vault através da gestão de chave extensível. Ver [ativar a TDE no SQL Server usando EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Isto também permite para Bring Your Own Key (BYOK) através de capacidade de BYOK de cofres de chaves do Azure.

SQL do Azure fornece encriptação para colunas através de [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isso permite que o acesso apenas aplicativos autorizados para colunas confidenciais. Usar esse tipo de criptografia limita a consultas SQL em colunas encriptadas para valores com base na igualdade.

Encriptação de nível de aplicativo também deve ser utilizada para dados seletivos. Preocupações de soberania de dados, às vezes, podem ser atenuadas ao encriptar dados com uma chave que é mantida no país/região correto. Isto impede que a transferência de dados acidentais até mesmo causar um problema, uma vez que é impossível descriptografar os dados sem a chave, partindo do princípio de que um algoritmo forte seja usado (como AES 256).

Pode usar as precauções adicionais para ajudar a proteger a base de dados, por exemplo, criando um sistema seguro, a criptografia de ativos confidenciais e a criação de uma firewall ao redor dos servidores da base de dados.

## <a name="next-steps"></a>Passos Seguintes
Este artigo apresentou-a uma coleção de base de dados SQL e SQL Data Warehouse melhores práticas de segurança para proteger as suas aplicações móveis e web de PaaS. Para saber mais sobre como proteger as suas implementações de PaaS, consulte:

- [Proteger implementações PaaS](security-paas-deployments.md)
- [Proteger web de PaaS e aplicativos móveis usando os serviços de aplicações do Azure](security-paas-applications-using-app-services.md)
