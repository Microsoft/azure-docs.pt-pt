---
title: Versões com suporte-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões principais e secundárias postgres com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792230"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais do PostgreSQL com suporte
A Microsoft pretende oferecer suporte a versões n-2 do mecanismo PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

O banco de dados do Azure para PostgreSQL atualmente dá suporte às seguintes versões principais:

## <a name="postgresql-version-11"></a>PostgreSQL versão 11
A versão secundária atual é 11,5. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-5.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A versão secundária atual é 10,10. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-10.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9,6
A versão secundária atual é 9.6.15. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-95"></a>PostgreSQL versão 9,5
A versão secundária atual é 9.5.19. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="managing-upgrades"></a>Gerenciando atualizações
O projeto PostgreSQL emite regularmente versões secundárias para corrigir bugs relatados. O banco de dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implantações mensais do serviço. 

A atualização automática da versão principal não é suportada. Por exemplo, não há nenhuma atualização automática do PostgreSQL 9.5 para o PostgreSQL 9.6. Para atualizar para a próxima versão principal, crie [uma captura e um restauro da base de dados](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a versão do novo motor.

### <a name="version-syntax"></a>Sintaxe da versão
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).
