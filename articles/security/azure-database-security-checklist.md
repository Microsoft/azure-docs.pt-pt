---
title: Lista de verificação de segurança de base de dados do Azure | Documentos da Microsoft
description: Este artigo fornece um conjunto de lista de verificação de segurança da base de dados do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 424453e70e5b62e408f408cd5ae8169cddb14dd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121696"
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança de base de dados do Azure

Para ajudar a melhorar a segurança, a base de dados do Azure inclui uma série de controles de segurança incorporadas que pode utilizar para o limite e controlar o acesso.

Estas incluem:

-   Uma firewall que permite que crie [regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) limitam a conectividade por endereço IP,
-   Firewall ao nível do servidor acessível a partir do portal do Azure
-   Regras de firewall ao nível da base de dados acessíveis a partir do SSMS
-   Conectividade segura à base de dados usando cadeias de ligação segura
-   Utilize a gestão de acesso
-   Encriptação de dados
-   Auditoria da Base de Dados SQL
-   Deteção de ameaças da Base de Dados SQL

## <a name="introduction"></a>Introdução
A computação em nuvem requer novos paradigmas de segurança que não estão familiarizadas para muitos utilizadores da aplicação, os administradores de banco de dados e programadores. Como resultado, algumas organizações são reticentes quanto ao implementar uma infraestrutura de nuvem para gestão de dados devido a riscos de segurança percetível. No entanto, grande parte esta preocupação pode ser minimizada por meio de uma melhor compreensão dos recursos de segurança incorporada no Microsoft Azure e base de dados do Microsoft Azure SQL.

## <a name="checklist"></a>Lista de verificação
Recomendamos que leia os [práticas recomendadas de segurança de base de dados do Azure](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) artigo antes de rever esta lista de verificação. Será capaz de obter o máximo proveito desta lista de verificação depois de compreender as melhores práticas. Em seguida, pode utilizar esta lista de verificação para certificar-se de que já tenha resolvido os problemas importantes em segurança de base de dados do Azure.


|Categoria da lista de verificação| Descrição|
| ------------ | -------- |
|**Proteger os dados**||
| <br> Encriptação em trânsito/Motion| <ul><li>[Segurança de camada de transporte](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), para a encriptação de dados quando estiver se movendo dados para as redes.</li><li>Base de dados requer uma comunicação segura de clientes com base na [TDS (Tabular Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) protocolo por TLS (Transport Layer Security).</li></ul> |
|<br>Encriptação inativa| <ul><li>[Encriptação de dados transparente](https://go.microsoft.com/fwlink/?LinkId=526242), quando os dados Inativos são armazenados fisicamente em qualquer formato digital.</li></ul>|
|**Controlar o acesso**||  
|<br> Acesso à Base de Dados | <ul><li>[Autenticação](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) autenticação (Azure Active Directory Authentication) AD utiliza identidades geridas pelo Azure Active Directory.</li><li>[Autorização](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) conceder aos utilizadores o mínimo de privilégios necessários.</li></ul> |
|<br>Acesso à aplicação| <ul><li>[Segurança ao nível da linha](https://msdn.microsoft.com/library/dn765131) (através de política de segurança, ao mesmo tempo, restringir o acesso ao nível da linha com base no contexto de identidade, a função ou a execução de um utilizador).</li><li>[Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (através de permissão e de política, limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios)</li></ul>|
|**Monitorização proativa**||  
| <br>Detetar e controlo| <ul><li>[Auditoria](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) regista os eventos de base de dados e escreve-as num registo de auditoria / atividade log seu [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Através de estado de funcionamento de base de dados do Azure do Roteiro [registos de atividades do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Deteção de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados. </li></ul> |
|<br>Centro de Segurança do Azure| <ul><li>[Monitorização de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) utilizar o Centro de segurança do Azure como uma solução de monitorização para SQL e outros serviços do Azure de segurança centralizada.</li></ul>|       

## <a name="conclusion"></a>Conclusão
Base de dados do Azure é uma plataforma de base de dados robusta, com uma gama completa de recursos de segurança que cumprem vários requisitos de conformidade organizacionais e normativos. É fácil proteger dados, controlar o acesso físico aos seus dados e, com uma variedade de opções para uma segurança de dados no ficheiro-, coluna- ou ao nível da linha com a encriptação de dados transparente, encriptação ao nível da célula ou segurança ao nível da linha. Sempre encriptado também permite operações de acordo com os dados encriptados, simplificando o processo de atualizações de aplicativos. Por sua vez, acesso a auditoria de registos de atividade da base de dados SQL fornece as informações que necessárias, permitindo que sabe como e quando os dados são acedidos.

## <a name="next-steps"></a>Passos Seguintes
Pode melhorar a proteção da sua base de dados contra utilizadores mal intencionados ou o acesso não autorizado com apenas alguns passos simples. Neste tutorial, ficará a saber como:

- Configurar [regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para o seu servidor e ou base de dados.
- Proteger os seus dados com [encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Ativar [auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

