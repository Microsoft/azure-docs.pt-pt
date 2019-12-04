---
title: Limites-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve os limites no banco de dados do Azure para PostgreSQL-servidor único, como o número de opções de mecanismo de armazenamento e conexão.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: d74206ebdf35a8f5b353553cb89e954cb2313611
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768542"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites no banco de dados do Azure para PostgreSQL-servidor único
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Se você quiser saber mais sobre as camadas de recurso (computação, memória, armazenamento), consulte o artigo [tipos de preço](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Máximo de conexões
O número máximo de conexões por tipo de preço e vCores são os seguintes: 

|**Escalão de Preço**| **vCore (s)**| **Máximo de conexões** | **Máximo de conexões de usuário** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Fins Gerais| 2| 150| 145|
|Fins Gerais| 4| 250| 245|
|Fins Gerais| 8| 480| 475|
|Fins Gerais| 16| 950| 945|
|Fins Gerais| 32| 1500| 1495|
|Fins Gerais| 64| 1900| 1895|
|Memória Otimizada| 2| 300| 295|
|Memória Otimizada| 4| 500| 495|
|Memória Otimizada| 8| 960| 955|
|Memória Otimizada| 16| 1900| 1895|
|Memória Otimizada| 32| 1987| 1982|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: Desculpe, já há muitos clientes

O sistema do Azure requer cinco conexões para monitorar o servidor do banco de dados do Azure para PostgreSQL. 

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de escala
- No momento, não há suporte para o dimensionamento dinâmico de e para os tipos de preço básicos.
- Não há suporte para a redução do tamanho de armazenamento do servidor no momento.

### <a name="server-version-upgrades"></a>Atualizações de versão do servidor
- Atualmente, não há suporte para migração automatizada entre as versões do mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) -o para um servidor criado com a nova versão do mecanismo.

> Observe que, antes do PostgreSQL versão 10, [a política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número (por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ ).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ e 10 a 11 é uma atualização de versão _principal_ ).

### <a name="vnet-service-endpoints"></a>Pontos de extremidade de serviço de VNet
- O suporte para pontos de extremidade de serviço de VNet é apenas para servidores Uso Geral e com otimização de memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camada de preços que o servidor no qual ele se baseia.
- O novo servidor criado durante uma restauração não tem as regras de firewall que existiam no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.
- Não há suporte para a restauração de um servidor excluído.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, os caracteres UTF-8 não têm suporte completo no PostgreSQL de código-fonte aberto no Windows, o que afeta o banco de dados do Azure para PostgreSQL. Consulte o thread sobre o [Bug #15476 no arquivo PostgreSQL-Archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [as versões de banco de dados PostgreSQL com suporte](concepts-supported-versions.md)
- Examine [como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL usando o portal do Azure](howto-restore-server-portal.md)
