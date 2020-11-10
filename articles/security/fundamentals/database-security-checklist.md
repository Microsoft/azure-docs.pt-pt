---
title: Lista de verificação de segurança da base de dados Azure Microsoft Docs
description: Utilize a lista de verificação de segurança da base de dados Azure para se certificar de que aborda problemas importantes de segurança da computação em nuvem.
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
ms.openlocfilehash: e9845eaf6d84caad3ddb8c7e615cb72f71c91f3b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412838"
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança da base de dados Azure

Para ajudar a melhorar a segurança, a Azure Database inclui uma série de controlos de segurança incorporados que pode utilizar para limitar e controlar o acesso.

Estas incluem:

-    Uma firewall que lhe permite criar [regras de firewall](../../azure-sql/database/firewall-configure.md) que limitam a conectividade por endereço IP,
-    Firewall de nível de servidor acessível a partir do portal Azure
-    Regras de firewall ao nível da base de dados acessíveis a partir de SSMS
-    Conectividade segura à sua base de dados usando cadeias de ligação seguras
-    Use a gestão de acessos
-    Encriptação de dados
-    Auditoria da Base de Dados SQL
-    Deteção de ameaças de base de dados SQL

## <a name="introduction"></a>Introdução
A computação em nuvem requer novos paradigmas de segurança que não são familiares a muitos utilizadores de aplicações, administradores de bases de dados e programadores. Como resultado, algumas organizações hesitam em implementar uma infraestrutura em nuvem para a gestão de dados devido a riscos de segurança percebidos. No entanto, grande parte desta preocupação pode ser atenuada através de uma melhor compreensão das funcionalidades de segurança incorporadas no Microsoft Azure e microsoft Azure SQL Database.

## <a name="checklist"></a>Lista de Verificação
Recomendamos que leia o artigo [Azure Database Security Best Practices](../../azure-sql/database/security-best-practice.md)  antes de rever esta lista de verificação. Poderá tirar o máximo partido desta lista de verificação depois de compreender as melhores práticas. Em seguida, pode utilizar esta lista de verificação para se certificar de que abordou os problemas importantes na segurança da base de dados Azure.


|Categoria lista de verificação| Description|
| ------------ | -------- |
|**Proteger dados**||
| <br> Encriptação em Movimento/Trânsito| <ul><li>[Transport Layer Security](/windows-server/security/tls/transport-layer-security-protocol), para encriptação de dados quando os dados estão a mover-se para as redes.</li><li>A base de dados requer uma comunicação segura dos clientes com base no protocolo [TDS (Tabular Data Stream)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) sobre tLS (Transport Layer Security).</li></ul> |
|<br>Encriptação inativa| <ul><li>[Encriptação de dados transparentes,](../../azure-sql/database/transparent-data-encryption-tde-overview.md)quando os dados inativos são armazenados fisicamente em qualquer forma digital.</li></ul>|
|**Controlar Acesso**||  
|<br> Acesso à Base de Dados | <ul><li>[A autenticação autenticação](../../azure-sql/database/logins-create-manage.md) (Autenticação ativa do Diretório Azure) utiliza identidades geridas pelo Azure Ative Directory.</li><li>[A autorização](../../azure-sql/database/logins-create-manage.md) concede aos utilizadores os privilégios menos necessários.</li></ul> |
|<br>Acesso à Aplicação| <ul><li>[Segurança de nível](/sql/relational-databases/security/row-level-security) de linha (Usando a Política de Segurança, ao mesmo tempo que restringe o acesso ao nível da linha com base no contexto de identidade, papel ou execução de um utilizador).</li><li>[Máscara dinâmica de dados](../../azure-sql/database/dynamic-data-masking-overview.md) (Usando a Política de Permissão &, limita a exposição sensível aos dados mascarando-a a utilizadores não privilegiados)</li></ul>|
|**Monitorização Proativa**||  
| <br>Deteção de & de rastreio| <ul><li>[A auditoria](../../azure-sql/database/auditing-overview.md) rastreia os eventos da base de dados e escreve-os para um registo de auditoria/registo de atividade na sua [conta de Armazenamento Azure](../../storage/common/storage-account-create.md).</li><li>Rastreio a saúde da base de dados de Azure utilizando [registos de atividade do monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[A Deteção de Ameaças](../../azure-sql/database/threat-detection-configure.md) deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados. </li></ul> |
|<br>Centro de Segurança do Azure| <ul><li>[Monitorização de Dados](../../security-center/security-center-remediate-recommendations.md) Utilize o Azure Security Center como uma solução centralizada de monitorização de segurança para o SQL e outros serviços Azure.</li></ul>|        

## <a name="conclusion"></a>Conclusão
A Azure Database é uma plataforma de base de dados robusta, com uma gama completa de funcionalidades de segurança que cumprem muitos requisitos de conformidade organizacional e regulamentar. Pode proteger facilmente os dados controlando o acesso físico aos seus dados e utilizando uma variedade de opções para a segurança dos dados no nível de ficheiros, colunas ou linha com encriptação de dados transparentes, Cell-Level Encriptação ou segurança Row-Level. Sempre encriptado também permite operações contra dados encriptados, simplificando o processo de atualizações de aplicações. Por sua vez, o acesso a registos de auditoria da atividade da SQL Database fornece-lhe as informações de que necessita, permitindo-lhe saber como e quando os dados são acedidos.

## <a name="next-steps"></a>Passos seguintes
Pode melhorar a proteção da sua base de dados contra utilizadores mal intencionados ou o acesso não autorizado com apenas alguns passos simples. Neste tutorial, ficará a saber como:

- Configurar [regras de firewall](../../azure-sql/database/firewall-configure.md) para o seu servidor e base de dados.
- Proteja os seus dados com [encriptação.](/sql/relational-databases/security/encryption/sql-server-encryption)
- Ativar [a auditoria da Base de Dados SQL.](../../azure-sql/database/auditing-overview.md)