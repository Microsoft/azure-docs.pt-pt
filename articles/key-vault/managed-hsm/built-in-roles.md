---
title: Funções geridas por RBAC local - Azure Key Vault | Microsoft Docs
description: Uma visão geral das funções integradas do HSM geridas que podem ser atribuídas aos utilizadores, diretores de serviços, grupos e identidades geridas
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 01e96922d9c0c47eaf4d430e92eafcd9d0964e13
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557229"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Funções incorporadas do RBAC locais do HSM Gerido

O RBAC local gerido do HSM tem vários papéis incorporados. Pode atribuir estas funções a utilizadores, diretores de serviço, grupos e identidades geridas. Para permitir que um diretor execute uma operação, deve atribuir-lhes um papel que lhes conceda permissão para realizar essas operações. Todas estas funções e operações apenas permitem gerir a permissão para operações de data plane. Para gerir permissões de plano de controlo para o recurso Gerido HSM, tem de utilizar o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md). Alguns exemplos de operações de avião de controlo são criar um novo HSM gerido ou atualizar, mover, eliminá-lo.

## <a name="built-in-roles"></a>Funções incorporadas

|Nome da Função|Description|ID|
|---|---|---|
|Administrador do HSM Gerido| Concede permissões para realizar todas as operações relacionadas com o Domínio de Segurança, cópia de segurança/restauro completo e gestão de funções. Não é permitido realizar nenhuma operação de gestão chave.|a290e904-7015-4bba-90c8-60543313cdb4|
|Oficial de Cripto gerido do HSM|Concede permissões para executar todas as chaves de gestão de funções, purgar ou recuperar chaves eliminadas e chaves de exportação. Não é permitido realizar quaisquer outras operações de gestão chave.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Utilizador crypto gerido do HSM|Concede permissões para a realização de todas as operações de gestão chave, exceto purgar ou recuperar chaves eliminadas e chaves de exportação.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Administrador de Política gerido do HSM| Concede permissão para criar e eliminar atribuições de funções|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Auditor cripto gerido do HSM|Os subsídios lêem permissão para ler (mas não usar) atributos-chave.|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Encriptação gerida do serviço crypto HSM| Concede permissão para usar uma chave para encriptação de serviço. |33413926-3206-4cdd-b39a-83574fe37a17|
|Cópia de Segurança do HSM Gerido| Concede permissão para realizar uma única chave ou cópia de segurança HSM inteira.|7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Operações permitidas
> [!NOTE]  
> - Um 'X' indica que é permitido um papel para executar a ação de dados. A célula vazia indica que a função não tem emissão para executar essa ação de dados.
> - Todos os nomes de ação de dados têm um prefixo 'Microsoft.KeyVault/managedHsm', que é omitido nas tabelas abaixo para brevidade.
> - Todos os nomes de papéis têm um prefixo "Managed HSM" que é omitido na tabela abaixo para a brevidade.

|Ação de Dados | Administrador | Crypto Officer | Utilizador Crypto | Administrador de Política | Encriptação do Serviço Crypto | Backup | Crypto Auditor|
|---|---|---|---|---|---|---|---|
|**Gestão do Domínio de Segurança**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Gestão de chaves**|
|/chaves/leitura/ação|||<center>X</center>||<center>X</center>||<center>X</center>|
|/chaves/escrita/ação|||<center>X</center>||||
|/chaves/criar|||<center>X</center>||||
|/chaves/apagar|||<center>X</center>||||
|/chaves/deletedKeys/read/action||<center>X</center>|||||
|/chaves/deletedKeys/recuperar/ação||<center>X</center>|||||
|/chaves/eliminadoKeys/delete||<center>X</center>|||||<center>X</center>|
|/chaves/backup/ação|||<center>X</center>|||<center>X</center>|
|/chaves/restauro/ação|||<center>X</center>||||
|/chaves/exportação/ação||<center>X</center>|||||
|/chaves/libertação/ação|||<center>X</center>||||
|/chaves/importação/ação|||<center>X</center>||||
|**Principais operações criptográficas**|
|/chaves/encriptação/ação|||<center>X</center>||||
|/chaves/desencriptação/ação|||<center>X</center>||||
|/chaves/embrulho/ação|||<center>X</center>||<center>X</center>||
|/chaves/desembrulhar/ação|||<center>X</center>||<center>X</center>||
|/chaves/sinal/ação|||<center>X</center>||||
|/chaves/verificar/ação|||<center>X</center>||||
|**Gestão de funções**|
|/funçãoAssagens/leitura/ação|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/funçãoAssinsignments/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/funçãoAssagens/eliminação/ação|<center>X</center>|<center>X</center>||<center>X</center>|||
|/funDefinitions/read/action|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/funDefinitions/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/funDefinitions/delete/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|**Gestão de backup/restauro**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/ação|<center>X</center>|||||<center>X</center>|
|/restaurar/iniciar/ação|<center>X</center>||||||
|/restaurar/estado/ação|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Passos seguintes

- Consulte uma visão geral do [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Veja um tutorial sobre [gestão de funções gerida do HSM](role-management.md)
