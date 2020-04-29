---
title: TLS - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Instruções e informações para configurar a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) e aplicações associadas para utilizar corretamente as ligações TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422326"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure TLS em Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
As ligações de aplicação do cliente ao nó coordenador de Hiperescala (Citus) requerem segurança da camada de transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). Impor as ligações TLS entre o seu servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

## <a name="enforcing-tls-connections"></a>Impor ligações TLS
Para toda a Base de Dados Azure para servidores PostgreSQL aprovisionados através do portal Azure, a aplicação das ligações TLS é ativada por padrão. 

Da mesma forma, as cordas de ligação que são pré-definidas nas definições "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao seu servidor de base de dados utilizando TLS. O parâmetro TLS varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta as ligações TLS
Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o TLS por defeito durante a instalação. Se o seu servidor PostgreSQL impor ligações TLS mas a aplicação não estiver configurada para TLS, a aplicação pode não conseguir ligar-se ao servidor da base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Pedidos que requerem verificação de certificado para conectividade TLS
Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado de certificado confiável da Autoridade de Certificados (CA) para se ligar em segurança. O certificado de ligação a uma Base de Dados Azure para PostgreSQL - Hyperscale (Citus) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

### <a name="connect-using-psql"></a>Conecte-se usando psql
O exemplo seguinte mostra como ligar-se ao seu nó coordenador de Hiperescala (Citus) utilizando o utilitário de linha de comando psql. Utilize `sslmode=verify-full` a definição de corda de ligação para impor a verificação do certificado TLS. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

Abaixo está um exemplo da cadeia de ligação psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme que o `sslrootcert` valor passado corresponde ao caminho do ficheiro para o certificado que guardou.

## <a name="next-steps"></a>Passos seguintes
Aumente ainda mais a segurança com as regras da Firewall na Base de [Dados Azure para PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
