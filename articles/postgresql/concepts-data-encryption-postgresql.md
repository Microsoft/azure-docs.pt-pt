---
title: Encriptação de dados com chave gerida pelo cliente - Azure Database for PostgreSQL - Servidor Único
description: Azure Database for PostgreSQL Single servidor data encrypted with a customer-managed key permite-lhe trazer a sua própria chave (BYOK) para proteção de dados em repouso. Também permite às organizações implementarem a separação de deveres na gestão das chaves e dos dados.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: d9624fd899649f4e54c5bd509ed5961b862632dd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581573"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database para encriptação de dados de servidor único postgresQL com uma chave gerida pelo cliente

O Azure PostgreSQL aproveita a [encriptação do Azure Storage](../storage/common/storage-service-encryption.md) para encriptar os dados em repouso por padrão utilizando as teclas geridas pela Microsoft. Para os utilizadores do Azure PostgreSQL, é muito semelhante à Encriptação de Dados Transparente (TDE) noutras bases de dados, como o SQL Server. Muitas organizações exigem o controlo total do acesso aos dados utilizando uma chave gerida pelo cliente. A encriptação de dados com as chaves geridas pelo cliente para a Base de Dados Azure para o servidor PostgreSQL Single permite-lhe trazer a sua própria chave (BYOK) para proteção de dados em repouso. Também permite às organizações implementarem a separação de deveres na gestão das chaves e dos dados. Com a encriptação gerida pelo cliente, para além de ser o responsável pelo ciclo de vida de uma chave, pelas permissões de utilização da chave e pela auditoria das operações nas chaves, também possui o controlo total.

A encriptação de dados com as teclas geridas pelo cliente para a Base de Dados Azure para o servidor PostgreSQL Single está definida ao nível do servidor. Para um determinado servidor, uma chave gerida pelo cliente, chamada chave de encriptação (KEK), é usada para encriptar a chave de encriptação de dados (DEK) utilizada pelo serviço. O KEK é uma chave assimétrica armazenada numa instância [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) de propriedade do cliente e gerida pelo cliente. A chave de encriptação chave (KEK) e a chave de encriptação de dados (DEK) é descrita mais detalhadamente mais tarde neste artigo.

Key Vault é um sistema de gestão de chaves externo baseado na nuvem. É altamente disponível e fornece armazenamento escalável e seguro para chaves criptográficas RSA, opcionalmente apoiado por módulos de segurança de hardware validados FIPS 140-2 Nível 2 (HSMs). Não permite o acesso direto a uma chave armazenada, mas fornece serviços de encriptação e desencriptação a entidades autorizadas. O Key Vault pode gerar a chave, importá-la ou [transferi-la de um dispositivo HSM no local.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para servidor Single PostgreSQL suporta os níveis de preços "Final Geral" e "Memory Optimized". Para outras limitações, consulte a secção [de limitação.](concepts-data-encryption-postgresql.md#limitations)

## <a name="benefits"></a>Benefícios

A encriptação de dados com chaves geridas pelo cliente para a Base de Dados Azure para o servidor PostgreSQL Single fornece os seguintes benefícios:

* O acesso a dados é totalmente controlado por si pela capacidade de remover a chave e tornar a base de dados inacessível 
*    Controlo total sobre o ciclo-chave de vida, incluindo a rotação da chave para alinhar com as políticas corporativas
*    Gestão central e organização de chaves no Cofre chave Azure
*    Capacidade de implementar a separação de funções entre os agentes de segurança e a DBA e os administradores de sistema

## <a name="terminology-and-description"></a>Terminologia e descrição

**Chave de encriptação de dados (DEK)**: Uma chave AES256 simétrica usada para encriptar uma divisória ou bloco de dados. Encriptar cada bloco de dados com uma chave diferente torna os ataques de análise cripto mais difícil. O acesso aos DEKs é necessário pelo fornecedor de recursos ou pela instância de aplicação que está a encriptar e desencriptar um bloco específico. Quando substituir um DEK por uma nova chave, apenas os dados do bloco associado devem ser reencri encriptados com a nova chave.

**Chave de encriptação (KEK)**: Uma chave de encriptação usada para encriptar os DEKs. Um KEK que nunca sai do Key Vault permite que os próprios DEKs sejam encriptados e controlados. A entidade que tem acesso à KEK pode ser diferente da entidade que requer o DEK. Uma vez que o KEK é obrigado a desencriptar os DEKs, o KEK é efetivamente um único ponto pelo qual os DEKs podem ser efetivamente eliminados pela supressão do KEK.

Os DEKs, encriptados com os KEKs, são armazenados separadamente. Apenas uma entidade com acesso ao KEK pode desencriptar estes DEKs. Para obter mais informações, consulte [a Segurança em encriptação em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Como encriptação de dados com um trabalho chave gerido pelo cliente

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Diagrama que mostra uma visão geral de Bring Your Own Key":::

Para que um servidor PostgreSQL utilize teclas geridas pelo cliente armazenadas no Cofre de Chaves para encriptação do DEK, um administrador do Key Vault dá os seguintes direitos de acesso ao servidor:

* **obter**: Para recuperar a parte pública e as propriedades da chave no cofre chave.
* **wrapKey**: Para ser capaz de encriptar o DEK. O DEK encriptado está armazenado na Base de Dados Azure para PostgreSQL.
* **desembrulharKey**: Para ser capaz de desencriptar o DEK. A Azure Database for PostgreSQL precisa do DEK desencriptado para encriptar/desencriptar os dados

O administrador-chave do cofre também pode [permitir o registo de eventos de auditoria do Key Vault,](../azure-monitor/insights/key-vault-insights-overview.md)para que possam ser auditados mais tarde.

Quando o servidor está configurado para utilizar a chave gerida pelo cliente armazenada no cofre de chaves, o servidor envia o DEK para o cofre de chaves para encriptação. O Key Vault devolve o DEK encriptado, que está armazenado na base de dados do utilizador. Da mesma forma, quando necessário, o servidor envia o DEK protegido para o cofre da chave para desencriptação. Os auditores podem usar o Azure Monitor para rever os registos de eventos de auditoria do Key Vault, se o registo estiver ativado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisitos para configurar encriptação de dados para Azure Database para servidor único postgreSQL

Seguem-se os requisitos para a configuração do Cofre de Chaves:

* Key Vault e Azure Database for PostgreSQL Single servidor deve pertencer ao mesmo inquilino do Azure Ative Directory (Azure AD). O Key Vault e as interações do servidor não são suportados. A deslocação do recurso Key Vault requer que reconfigure a encriptação de dados.
* O cofre-chave deve ser definido com 90 dias para "Dias para reter cofres apagados". Se o cofre de chaves existente tiver sido configurado com um número mais baixo, terá de criar um novo cofre-chave, uma vez que não pode ser modificado após a criação.
* Ativar a função de eliminação suave no cofre da chave, para proteger da perda de dados se uma chave acidental (ou cofre de chaves) acontecer. Os recursos eliminados por 90 dias são retidos, a menos que o utilizador os recupere ou purgue entretanto. As ações de recuperação e purga têm as suas próprias permissões associadas a uma política de acesso ao Cofre-Chave. A função de eliminação suave está desligada por padrão, mas pode ative-la através do PowerShell ou do Azure CLI (note que não é possível ative-lo através do portal Azure). 
* Permitir a proteção de purga para impor um período de retenção obrigatório para abóbadas e objetos de abóbada apagados
* Conceda à Base de Dados Azure para o acesso do servidor single PostgreSQL ao cofre de chaves com as permissões get, wrapKey e desembrulhar, utilizando a sua identidade gerida única. No portal Azure, a identidade única de 'Serviço' é criada automaticamente quando a encriptação de dados é ativada no servidor PostgreSQL Single. Consulte [a encriptação de dados para a base de dados Azure para o servidor Single PostgreSQL, utilizando o portal Azure](howto-data-encryption-portal.md) para obter instruções detalhadas, passo a passo, quando estiver a utilizar o portal Azure.

Seguem-se os requisitos para a configuração da chave gerida pelo cliente:

* A chave gerida pelo cliente para encriptar o DEK só pode ser assimétrica, RSA 2048.
* A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.
* A chave deve estar no estado *ativado.*
* Se estiver [a importar uma chave existente](/rest/api/keyvault/ImportKey/ImportKey) para o cofre da chave, certifique-se de que a fornece nos formatos de ficheiros suportados `.pfx` (, , `.byok` . `.backup`

## <a name="recommendations"></a>Recomendações

Quando está a utilizar a encriptação de dados utilizando uma chave gerida pelo cliente, aqui estão recomendações para configurar o Key Vault:

* Coloque um bloqueio de recursos no Cofre de Chaves para controlar quem pode eliminar este recurso crítico e evitar a eliminação acidental ou não autorizada.
* Ativar a auditoria e a reportagem de todas as chaves de encriptação. O Key Vault fornece registos fáceis de injetar em outras informações de segurança e ferramentas de gestão de eventos. O Azure Monitor Log Analytics é um exemplo de um serviço que já está integrado.
* Certifique-se de que o Cofre-Chave e a Base de Dados Azure para servidor Single PostgreSQL residem na mesma região, para garantir um acesso mais rápido para o invólucro DEK e desembrulhar operações.
* Bloqueie o Azure KeyVault apenas para **o ponto final privado e redes selecionadas** e permita apenas serviços *confiáveis* da Microsoft para garantir os recursos.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="serviço de confiança com AKV":::

Aqui estão as recomendações para configurar uma chave gerida pelo cliente:

* Guarde uma cópia da chave gerida pelo cliente num local seguro ou guarde-a para o serviço de caução.

* Se o Key Vault gerar a tecla, crie uma cópia de segurança antes de utilizar a chave pela primeira vez. Só pode restaurar a cópia de segurança para o Key Vault. Para obter mais informações sobre o comando de backup, consulte [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condição chave gerida pelo cliente inacessível

Quando configurar a encriptação de dados com uma chave gerida pelo cliente no Key Vault, é necessário um acesso contínuo a esta chave para que o servidor se mantenha on-line. Se o servidor perder o acesso à chave gerida pelo cliente no Key Vault, o servidor começa a negar todas as ligações dentro de 10 minutos. O servidor emite uma mensagem de erro correspondente e altera o estado do servidor para *Inacessível*. Parte da razão pela qual o servidor pode chegar a este estado são:

* Se criarmos um servidor Point In Time Restore para o seu servidor PostgreSQL Single, que tem encriptação de dados ativada, o servidor recém-criado estará em estado *inacessível.* Pode corrigir o estado do servidor através do [portal Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) ou [do CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se criarmos uma réplica de leitura para o seu Azure Database para o servidor PostgreSQL Single, que tem encriptação de dados ativada, o servidor de réplica estará em estado *inacessível.* Pode corrigir o estado do servidor através do [portal Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) ou [do CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se eliminar o KeyVault, a Base de Dados Azure para o servidor PostgreSQL Single não poderá aceder à chave e mudar-se-á para o estado *Inacessível.* Recupere o [Cofre de Chaves](../key-vault/general/key-vault-recovery.md) e revalida a encriptação de dados para disponibilizar o servidor . 
* Se eliminarmos a chave do KeyVault, a Base de Dados Azure para o servidor Single PostgreSQL não poderá aceder à chave e mudar-se-á para o estado *inacessível.* Recupere a [Chave](../key-vault/general/key-vault-recovery.md) e revalida a encriptação de dados para tornar o servidor *disponível*.
* Se a chave armazenada no Azure KeyVault expirar, a chave tornar-se-á inválida e a Base de Dados Azure para o servidor Single PostgreSQL irá transitar para estado *inacessível.* Prolongue a data de validade da chave utilizando [o CLI](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) e, em seguida, revalidar a encriptação de dados para tornar o servidor *disponível*.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revogação acidental do acesso à chave do Cofre de Chaves

Pode acontecer que alguém com direitos de acesso suficientes ao Key Vault desative acidentalmente o acesso do servidor à chave:

* Revogando as permissões do cofre de chaves, embrulhar oKey e desembrulhar as permissões do servidor.
* Apagar a chave.
* Apagar o cofre das chaves.
* Mudar as regras do cofre da chave.

* Eliminação da identidade gerida do servidor em Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorize a chave gerida pelo cliente no Key Vault

Para monitorizar o estado da base de dados e para permitir alertar para a perda de acesso transparente ao protetor de encriptação de dados, configurar as seguintes funcionalidades do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): Uma base de dados inacessível que perdeu o acesso à chave do cliente mostra como "Inacessível" depois de ter sido negada a primeira ligação à base de dados.
* [Registo de atividade](../service-health/alerts-activity-log-service-notifications-portal.md): Quando o acesso à chave do cliente no Cofre chave gerido pelo cliente falha, as entradas são adicionadas ao registo de atividade. Pode restabelecer o acesso o mais rapidamente possível, se criar alertas para estes eventos.

* [Grupos de ação](../azure-monitor/alerts/action-groups.md): Defina estes grupos para enviar notificações e alertas com base nas suas preferências.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Restaurar e replicar com a chave gerida por um cliente no Cofre chave

Depois de a Base de Dados Azure para o servidor PostgreSQL Single ser encriptada com a chave gerida de um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é encriptada. Pode fazer esta nova cópia através de uma operação local ou de geo-restauro, ou através de réplicas de leitura. No entanto, a cópia pode ser alterada para refletir a chave gerida por um novo cliente para encriptação. Quando a tecla gerida pelo cliente é alterada, as cópias de segurança antigas do servidor começam a utilizar a tecla mais recente.

Para evitar problemas durante a configuração da encriptação de dados gerida pelo cliente durante a criação de réplicas restauradas ou lidas, é importante seguir estes passos nos servidores primários e restaurados/replicados:

* Inicie o processo de criação de réplicas ou restauros a partir da base de dados primária do Azure para servidor Single PostgreSQL.
* Mantenha o servidor recém-criado (restaurado/replicado) num estado inacessível, porque a sua identidade única ainda não foi dada permissões ao Key Vault.
* No servidor restaurado/replicado, revalincie a chave gerida pelo cliente nas definições de encriptação de dados. Isto garante que o servidor recém-criado recebe permissões de embrulho e desembrulhar a chave armazenada no Key Vault.

## <a name="limitations"></a>Limitações

Para a Base de Dados Azure para PostgreSQL, o suporte para encriptação de dados em repouso usando a chave gerida pelos clientes (CMK) tem poucas limitações -

* O suporte para esta funcionalidade está limitado aos níveis de preços otimizados para **fins gerais** e **memória.**
* Esta funcionalidade é suportada apenas em regiões e servidores que suportam armazenamento até 16 TB. Para a lista de regiões de Azure que suportam o armazenamento até 16TB, consulte a secção de armazenamento em documentação [aqui](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Todos os novos servidores PostgreSQL criados nas regiões acima listadas, o suporte para encriptação com as chaves do gestor de clientes está **disponível**. O servidor Point In Time Restored (PITR) ou a réplica de leitura não se qualificam embora, em teoria, sejam "novos".
    > - Para validar se o seu servidor a provisionado suportar até 16TB, pode ir à lâmina de nível de preços no portal e ver o tamanho máximo de armazenamento suportado pelo seu servidor provisionado. Se conseguir mover o slider até 4TB, o seu servidor poderá não suportar a encriptação com as teclas geridas pelo cliente. No entanto, os dados são encriptados usando sempre as teclas geridas pelo serviço. Por favor, contacte AskAzureDBforPostgreSQL@service.microsoft.com se tiver alguma pergunta.

* A encriptação só é suportada com a chave criptográfica RSA 2048.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a [encriptação de dados com uma chave gerida pelo cliente para a sua base de dados Azure para o servidor Single PostgreSQL utilizando o portal Azure](howto-data-encryption-portal.md).
