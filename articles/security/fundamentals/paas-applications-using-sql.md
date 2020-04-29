---
title: Garantir bases de dados paas em Azure Microsoft Docs
description: 'Conheça as melhores práticas de segurança da Azure SQL Database e do SQL Data Warehouse para garantir as suas aplicações web e móveis PaaS. '
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
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124997"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Boas práticas para garantir bases de dados PaaS em Azure

Neste artigo, discutimos uma coleção de bases de [dados Azure SQL](../../sql-database/sql-database-technical-overview.md) e [sQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) de segurança para garantir as suas aplicações web e móveis (PaaS) de plataforma como serviço (PaaS). Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

A Base de Dados Azure SQL e o SQL Data Warehouse fornecem um serviço de base de dados relacional para as suas aplicações baseadas na Internet. Vamos ver serviços que ajudam a proteger as suas aplicações e dados ao utilizar a Base de Dados Azure SQL e o Armazém de Dados SQL numa implementação paaS:

- Autenticação de Diretório Ativo Azure (em vez da autenticação do Servidor SQL)
- Firewall Azure SQL
- Encriptação de Dados Transparente (TDE)

## <a name="use-a-centralized-identity-repository"></a>Utilize um repositório de identidade centralizado
As bases de dados Azure SQL podem ser configuradas para utilizar um de dois tipos de autenticação:

- **A autenticação SQL** utiliza um nome de utilizador e uma palavra-passe. Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Utilizando estas credenciais, pode autenticar qualquer base de dados desse servidor como proprietário da base de dados.

- A **autenticação do Diretório Ativo Azure** utiliza identidades geridas pelo Azure Ative Directory e é suportada por domínios geridos e integrados. Para utilizar a Autenticação de Diretório Ativo Azure, deve criar outro administrador de servidor chamado "Administração AD Azure", que é permitido administrar utilizadores e grupos de Anúncios Azure. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz.

[A autenticação do Diretório Ativo Azure](../../active-directory/develop/authentication-scenarios.md) é um mecanismo de ligação à Base de Dados Azure SQL e ao SQL Data Warehouse utilizando identidades no Diretório Ativo azure (AD). O Azure AD fornece uma alternativa à autenticação do SQL Server para que possa parar a proliferação de identidades de utilizadores através de servidores de base de dados. A autenticação Azure AD permite-lhe gerir centralmente as identidades dos utilizadores de bases de dados e de outros serviços da Microsoft numa localização central. A gestão de IDs centralizada disponibiliza um único local para gerir utilizadores da base de dados e simplifica a gestão de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Benefícios da utilização de AD Azure em vez da autenticação SQL
- Permite a rotação da palavra-passe num único local.
- Gere permissões de base de dados utilizando grupos externos de AD Azure.
- Elimina o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pela AD Azure.
- Os utilizadores da base de dados continham utilizadores de bases de dados para autenticar identidades ao nível da base de dados.
- Suporta a autenticação baseada em token para aplicações que liguem à Base de Dados SQL.
- Suporta a federação de domínio com Serviços da Federação de Diretórios Ativos (ADFS) ou autenticação de utilizador/palavra-passe nativo para um Azure AD local sem sincronização de domínio.
- Suporta ligações do Estúdio de Gestão de Servidores SQL que utilizam autenticação universal de diretório ativo, que inclui [autenticação multi-factor (MFA)](/azure/active-directory/authentication/multi-factor-authentication). A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte [a Autenticação Universal com base de dados SQL e Armazém de Dados SQL](../../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação da Azure AD, consulte:

- [Utilize a autenticação de diretório ativo Azure para autenticação com base de dados SQL, Instância Gerida ou Armazém de Dados SQL](../../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Suporte à autenticação baseado em token para O BD Azure utilizando autenticação Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Para garantir que o Diretório Ativo Azure é um bom ajuste para o seu ambiente, consulte [as funcionalidades e limitações da Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Pode criar regras de firewall que especifiquem gamas de endereços IP aceitáveis. Estas regras podem ser direcionadas para os níveis do servidor e da base de dados. Recomendamos que utilize regras de firewall ao nível da base de dados sempre que possível para aumentar a segurança e tornar a sua base de dados mais portátil. As regras de firewall ao nível do servidor são mais utilizadas para administradores e quando tem muitas bases de dados que têm os mesmos requisitos de acesso, mas não pretende gastar tempo a configurar cada base de dados individualmente.

As restrições de endereço IP de origem padrão da Base de Dados SQL permitem o acesso a partir de qualquer endereço Azure, incluindo outras subscrições e inquilinos. Só pode restringir isto para permitir que os seus endereços IP acedam à instância. Mesmo com as restrições de firewall SQL e endereço IP, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de Firewall Azure SQL e IP, consulte:

- [Base de Dados Azure SQL e controlo de acesso ao Armazém de Dados SQL](../../sql-database/sql-database-manage-logins.md)
- [Regras de firewall da Base de Dados SQL e do SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Encriptar dados inativos
[A Encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) é ativada por padrão. O TDE encripta de forma transparente os dados do SQL Server, da Base de Dados Azure SQL e do Azure SQL Data Warehouse e dos ficheiros de registo. O TDE protege contra um compromisso de acesso direto aos ficheiros ou à sua cópia de segurança. Isto permite-lhe encriptar dados em repouso sem alterar as aplicações existentes. O TDE deve manter-se sempre ativado; no entanto, isto não impedirá um intruso de usar a via de acesso normal. A TDE oferece a capacidade de cumprir muitas leis, regulamentos e orientações estabelecidas em várias indústrias.

O Azure SQL gere questões relacionadas com a chave para o TDE. Tal como acontece com o TDE, há que ter especial cuidado no local para garantir a recuperabilidade e a deslocação das bases de dados. Em cenários mais sofisticados, as chaves podem ser geridas explicitamente no Cofre chave Azure através de uma gestão de chaves extensíveis. Ver [Ativar o TDE no servidor SQL utilizando](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm)o EKM . Isto também permite trazer a sua própria chave (BYOK) através da capacidade de ByOK de Cofres de Chave Azure.

O Azure SQL fornece encriptação para colunas através de [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isto permite apenas acesso autorizado a aplicações a colunas sensíveis. A utilização deste tipo de encriptação limita as consultas SQL para colunas encriptadas a valores baseados na igualdade.

A encriptação do nível de aplicação também deve ser utilizada para dados seletivos. As preocupações com a soberania dos dados podem por vezes ser atenuadas através da encriptação de dados com uma chave que é mantida no país/região correta. Isto impede mesmo a transferência acidental de dados de causar um problema, uma vez que é impossível desencriptar os dados sem a chave, assumindo que um algoritmo forte é usado (como AES 256).

Pode utilizar precauções adicionais para ajudar a proteger a base de dados, como desenhar um sistema seguro, encriptar ativos confidenciais e construir uma firewall em torno dos servidores da base de dados.

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe uma coleção de bases de dados SQL e sQL Data Warehouse de segurança para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre como garantir as suas implementações paas, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Garantir aplicações web e móveis paaS utilizando serviços de aplicações azure](paas-applications-using-app-services.md)
