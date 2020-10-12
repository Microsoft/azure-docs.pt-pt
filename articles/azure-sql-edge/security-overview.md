---
title: Borda Azure SQL Segura
description: Conheça a segurança em Azure SQL Edge
keywords: SQL Edge, segurança
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 17e3e8dca1c03f9783c0ca94350bb8a4ba5aca64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938647"
---
# <a name="securing-azure-sql-edge"></a>Segurança Azure SQL Edge

Com o aumento da adoção de computação IoT e Edge entre indústrias, há um aumento no número de dispositivos e nos dados gerados a partir destes dispositivos. O aumento do volume de dados e o número de pontos finais do dispositivo representam um desafio significativo em termos de segurança dos dados e dos dispositivos. 

O Azure SQL Edge oferece múltiplas funcionalidades e capacidades que facilitam relativamente a segurança dos dados IoT dentro das bases de dados do SQL Server. O Azure SQL Edge é construído com o mesmo motor SQL que alimenta o Microsoft SQL Server e o Azure SQL, partilhando as mesmas capacidades de segurança, o que facilita a extensão das mesmas políticas e práticas de segurança da nuvem para a borda.

Tal como o Microsoft SQL Server e o Azure SQL, a garantia de implementações do Azure SQL Edge pode ser vista como uma série de passos que envolvem quatro áreas: a plataforma, a autenticação, os objetos (incluindo dados) e as aplicações que acedem ao sistema. 

## <a name="platform-and-system-security"></a>Segurança da plataforma e do sistema

A plataforma do Azure SQL Edge inclui o anfitrião de estiva física, o sistema operativo no hospedeiro e os sistemas de ligação de rede que ligam o dispositivo físico a aplicações e clientes. 

A implementação da segurança da plataforma começa por manter os utilizadores não autorizados fora da rede. Algumas das melhores práticas incluem, mas não se limita a:
- Implementação de regras de firewall para garantir a política de segurança organizacional. 
- Certifique-se de que o sistema operativo do dispositivo físico tem todas as atualizações de segurança mais recentes aplicadas. 
- Especificar e restringir portas hospedeiras que estão a usar para Azure SQL Edge
- Garantindo que o controlo adequado do acesso é aplicado a todos os volumes de dados que acolhem dados do Azure SQL Edge. 

Para obter mais informações sobre os protocolos de rede Azure SQL Edge e pontos finais TDS consulte, [Protocolos de Rede e Pontos Finais TDS](https://docs.microsoft.com//previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Autenticação e autorização 

### <a name="authentication"></a>Autenticação  
A autenticação é o processo de provar que o utilizador é quem diz ser. A Azure SQL Edge suporta atualmente apenas o `SQL Authentication` mecanismo.

- *Autenticação SQL:*

    A autenticação SQL refere-se à autenticação de um utilizador ao ligar-se ao Azure SQL Edge utilizando o nome de utilizador e a palavra-passe. A palavra-passe de login SQL **sa** deve ser especificada durante a implementação do SQL Edge. Depois disso, podem ser criados logins e utilizadores SQL adicionais pela administração do servidor, que permitem aos utilizadores conectar-se usando o nome de utilizador e a palavra-passe.

    Para obter mais informações sobre a criação e gestão de logins e utilizadores em SQL Edge, consulte [Criar um Utilizador de Login](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) e Criar Base de [Dados](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorização   

A autorização refere-se às permissões atribuídas a um utilizador dentro de uma base de dados em Azure SQL Edge, e determina o que o utilizador está autorizado a fazer. As permissões são controladas adicionando contas de utilizador às [funções de base](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) de dados e atribuindo permissões de nível de base a essas funções ou concedendo ao utilizador [determinadas permissões ao nível dos objetos](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Para mais informações, consulte [Logins e utilizadores.](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

Como uma boa prática, crie papéis personalizados quando necessário. Adicione os utilizadores ao papel com os privilégios menos necessários para fazer a sua função de trabalho. Não adtribua permissões diretamente aos utilizadores. A conta de administração do servidor é um membro da função de db_owner incorporada, que tem permissões extensivas e só deve ser concedida a poucos utilizadores com funções administrativas. Para aplicações, utilize o [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo ou utilizar [funções de aplicação](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Esta prática garante que a aplicação que se liga à base de dados tem os privilégios menos necessários pela aplicação. Seguir estas boas práticas também fomenta a separação de deveres.

## <a name="database-object-security"></a>Segurança de objetos de base de dados

Os principais são os indivíduos, grupos e processos que têm acesso ao SQL Edge. "Securables" são o servidor, base de dados e objetos que a base de dados contém. Cada um tem um conjunto de permissões que podem ser configuradas para ajudar a reduzir a área da superfície. A tabela seguinte contém informações sobre princípios e garantias.

|Para obter informações sobre|Consulte|  
|---------------------------|---------|  
|Utilizadores, funções e processos de servidor e base de dados|[Motor de base de dados dos principais](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Segurança de objetos de servidor e de base de dados|[Garantias](https://docs.microsoft.com/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Encriptação e certificados  
 
A encriptação não resolve problemas de controlo de acesso. No entanto, aumenta a segurança limitando a perda de dados mesmo na rara ocorrência de que os controlos de acesso são ignorados. Por exemplo, se o computador anfitrião da base de dados estiver mal configurado e um utilizador malicioso obtiver dados sensíveis, como números de cartão de crédito, essa informação roubada pode ser inútil se for encriptada. A tabela seguinte contém mais informações sobre encriptação em Azure SQL Edge.  
  
|Para obter informações sobre|Consulte|  
|---------------------------|---------|  
|Implementação de ligações seguras|[Encriptação de Ligações](https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections)|  
|Funções de encriptação|[Funções Criptográficas &#40;&#41;Transact-SQL ](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Encriptação de dados em repouso|[Encriptação de Dados Transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Sempre Encriptado|[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> As limitações de segurança descritas para [o SQL Server em Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-security-overview) também se aplicam ao Azure SQL Edge. 


> [!NOTE]
> A Azure SQL Edge não inclui o utilitário mssql-conf. Todas as configurações, incluindo a configuração relacionada com encriptação, devem ser executadas através do [ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file) ou [das variáveis ambientais](configure.md#configure-by-using-environment-variables). 


Semelhante ao Azure SQL e ao Microsoft SQL Server, o Azure SQL Edge fornece o mesmo mecanismo para criar e utilizar certificados para melhorar a segurança de objetos e ligações. Para obter mais informações, consulte, [CRIE CERTIFICADO (TRANSACT-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Segurança de aplicações

### <a name="client-programs"></a>Programas de clientes

As melhores práticas de segurança da Azure SQL Edge incluem a escrita de aplicações seguras para clientes. Para obter mais informações sobre como ajudar a proteger as aplicações do cliente na camada de rede, consulte a [Configuração da Rede do Cliente.](https://docs.microsoft.com/sql/database-engine/configure-windows/client-network-configuration)

### <a name="sql-server-security-catalog-views-and-functions"></a>Vistas e funções do catálogo de segurança SQL Server  
 As informações de segurança são expostas em várias vistas e funções otimizadas para desempenho e utilidade. A tabela seguinte contém informações sobre vistas e funções de segurança.  
  
|Funções e vistas|Ligações|  
|---------------------------|---------|  
|Vistas de catálogo de segurança, que devolvem informações sobre permissões de nível de base de dados e servidores, principais, funções, e assim por diante. Além disso, existem vistas de catálogo que fornecem informações sobre chaves de encriptação, certificados e credenciais.|[Vistas do catálogo de segurança &#40;&#41;Transact-SQL ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Funções de segurança, que devolvem informações sobre o utilizador atual, permissões e esquemas.|[Funções de Segurança &#40;&#41;Transact-SQL ](https://docs.microsoft.com/sql/t-sql/functions/security-functions-transact-sql)|  
|Pontos de vista dinâmicos de gestão de segurança.|[Vistas e funções dinâmicas relacionadas com a segurança &#40;&#41;Transact-SQL ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Auditoria 

O Azure SQL Edge fornece os mesmos mecanismos de auditoria que o SQL Server. Para obter mais informações, consulte [a Auditoria do Servidor SQL (Motor de Base de Dados)](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Passos seguintes

- [Começar com funcionalidades de segurança](https://docs.microsoft.com/sql/linux/sql-server-linux-security-get-started)
- [Executando Azure SQL Edge como um utilizador sem raízes](configure.md#run-azure-sql-edge-as-non-root-user)
- [Centro de Segurança Azure para IoT](https://docs.microsoft.com/azure/asc-for-iot/overview)

