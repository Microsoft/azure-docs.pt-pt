---
title: Encriptação dupla da infraestrutura - Base de Dados Azure para PostgreSQL
description: Saiba como utilizar a encriptação dupla infraestrutura para adicionar uma segunda camada de encriptação com uma tecla gerida pelo serviço.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: d3a3582891ab3d3e4bfb53cbba0c0b2826cba56b
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123359"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Base de dados Azure para infraestrutura pós-SQL dupla encriptação

A Azure Database for PostgreSQL utiliza encriptação de armazenamento [de dados em repouso](concepts-security.md#at-rest) para dados usando as chaves geridas da Microsoft. Os dados, incluindo cópias de segurança, são encriptados no disco e esta encriptação está sempre acesa e não pode ser desativada. A encriptação utiliza o módulo criptográfico validado FIPS 140-2 e uma cifra AES de 256 bits para a encriptação de armazenamento Azure.

A encriptação dupla da infraestrutura adiciona uma segunda camada de encriptação usando chaves geridas pelo serviço. Utiliza o módulo criptográfico validado FIPS 140-2, mas com um algoritmo de encriptação diferente. Isto fornece uma camada adicional de proteção para os seus dados em repouso. A chave utilizada na encriptação dupla infraestrutura também é gerida pela Base de Dados Azure para o serviço PostgreSQL. A dupla encriptação da infraestrutura não é ativada por padrão, uma vez que a camada adicional de encriptação pode ter um impacto de desempenho.

> [!NOTE]
> Esta funcionalidade é suportada apenas para os níveis de preços "Final geral" e "Memory Optimized" na Base de Dados Azure para PostgreSQL.

A encriptação Infrastructure Layer tem o benefício de ser implementada na camada mais próxima do dispositivo de armazenamento ou dos fios de rede. A Azure Database for PostgreSQL implementa as duas camadas de encriptação utilizando chaves geridas pelo serviço. Apesar de ainda tecnicamente na camada de serviço, está muito perto do hardware que armazena os dados em repouso. Pode ainda ativar opcionalmente a encriptação de dados em repouso utilizando a [chave gerida pelo cliente](concepts-data-encryption-postgresql.md) para o servidor PostgreSQL for provisionado.  

A implementação nas camadas de infraestrutura também suporta uma diversidade de chaves. As infraestruturas devem estar cientes de diferentes aglomerados de máquinas e redes. Como tal, diferentes chaves são usadas para minimizar o raio de explosão de ataques de infraestrutura e uma variedade de falhas de hardware e rede. 

> [!NOTE]
> A utilização da encriptação dupla infraestrutura terá impacto no desempenho na Base de Dados Azure para o servidor PostgreSQL devido ao processo de encriptação adicional.

## <a name="benefits"></a>Benefícios

A dupla encriptação da infraestrutura para a Base de Dados de Azure para PostgreSQL proporciona os seguintes benefícios:

1. **Diversidade adicional de implementação de cripto** - A mudança planeada para encriptação baseada em hardware irá diversificar ainda mais as implementações, fornecendo uma implementação baseada em hardware, além da implementação baseada em software.
2. **Erros** de implementação - Duas camadas de encriptação na camada de infraestrutura protegem contra quaisquer erros na gestão de caching ou memória em camadas mais altas que expõem dados de texto simples. Além disso, as duas camadas também garantem contra erros na implementação da encriptação em geral.

A combinação destes proporciona uma forte proteção contra ameaças e fraquezas comuns usadas para atacar a criptografia.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Cenários suportados com infraestrutura dupla encriptação

As capacidades de encriptação fornecidas pela Azure Database para PostgreSQL podem ser usadas em conjunto. Abaixo está um resumo dos vários cenários que pode utilizar:

|  ##   | Encriptação padrão | Encriptação dupla de infraestrutura | Encriptação de dados utilizando chaves geridas pelo Cliente  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Sim*              | *Não*                             | *Não*                                         |
| 2     | *Sim*              | *Sim*                            | *Não*                                         |
| 3     | *Sim*              | *Não*                             | *Sim*                                        |
| 4     | *Sim*              | *Sim*                            | *Sim*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - O cenário 2 e 4 terão impacto no desempenho na Base de Dados Azure para o servidor PostgreSQL devido à camada adicional de encriptação da infraestrutura.
> - Configurar a encriptação dupla da Infraestrutura para a Base de Dados Azure para PostgreSQL só é permitida durante a criação do servidor. Uma vez que o servidor é provisionado, não é possível alterar a encriptação de armazenamento. No entanto, ainda é possível ativar a encriptação de Dados utilizando as chaves geridas pelo cliente para o servidor criado com/sem infraestrutura dupla encriptação.

## <a name="limitations"></a>Limitações

Para a Base de Dados Azure para PostgreSQL, o suporte para a dupla encriptação de infraestrutura utilizando a chave gerida pelo serviço tem as seguintes limitações:

* O suporte para esta funcionalidade está limitado aos níveis de preços otimizados para **fins gerais** e **memória.**
* Pode criar uma Base de Dados Azure para PostgreSQL com infraestrutura dupla encriptação ativada nas seguintes regiões:

   * E.U.A. Leste
   * E.U.A. Centro-Sul
   * E.U.A. Oeste 2
   
* Esta funcionalidade é suportada apenas em regiões e servidores, que suportam o armazenamento até 16 TB. Para a lista das regiões de Azure que suportam o armazenamento até 16 TB, consulte a [documentação](concepts-pricing-tiers.md#storage)de armazenamento .

    > [!NOTE]
    > - Todos os **novos** servidores PostgreSQL criados nas regiões acima listadas também suportam encriptação de dados com chaves de gestor de clientes. Neste caso, os servidores criados através de restauro pontual (PITR) ou réplicas de leitura não se qualificam como "novos".
    > - Para validar se o seu servidor a provisionado suporta até 16 TB, pode ir à lâmina de nível de preços no portal e ver se o slider de armazenamento pode ser movido até 16 TB. Se só conseguir mover o slider até 4 TB, o seu servidor poderá não suportar a encriptação com as teclas geridas pelo cliente; no entanto, os dados são criptografados usando chaves geridas pelo serviço em todos os momentos. Por favor, contacte AskAzureDBforPostgreSQL@service.microsoft.com se tiver alguma pergunta.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a [encriptação dupla infraestrutura para a base de dados Azure para PostgreSQL](howto-double-encryption.md).
