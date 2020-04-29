---
title: Encriptação de dados com chave gerida pelo cliente - Base de Dados Azure para PostgreSQL - Servidor único
description: A Base de Dados Azure para encriptação de dados de servidor único PostgreSQL com uma chave gerida pelo cliente permite-lhe trazer a sua própria chave (BYOK) para a proteção de dados em repouso. Permite ainda que as organizações implementem a separação de deveres na gestão de chaves e dados.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297027"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Base de Dados Azure para encriptação de dados de servidor único PostgreSQL com uma chave gerida pelo cliente

> [!NOTE]
> Neste momento, deve solicitar o acesso à utilização desta capacidade. Para isso, contacte. AskAzureDBforPostgreSQL@service.microsoft.com

A encriptação de dados com chaves geridas pelo cliente para a Base de Dados Azure para o servidor Single PostgreSQL permite-lhe trazer a sua própria chave (BYOK) para a proteção de dados em repouso. Permite ainda que as organizações implementem a separação de deveres na gestão de chaves e dados. Com a encriptação gerida pelo cliente, é responsável e, em pleno controlo, pelo ciclo de vida de uma chave, permissões de utilização chave e auditoria de operações em chaves.

A encriptação de dados com chaves geridas pelo cliente para a Base de Dados Azure para o servidor Single PostgreSQL, é definida ao nível do servidor. Para um determinado servidor, uma chave gerida pelo cliente, chamada chave de encriptação (KEK), é usada para encriptar a chave de encriptação de dados (DEK) utilizada pelo serviço. O KEK é uma chave assimétrica armazenada numa instância [azure key vault](../key-vault/key-Vault-secure-your-key-Vault.md) gerida pelo cliente e gerida pelo cliente. A chave de encriptação (KEK) e a chave de encriptação de dados (DEK) são descritas mais detalhadamente mais tarde neste artigo.

Key Vault é um sistema de gestão de chaves externas baseado em nuvem. É altamente disponível e fornece armazenamento escalável e seguro para chaves criptográficas RSA, opcionalmente apoiadopor módulos de segurança de hardware validados de nível 2 (HSMs). Não permite o acesso direto a uma chave armazenada, mas fornece serviços de encriptação e desencriptação a entidades autorizadas. O Cofre chave pode gerar a chave, importá-la ou [transferi-la de um dispositivo HSM no local](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde o Azure Database for PostgreSQL Single server suporta níveis de preços "Propósito Geral" e "Memory Optimized".

## <a name="benefits"></a>Benefícios

A encriptação de dados para a Base de Dados Azure para o servidor Single PostgreSQL fornece os seguintes benefícios:

* O acesso a dados é totalmente controlado por si pela capacidade de remover a chave e tornar a base de dados inacessível 
*    Controlo total sobre o ciclo de vida-chave, incluindo a rotação da chave para alinhar com as políticas corporativas
*    Gestão central e organização de chaves no Cofre chave Azure
*    Capacidade de implementação da separação de funções entre oficiais de segurança, e DBA e administradores de sistemas

## <a name="terminology-and-description"></a>Terminologia e descrição

Chave de encriptação de **dados (DEK)**: Uma chave Simétrica AES256 utilizada para encriptar uma partilha ou bloco de dados. Encriptar cada bloco de dados com uma tecla diferente torna os ataques de análise de cripto mais difíceis. O acesso aos DEKs é necessário pelo fornecedor de recursos ou pela instância de aplicação que está a encriptar e a desencriptar um bloco específico. Quando substituir um DEK por uma nova tecla, apenas os dados do seu bloco associado devem ser novamente encriptados com a nova tecla.

**Chave de encriptação (KEK)**: Uma chave de encriptação usada para encriptar os DEKs. Um KEK que nunca sai do Key Vault permite que os próprios DEKs sejam encriptados e controlados. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma vez que o KEK é obrigado a desencriptar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente eliminados por eliminação do KEK.

Os DEKs, encriptados com os KEKs, são armazenados separadamente. Só uma entidade com acesso ao KEK pode desencriptar estes DEKs. Para mais informações, consulte [a Segurança na encriptação em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Como funciona a encriptação de dados com uma chave gerida pelo cliente

![Diagrama que mostra uma visão geral de Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Para que um servidor PostgreSQL utilize chaves geridas pelo cliente armazenadas no Key Vault para encriptação do DEK, um administrador key vault dá os seguintes direitos de acesso ao servidor:

* **obter**: Para recuperar a parte pública e as propriedades da chave no cofre chave.
* **wrapKey**: Para ser capaz de encriptar o DEK.
* **desembrulharChave**: Para ser capaz de desencriptar o DEK.

O administrador do cofre chave também pode [permitir o registo de eventos de auditoria key vault,](../azure-monitor/insights/azure-key-vault.md)para que possam ser auditados mais tarde.

Quando o servidor está configurado para utilizar a chave gerida pelo cliente armazenada no cofre da chave, o servidor envia o DEK para o cofre de chaves para encriptação. O Key Vault devolve o DEK encriptado, que está armazenado na base de dados do utilizador. Da mesma forma, quando necessário, o servidor envia o DEK protegido para o cofre chave para desencriptação. Os auditores podem usar o Monitor Azure para rever os registos de eventos de auditoria do Key Vault, se o registo está ativado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisitos para configurar encriptação de dados para base de dados Azure para servidor single PostgreSQL

Seguem-se os requisitos para configurar o Cofre chave:

* Key Vault e Azure Database para o servidor Single PostgreSQL devem pertencer ao mesmo inquilino azure Ative Directory (Azure AD). O Cofre chave do inquilino cruzado e as interações do servidor não são suportadas. Mover recursos depois requer que reconfigure a encriptação de dados.
* Deve ativar a função de eliminação suave no cofre da chave, para proteger contra a perda de dados se ocorrer uma eliminação acidental da chave (ou cofre de chaves). Os recursos eliminados suavemente são retidos durante 90 dias, a menos que o utilizador os recupere ou purgue entretanto. As ações de recuperação e purga têm as suas próprias permissões associadas a uma política de acesso ao Cofre Chave. A função soft-delete está desligada por defeito, mas pode atilá-la através do PowerShell ou do Azure CLI (note que não pode permitir através do portal Azure).
* Conceda a Base de Dados Azure para o acesso do servidor Single PostgreSQL ao cofre chave com as permissões get, wrapKey e desembrulharKey utilizando a sua identidade gerida única. No portal Azure, a identidade única é criada automaticamente quando a encriptação de dados é ativada no servidor PostgreSQL Single. Consulte a [encriptação de dados para o servidor Single Azure para PostgreSQL, utilizando o portal Azure](howto-data-encryption-portal.md) para instruções detalhadas e passo a passo quando estiver a utilizar o portal Azure.

* Quando estiver a utilizar uma firewall com o Key Vault, tem de ativar a opção **Permitir que os serviços fidedignos da Microsoft contornem a firewall**.

Seguem-se os requisitos para configurar a chave gerida pelo cliente:

* A chave gerida pelo cliente para encriptar o DEK pode ser apenas assimétrica, RSA 2028.
* A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.
* A chave deve estar no estado *ativado.*
* Se estiver a importar uma chave existente para o cofre da chave, certifique-se`.pfx` `.byok`de `.backup`a fornecer nos formatos de ficheiros suportados ( . . ).

## <a name="recommendations"></a>Recomendações

Quando está a usar encriptação de dados utilizando uma chave gerida pelo cliente, aqui estão as recomendações para configurar o Key Vault:

* Detete um bloqueio de recursos no Cofre chave para controlar quem pode eliminar este recurso crítico e evitar a eliminação acidental ou não autorizada.
* Ativar auditoria e reportagem em todas as chaves de encriptação. O Key Vault fornece registos que são fáceis de injetar em outras ferramentas de informação de segurança e gestão de eventos. O Azure Monitor Log Analytics é um exemplo de um serviço que já está integrado.

* Certifique-se de que o Key Vault e o Azure Database para o servidor PostgreSQL Single residem na mesma região, para garantir um acesso mais rápido para operações de embrulho e desembrulhar desembrulhadas de DEK.

Aqui estão as recomendações para configurar uma chave gerida pelo cliente:

* Guarde uma cópia da chave gerida pelo cliente num local seguro ou deposite-a no serviço de caução.

* Se o Key Vault gerar a chave, crie uma chave de reserva antes de utilizar a tecla pela primeira vez. Só pode supor a reserva do Cofre chave. Para mais informações sobre o comando de backup, consulte [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condição-chave gerida pelo cliente inacessível

Quando configura a encriptação de dados com uma chave gerida pelo cliente no Key Vault, é necessário um acesso contínuo a esta chave para que o servidor permaneça on-line. Se o servidor perder o acesso à chave gerida pelo cliente no Key Vault, o servidor começa a negar todas as ligações dentro de 10 minutos. O servidor emite uma mensagem de erro correspondente e altera o estado do servidor para *Inacessível*. A única ação permitida numa base de dados neste estado é asuaditá-la.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revogação acidental do acesso à chave do Cofre chave

Pode acontecer que alguém com direitos de acesso suficientes para key vault acidentalmente desativa o acesso do servidor à chave por:

* Revogando as permissões do cofre de chaves do servidor.
* Apagar a chave.
* Apagar o cofre da chave.
* Mudar as regras da firewall do cofre.

* Apagar a identidade gerida do servidor em Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorize a chave gerida pelo cliente no Cofre chave

Para monitorizar o estado da base de dados e para permitir alertar para a perda de acesso transparente ao protetor de encriptação de dados, configure as seguintes funcionalidades do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): Uma base de dados inacessível que perdeu o acesso à chave do cliente mostra-se "Inacessível" depois de ter sido negada a primeira ligação à base de dados.
* [Registo de atividades](../service-health/alerts-activity-log-service-notifications.md): Quando o acesso à chave do cliente no Cofre chave gerido pelo cliente falha, as entradas são adicionadas ao registo de atividade. Pode restabelecer o acesso o mais rapidamente possível, se criar alertas para estes eventos.

* [Grupos de ação](../azure-monitor/platform/action-groups.md): Defina-os para lhe enviar notificações e alertas com base nas suas preferências.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Restaurar e replicar com a chave gerida do cliente no Key Vault

Depois de a Base de Dados Azure para o servidor Single PostgreSQL ser encriptada com a chave gerida por um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é encriptada. Pode fazer esta nova cópia através de uma operação local ou geo-restauração, ou através de réplicas de leitura. No entanto, a cópia pode ser alterada para refletir a chave gerida por um novo cliente para encriptação. Quando a chave gerida pelo cliente é alterada, as cópias de segurança antigas do servidor começam a utilizar a tecla mais recente.

Para evitar problemas ao configurar encriptação de dados gerida pelo cliente durante a restauração ou leitura da criação de réplicas, é importante seguir estes passos nos servidores mestre e restaurado/réplica:

* Inicie o processo de restauro ou leitura de criação de réplicas a partir da base de dados master Azure para o servidor Single PostgreSQL.
* Mantenha o servidor recém-criado (restaurado/réplica) num estado inacessível, porque a sua identidade única ainda não foi dada permissões ao Key Vault.
* No servidor restaurado/réplica, revvalida a chave gerida pelo cliente nas definições de encriptação de dados. Isto garante que o servidor recém-criado é dado embrulhámento e desembrulhar permissões para a chave armazenada no Cofre chave.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a encriptação de [dados com uma chave gerida pelo cliente para a sua base de dados Azure para o servidor Single PostgreSQL utilizando o portal Azure](howto-data-encryption-portal.md).

