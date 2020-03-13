---
title: Lista de verificação de segurança da base de dados Azure Microsoft Docs
description: Este artigo fornece um conjunto de lista de verificação para a segurança da base de dados Azure.
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
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79201030"
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança da base de dados Azure

Para ajudar a melhorar a segurança, a Base de Dados Azure inclui uma série de controlos de segurança incorporados que pode usar para limitar e controlar o acesso.

Estas incluem:

-    Uma firewall que lhe permite criar regras de [firewall](../../sql-database/sql-database-firewall-configure.md) limitando a conectividade por endereço IP,
-    Firewall ao nível do servidor acessível a partir do portal Azure
-    Regras de firewall ao nível da base de dados acessíveis a partir de SSMS
-    Segurança na conectividade com a sua base de dados utilizando cordas de ligação seguras
-    Utilizar a gestão de acesso
-    Encriptação de dados
-    Auditoria da Base de Dados SQL
-    Deteção de ameaças de base de dados SQL

## <a name="introduction"></a>Introdução
A computação em nuvem requer novos paradigmas de segurança que não são familiares para muitos utilizadores de aplicações, administradores de bases de dados e programadores. Como resultado, algumas organizações hesitam em implementar uma infraestrutura em nuvem para a gestão de dados devido a riscos de segurança percebidos. No entanto, grande parte desta preocupação pode ser aliviada através de uma melhor compreensão das funcionalidades de segurança incorporadas na Microsoft Azure e Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista de Verificação
Recomendamos que leia o artigo de Boas Práticas de Segurança da Base de [Dados Azure](database-best-practices.md) antes de rever esta lista de verificação. Poderá tirar o máximo partido desta lista de verificação depois de entender as melhores práticas. Em seguida, pode utilizar esta lista de verificação para se certificar de que abordou os problemas importantes na segurança da base de dados Azure.


|Categoria lista de verificação| Descrição|
| ------------ | -------- |
|**Proteger dados**||
| <br> Encriptação em Movimento/Trânsito| <ul><li>[Transporte Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), para encriptação de dados quando os dados estão a mover-se para as redes.</li><li>A base de dados requer uma comunicação segura dos clientes com base no protocolo [TDS (Fluxo de Dados Tabular)](https://msdn.microsoft.com/library/dd357628.aspx) sobre tLS (Segurança da Camada de Transporte).</li></ul> |
|<br>Encriptação inativa| <ul><li>[Encriptação transparente de dados,](https://go.microsoft.com/fwlink/?LinkId=526242)quando os dados inativos são armazenados fisicamente em qualquer forma torto digital.</li></ul>|
|**Acesso ao Controlo**||  
|<br> Acesso à Base de Dados | <ul><li>[Autenticação](../../sql-database/sql-database-manage-logins.md) (Autenticação ativa do Diretório Azure) Autenticação adusa utiliza identidades geridas pelo Diretório Ativo Azure.</li><li>[A autorização](../../sql-database/sql-database-manage-logins.md) concede aos utilizadores os menores privilégios necessários.</li></ul> |
|<br>Acesso à aplicação| <ul><li>[Segurança de nível](https://msdn.microsoft.com/library/dn765131) de linha (Utilizando a Política de Segurança, ao mesmo tempo que restringe o acesso ao nível da linha com base na identidade, função ou contexto de execução de um utilizador).</li><li>[Mascaramento de dados dinâmicos](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (Utilizando permissão e política, limita a exposição sensível de dados, mascarando-os a utilizadores não privilegiados)</li></ul>|
|**Monitorização Proativa**||  
| <br>Rastreio e Deteção| <ul><li>[A auditoria](../../sql-database/sql-database-auditing.md) rastreia os eventos da base de dados e escreve-os num registo de auditoria/Registo de Atividade na sua [conta de Armazenamento Azure](../../storage/common/storage-create-storage-account.md).</li><li>Rastrear a saúde da base de dados Azure utilizando registos de atividade do [Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[A Deteção de Ameaças](../../sql-database/sql-database-threat-detection.md) deteta atividades anómalas na base de dados que indicam potenciais ameaças à segurança na base de dados. </li></ul> |
|<br>Centro de Segurança do Azure| <ul><li>[Monitorização de Dados](../../security-center/security-center-enable-auditing-on-sql-databases.md) Utilize o Azure Security Center como uma solução centralizada de monitorização de segurança para os serviços SQL e outros serviços Azure.</li></ul>|        

## <a name="conclusion"></a>Conclusão
A Azure Database é uma plataforma de base de dados robusta, com uma gama completa de funcionalidades de segurança que cumprem muitos requisitos de conformidade organizacional e regulamentar. Pode proteger facilmente os dados controlando o acesso físico aos seus dados e utilizando uma variedade de opções para a segurança de dados no nível de ficheiros, colunas ou linhas com encriptação transparente de dados, encriptação de nível celular ou Segurança de Nível de Linha. Sempre Encriptado também permite operações contra dados encriptados, simplificando o processo de atualizações da aplicação. Por sua vez, o acesso a registos de auditoria da atividade da Base de Dados SQL fornece-lhe a informação de que necessita, permitindo-lhe saber como e quando os dados são acedidos.

## <a name="next-steps"></a>Passos seguintes
Pode melhorar a proteção da sua base de dados contra utilizadores mal intencionados ou o acesso não autorizado com apenas alguns passos simples. Neste tutorial, ficará a saber como:

- Configurar regras de [firewall](../../sql-database/sql-database-firewall-configure.md) para o seu servidor e ou base de dados.
- Proteja os seus dados com [encriptação](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Ativar a auditoria da Base de [Dados SQL](../../sql-database/sql-database-auditing.md).

