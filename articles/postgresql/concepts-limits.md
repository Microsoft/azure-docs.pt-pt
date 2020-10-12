---
title: Limites - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve limites na Base de Dados Azure para PostgreSQL - Servidor Único, como o número de opções de motor de ligação e armazenamento.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 6f48245983898c542197deb7e0b3cd53bd39be33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707528"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites na Base de Dados Azure para PostgreSQL - Servidor Único
As seguintes secções descrevem limites de capacidade e funcionais no serviço de base de dados. Se quiser aprender sobre os níveis de recursos (computação, memória, armazenamento), consulte o artigo [de níveis de preços.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Número máximo de ligações
O número máximo de ligações por nível de preços e vCores são mostrados abaixo. O sistema Azure requer cinco ligações para monitorizar a Base de Dados Azure para o servidor PostgreSQL. 

|**Escalão de Preço**| **vCore(s)**| **Máx. Ligações** | **Conexões de utilizador max** |
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
> Para uma melhor experiência, recomendamos que utilize um pooler de conexão como o pgBouncer para gerir eficientemente as ligações.

Uma ligação PostgreSQL, mesmo inativa, pode ocupar cerca de 10MB de memória. Além disso, criar novas ligações leva tempo. A maioria das aplicações solicita muitas ligações de curta duração, o que agrava esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando à diminuição do desempenho. Um pooler de ligação que diminui as ligações ociosas e reutiliza as ligações existentes ajudará a evitar isso. Para saber mais, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de escala
- Atualmente, não é suportado um escalão dinâmico de e para os níveis básicos de preços.
- O tamanho de armazenamento do servidor diminuindo não é suportado atualmente.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor da base de dados não é suportada. Se quiser fazer o upgrade para a próxima versão principal, faça uma [lixeira e restaure-a](./howto-migrate-using-dump-and-restore.md) num servidor que foi criado com a nova versão do motor.

> Note que antes da versão 10 do PostgreSQL, [a política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número (por exemplo, 9.5 a 9.6 foi considerada uma _grande_ atualização de versão).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão (por exemplo, 10.0 para 10.1 é uma versão _menor_ atualização, e 10 a 11 é uma _grande_ atualização de versão).

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço da VNet
- O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

### <a name="restoring-a-server"></a>Restaurar um servidor
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações de nível de preços que o servidor em que se baseia.
- O novo servidor criado durante uma restauração não possui as regras de firewall que existiam no servidor original. As regras de firewall têm de ser configuradas separadamente para este novo servidor.
- Restaurar um servidor eliminado não é suportado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, os caracteres UTF-8 não são totalmente suportados em PostgreSQL de código aberto no Windows, que afeta a Base de Dados Azure para PostgreSQL. Por favor, consulte a linha [do Bug #15476 no arquivo pós-quadrado](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

### <a name="gss-error"></a>Erro GSS
Se vir um erro relacionado com **o GSS,** é provável que utilize uma versão mais recente do cliente/controlador que o Azure Postgres Single Server ainda não suporta totalmente. Este erro é conhecido por afetar as [versões do condutor JDBC 42.2.15 e 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - Planeamos concluir a atualização até ao final de novembro. Considere usar uma versão de motorista de trabalho entretanto.
   - Ou, considere desativar o pedido do GSS.  Utilize um parâmetro de ligação como `gssEncMode=disable` .

## <a name="next-steps"></a>Passos seguintes
- Compreenda [o que está disponível em cada nível de preços](concepts-pricing-tiers.md)
- Saiba mais sobre [versões de base de dados postgresql suportadas](concepts-supported-versions.md)
- [Reveja como fazer o back up e restaurar um servidor na Base de Dados Azure para PostgreSQL utilizando o portal Azure](howto-restore-server-portal.md)
