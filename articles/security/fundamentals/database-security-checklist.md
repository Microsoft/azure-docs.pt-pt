---
title: Lista de verificação de segurança do banco de dados do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de lista de verificação para segurança de banco de dados do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: bf11d7a9dbaa37db521da67deddbed977cc1d251
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780542"
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança do banco de dados do Azure

Para ajudar a melhorar a segurança, o banco de dados do Azure inclui vários controles de segurança internos que você pode usar para limitar e controlar o acesso.

Estas incluem:

-   Um firewall que permite criar regras de [Firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) limitando a conectividade por endereço IP,
-   Firewall de nível de servidor acessível por meio do portal do Azure
-   Regras de firewall no nível de banco de dados acessíveis do SSMS
-   Conectividade segura com o banco de dados usando cadeias de conexão seguras
-   Usar o gerenciamento de acesso
-   Encriptação de dados
-   Auditoria da Base de Dados SQL
-   Deteção de ameaças da Base de Dados SQL

## <a name="introduction"></a>Introdução
A computação em nuvem requer novos paradigmas de segurança que não são familiares para muitos usuários de aplicativos, administradores de banco de dados e programadores. Como resultado, algumas organizações estão hesitars a implementar uma infraestrutura de nuvem para gerenciamento de dados devido a riscos de segurança percebidos. No entanto, grande parte dessa preocupação pode ser aliviada por meio de uma melhor compreensão dos recursos de segurança incorporados ao Microsoft Azure e Banco de Dados SQL do Microsoft Azure.

## <a name="checklist"></a>Lista de verificação
Recomendamos que você leia o artigo [práticas recomendadas de segurança do banco de dados do Azure](https://docs.microsoft.com/azure/security/fundamentals/database-best-practices) antes de examinar esta lista de verificação. Você poderá obter o máximo desta lista de verificação depois de entender as práticas recomendadas. Você pode usar essa lista de verificação para certificar-se de que você resolveu os problemas importantes na segurança de banco de dados do Azure.


|Categoria da lista de verificação| Descrição|
| ------------ | -------- |
|**Proteger dados**||
| <br> Criptografia em movimento/trânsito| <ul><li>[Segurança da camada de transporte](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), para criptografia de dados quando os dados estão sendo movidos para as redes.</li><li>O banco de dados requer uma comunicação segura de clientes com base no protocolo [TDS (Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) sobre TLS (segurança da camada de transporte).</li></ul> |
|<br>Encriptação inativa| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242), quando os dados inativos são armazenados fisicamente em qualquer formato digital.</li></ul>|
|**Controlar o acesso**||  
|<br> Acesso à Base de Dados | <ul><li>[Autenticação](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) do (Autenticação de Azure Active Directory) A autenticação do AD usa identidades gerenciadas pelo Azure Active Directory.</li><li>A [autorização](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) concede aos usuários os privilégios mínimos necessários.</li></ul> |
|<br>Acesso ao aplicativo| <ul><li>[Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131) (Usando a política de segurança, ao mesmo tempo restringindo o acesso em nível de linha com base na identidade, na função ou no contexto de execução de um usuário).</li><li>[Máscara de dados dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (Usando a política de & de permissão, o limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios)</li></ul>|
|**Monitoramento proativo**||  
| <br>Rastreando & detectando| <ul><li>A [auditoria](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) rastreia eventos de banco de dados e os grava em um log de auditoria/log de atividades em sua [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Acompanhe a integridade do banco de dados do Azure usando [Azure monitor logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>A [detecção de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados. </li></ul> |
|<br>Centro de Segurança do Azure| <ul><li>[Monitoramento de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) Use a central de segurança do Azure como uma solução de monitoramento de segurança centralizada para SQL e outros serviços do Azure.</li></ul>|       

## <a name="conclusion"></a>Conclusão
O banco de dados do Azure é uma plataforma de banco de dados robusta, com uma gama completa de recursos de segurança que atendem a muitos requisitos de conformidade organizacional e regulatório. Você pode proteger facilmente os dados controlando o acesso físico aos seus dados e usando uma variedade de opções de segurança de dados no nível de arquivo, coluna ou linha com Transparent Data Encryption, criptografia no nível da célula ou segurança em nível de linha. O Always Encrypted também permite operações em relação a dados criptografados, simplificando o processo de atualizações de aplicativos. Por sua vez, o acesso à auditoria de logs de atividade do banco de dados SQL fornece as informações necessárias, permitindo que você saiba como e quando os dados são acessados.

## <a name="next-steps"></a>Passos Seguintes
Pode melhorar a proteção da sua base de dados contra utilizadores mal intencionados ou o acesso não autorizado com apenas alguns passos simples. Neste tutorial, ficará a saber como:

- Configure [as regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para seu servidor e ou banco de dados.
- Proteja seus dados com [criptografia](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Habilite a [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

