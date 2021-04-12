---
title: Limitações - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve limitações na Base de Dados Azure para o MySQL - Servidor Flexível, como o número de opções de motor de ligação e armazenamento.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034525"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Limitações na Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo descreve limitações na Base de Dados Azure para o serviço MySQL Flexible Server. São também aplicáveis [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) no motor da base de dados MySQL. Se quiser aprender sobre os níveis de recursos (computação, memória, armazenamento), consulte o artigo [de computação e armazenamento.](concepts-compute-storage.md)

## <a name="server-parameters"></a>Parâmetros do servidor

> [!NOTE]
> Se estiver à procura de valores min/max para parâmetros de servidor como `max_connections` `innodb_buffer_pool_size` e, esta informação passou para o artigo [de parâmetros](./concepts-server-parameters.md) de design de design de servidores do servidor.

A Azure Database for MySQL suporta afinar os valores dos parâmetros do servidor. O valor min e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` `query_cache_size` , é determinado pelo nível de cálculo e tamanho do cálculo do servidor. Consulte os [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre estes limites.

Os plugins de palavras-passe como "validate_password" e "caching_sha2_password" não são suportados pelo serviço.

## <a name="storage-engines"></a>Motores de armazenamento

O MySQL suporta muitos motores de armazenamento. Na Base de Dados Azure para o MySQL Flexible Server, os seguintes motores de armazenamento são suportados e não suportados:

### <a name="supported"></a>Suportado
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BURACO NEGRO](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARQUIVO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERADO](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Privilégios & suporte à manipulação de dados

Muitos parâmetros e configurações do servidor podem inadvertidamente degradar o desempenho do servidor ou anular as propriedades acid do servidor MySQL. Para manter a integridade do serviço e sLA a nível de produto, este serviço não expõe múltiplas funções. 

O serviço MySQL não permite o acesso direto ao sistema de ficheiros subjacente. Alguns comandos de manipulação de dados não são suportados. 

### <a name="unsupported"></a>Não suportado

Os seguintes não são suportados:
- Papel DBA: Restrito. Em alternativa, pode utilizar o utilizador do administrador (criado durante a criação de novos servidores), permite-lhe executar a maioria das declarações de DDL e DML. 
- SUPER privilégio: Da mesma forma, [o privilégio SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova os `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` comando quando efetuar uma mesqldump.
- Bases de dados do sistema: A base de [dados do sistema Mysql](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) é apenas de leitura e utilizada para suportar várias funcionalidades do PaaS. Não é possível esquirá alterações na base de dados do `mysql` sistema.
- `SELECT ... INTO OUTFILE`: Não suportado no serviço.

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE` é suportado, mas o `[LOCAL]` parâmetro deve ser especificado e direcionado para um caminho UNC (armazenamento azul montado através de SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="zone-redundant-ha"></a>Zona redundante HA
- Esta configuração só pode ser definida durante a criação do servidor.
- Não suportado no nível de cálculo burstable.

### <a name="networking"></a>Rede
- O método de conectividade não pode ser alterado após a criação do servidor. Se o servidor for criado com *acesso privado (VNet Integration)*, não pode ser alterado para *acesso público (endereços IP permitidos)* após a criação, e vice-versa
- O TLS/SSL é ativado por defeito e não pode ser desativado.
- A versão mínima TLS suportada no servidor é TLS1.2. Consulte para [ligar utilizando TLS/SSL](./how-to-connect-tls-ssl.md) para saber mais.

### <a name="stopstart-operation"></a>Operação stop/start
- Não suportado com configurações ha redundantes de zona (tanto primária como de prontidão).
- Não suportado com configurações de réplicas de leitura (fonte e réplicas).

### <a name="scale-operations"></a>Operações de escala
- A diminuição do armazenamento do servidor não é suportada.

### <a name="read-replicas"></a>Réplicas de leitura
- Não suportado com configurações ha redundantes de zona (tanto primária como de prontidão).

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- A migração automatizada entre as principais versões do motor da base de dados não é suportada. Se quiser atualizar a versão principal, faça uma [lixeira e restaure-a](../concepts-migrate-dump-restore.md) num servidor que foi criado com a nova versão do motor.

### <a name="restoring-a-server"></a>Restaurar um servidor
- Com a restauração pontual, novos servidores são criados com as mesmas configurações de computação e armazenamento que o servidor de origem em que se baseia. A computação do servidor recentemente restaurada pode ser reduzida após a criação do servidor.
- Restaurar um servidor apagado não é suportado.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Funcionalidades disponíveis no Servidor Único mas ainda não suportadas no Servidor Flexível 
Nem todas as funcionalidades disponíveis na Base de Dados Azure para o MySQL - Servidor Único ainda estão disponíveis no Servidor Flexível. Para obter uma lista completa de comparação de funcionalidades entre servidor único e servidor flexível, consulte [a escolha da opção correta do Servidor MySQL na documentação Azure.](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>Passos seguintes

- Aprenda [a escolher a opção certa do Servidor MySQL na documentação do Azure](../select-right-deployment-type.md)
- Compreenda [o que está disponível para opções de computação e armazenamento em servidor flexível](concepts-compute-storage.md)
- Saiba mais sobre [versões MySQL suportadas](concepts-supported-versions.md)
- Quickstart: [Use o portal Azure para criar uma Base de Dados Azure para servidor flexível MySQL](quickstart-create-server-portal.md)
