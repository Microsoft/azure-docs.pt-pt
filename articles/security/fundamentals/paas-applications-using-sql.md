---
title: Protegendo bancos de dados PaaS no Azure | Microsoft Docs
description: 'Saiba mais sobre o banco de dados SQL do Azure e as práticas recomendadas de segurança SQL Data Warehouse para proteger seus aplicativos Web e móveis de PaaS. '
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
ms.openlocfilehash: f7d993799fed637fbec55afc8f06d90c8fc6910f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726786"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Práticas recomendadas para proteger bancos de dados PaaS no Azure

Neste artigo, discutiremos uma coleção do [banco de dados SQL do Azure](../../sql-database/sql-database-technical-overview.md) e [SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) práticas recomendadas de segurança para proteger seus aplicativos Web e móveis de PaaS (plataforma como serviço). Essas práticas recomendadas derivam de nossa experiência com o Azure e as experiências de clientes como você.

O banco de dados SQL do Azure e o SQL Data Warehouse fornecem um serviço de banco de dados relacional para seus aplicativos baseados na Internet. Vamos dar uma olhada nos serviços que ajudam a proteger seus aplicativos e dados ao usar o Azure SQL Database e SQL Data Warehouse em uma implantação de PaaS:

- Autenticação Azure Active Directory (em vez de autenticação SQL Server)
- Firewall do SQL do Azure
- Encriptação de Dados Transparente (TDE)

## <a name="use-a-centralized-identity-repository"></a>Usar um repositório de identidades centralizado
Os bancos de dados SQL do Azure podem ser configurados para usar um dos dois tipos de autenticação:

- A **autenticação do SQL** usa um nome de usuário e senha. Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Usando essas credenciais, você pode se autenticar em qualquer banco de dados no servidor como o proprietário do banco de dados.

- A **autenticação Azure Active Directory** usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Para usar a autenticação Azure Active Directory, você deve criar outro administrador de servidor chamado de "administrador do Azure AD", que tem permissão para administrar usuários e grupos do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz.

[Azure Active Directory autenticação](../../active-directory/develop/authentication-scenarios.md) é um mecanismo de conexão com o banco de dados SQL do Azure e SQL data warehouse usando identidades no Azure Active Directory (AD). O Azure AD fornece uma alternativa à autenticação SQL Server para que você possa interromper a proliferação de identidades de usuário entre servidores de banco de dados. A autenticação do Azure AD permite que você gerencie centralmente as identidades dos usuários do banco de dados e outros serviços da Microsoft em um local central. O gerenciamento de identificação central fornece um único local para gerenciar usuários de banco de dados e simplifica o gerenciamento de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Benefícios do uso do Azure AD em vez da autenticação do SQL
- Permite a rotação de senha em um único lugar.
- Gerencia permissões de banco de dados usando grupos externos do Azure AD.
- Elimina o armazenamento de senhas habilitando a autenticação integrada do Windows e outras formas de autenticação com suporte do Azure AD.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados SQL.
- Dá suporte à Federação de domínio com ADFS (Serviços de Federação do Active Directory (AD FS)) ou autenticação nativa de usuário/senha para um Azure AD local sem sincronização de domínio.
- Dá suporte a conexões de SQL Server Management Studio que usam Active Directory autenticação universal, que inclui a [autenticação multifator (MFA)](/azure/active-directory/authentication/multi-factor-authentication). A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para obter mais informações, consulte [autenticação universal com o banco de dados SQL e SQL data warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação do Azure AD, consulte:

- [Usar a autenticação Azure Active Directory para autenticação com o banco de dados SQL, Instância Gerenciada ou SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)](../../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Suporte à autenticação baseada em token para o BD SQL do Azure usando a autenticação do Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Para garantir que Azure Active Directory seja uma boa opção para seu ambiente, consulte [limitações e recursos do Azure ad](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Você pode criar regras de firewall que especificam intervalos de endereços IP aceitáveis. Essas regras podem ser direcionadas tanto para os níveis de servidor quanto de banco de dados. É recomendável usar regras de firewall no nível de banco de dados sempre que possível para aumentar a segurança e tornar seu banco de dados mais portátil. As regras de firewall no nível de servidor são mais bem usadas para os administradores e quando você tem muitos bancos de dados que têm os mesmos requisitos de acesso, mas não quer gastar tempo configurando cada banco individualmente.

As restrições de endereço IP de origem padrão do banco de dados SQL permitem o acesso de qualquer endereço do Azure, incluindo outras assinaturas e locatários. Você pode restringir isso para permitir que apenas seus endereços IP acessem a instância. Mesmo com o Firewall do SQL e as restrições de endereço IP, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de IP e o Firewall do SQL do Azure, consulte:

- [Banco de dados SQL do Azure e controle de acesso de SQL Data Warehouse](../../sql-database/sql-database-control-access.md)
- [Regras de firewall do banco de dados SQL do Azure e SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Criptografar dados em repouso
O [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) está habilitado por padrão. O TDE criptografa de forma transparente SQL Server, banco de dados SQL do Azure e arquivos de log e de SQL Data Warehouse do Azure. O TDE protege contra um comprometimento do acesso direto aos arquivos ou ao seu backup. Isso permite que você criptografe dados em repouso sem alterar os aplicativos existentes. TDE sempre deve estar habilitado; no entanto, isso não interromperá um invasor usando o caminho de acesso normal. O TDE fornece a capacidade de cumprir muitas leis, regulamentos e diretrizes estabelecidas em vários setores.

O SQL do Azure gerencia os principais problemas relacionados ao TDE. Assim como com TDE, deve-se tomar cuidado especial local para garantir a capacidade de recuperação e ao mover bancos de dados. Em cenários mais sofisticados, as chaves podem ser explicitamente gerenciadas em Azure Key Vault por meio do gerenciamento extensível de chaves. Consulte [habilitar TDE em SQL Server usando EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Isso também permite Bring Your Own Key (BYOK) por meio do recurso BYOK do Azure Key Vaults.

O SQL do Azure fornece criptografia para colunas por meio de [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isso permite que somente aplicativos autorizados acessem colunas confidenciais. O uso desse tipo de criptografia limita as consultas SQL de colunas criptografadas a valores baseados em igualdade.

A criptografia no nível do aplicativo também deve ser usada para dados seletivos. Às vezes, as preocupações com a soberania de dados podem ser atenuadas com a criptografia de dados com uma chave que é mantida no país/região correto. Isso evita que até mesmo a transferência de dados acidental cause um problema, já que é impossível descriptografar os dados sem a chave, supondo que um algoritmo forte seja usado (como o AES 256).

Você pode usar precauções adicionais para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em todos os servidores de banco de dados.

## <a name="next-steps"></a>Passos Seguintes
Este artigo apresentou a você uma coleção de banco de dados SQL e SQL Data Warehouse práticas recomendadas de segurança para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteger implementações PaaS](paas-deployments.md)
- [Protegendo aplicativos Web e móveis de PaaS usando serviços Azure Apps](paas-applications-using-app-services.md)
