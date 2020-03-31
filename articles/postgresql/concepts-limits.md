---
title: Limites - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve limites na Base de Dados Azure para PostgreSQL - Servidor Único, tais como o número de opções de motor de ligação e armazenamento.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836461"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites na Base de Dados Azure para PostgreSQL - Servidor Único
As seguintes secções descrevem a capacidade e os limites funcionais no serviço de base de dados. Se quiser aprender sobre os níveis de recursos (computação, memória, armazenamento), consulte o artigo dos níveis de [preços.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Número máximo de ligações
O número máximo de ligações por nível de preços e vCores são mostrados abaixo. O sistema Azure requer cinco ligações para monitorizar a Base de Dados Azure para o servidor PostgreSQL. 

|**Nível de Preços**| **vCore(s)**| **Máx. Ligações** | **Ligações ao utilizador Max** |
|---|---|---|---|
|Básico| 1| 55 | 50|
|Básico| 2| 105 | 100|
|Fins Gerais| 2| 150| 145|
|Fins Gerais| 4| 250| 245|
|Fins Gerais| 8| 480| 475|
|Fins Gerais| 16| 950| 945|
|Fins Gerais| 32| 1500| 1495|
|Fins Gerais| 64| 1900| 1895|
|Otimizada para Memória| 2| 300| 295|
|Otimizada para Memória| 4| 500| 495|
|Otimizada para Memória| 8| 960| 955|
|Otimizada para Memória| 16| 1900| 1895|
|Otimizada para Memória| 32| 1987| 1982|

Quando as ligações excederem o limite, poderá receber o seguinte erro:
> FATAL: desculpe, muitos clientes já

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de ligação como o pgBouncer para gerir eficientemente as ligações.

Uma ligação PostgreSQL, mesmo inativa, pode ocupar cerca de 10MB de memória. Além disso, criar novas ligações leva tempo. A maioria das aplicações solicita muitas ligações de curta duração, o que compõe esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de ligação que diminui as ligações inativas e reutiliza as ligações existentes ajudará a evitar isso. Para saber mais, visite o nosso [post de blog.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações à escala
- Atualmente, a escala dinâmica de e para os níveis de preços básicos não é suportada.
- O tamanho de armazenamento do servidor diminuindo não é suportado atualmente.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor de base de dados não é suportada. Se quiser fazer upgrade para a próxima versão principal, [despeje-a e restaure-a](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do motor.

> Note que antes da versão 10 do PostgreSQL, a política de [versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou que uma _grande atualização_ da versão era um aumento no primeiro _ou_ segundo número (por exemplo, 9.5 a 9.6 foi considerada uma _grande_ atualização da versão).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização da versão (por exemplo, 10.0 a 10.1 é uma atualização de versão _menor,_ e 10 a 11 é uma atualização de versão _importante)._

### <a name="vnet-service-endpoints"></a>VNet service endpoints (Pontos finais de serviço de VNet)
- O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.

### <a name="restoring-a-server"></a>Restaurar um servidor
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações de nível de preços que o servidor em que se baseia.
- O novo servidor criado durante um restauro não tem as regras de firewall que existiam no servidor original. As regras de firewall têm de ser configuradas separadamente para este novo servidor.
- Restaurar um servidor eliminado não é suportado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, os caracteres UTF-8 não são suportados totalmente em código aberto PostgreSQL no Windows, que afeta a Base de Dados Azure para PostgreSQL. Consulte a linha do [Bug #15476 no arquivo postgresql](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
- Entenda [o que está disponível em cada nível de preços](concepts-pricing-tiers.md)
- Saiba mais sobre [versões de base de dados PostgreSQL suportadas](concepts-supported-versions.md)
- Reveja como fazer o back up e restaurar um servidor na Base de [Dados Azure para PostgreSQL usando o portal Azure](howto-restore-server-portal.md)
