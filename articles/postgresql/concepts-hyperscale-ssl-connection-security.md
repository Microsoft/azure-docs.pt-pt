---
title: TLS - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Instruções e informações para configurar a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) e aplicações associadas para utilizar corretamente as ligações TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82580562"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure TLS na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
As ligações de aplicação do cliente ao nó coordenador de Hiperescala (Citus) requerem segurança da camada de transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). A aplicação das ligações TLS entre o servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

## <a name="enforcing-tls-connections"></a>Aplicação das ligações TLS
Para todas as bases de dados Azure para servidores PostgreSQL a provisionadas através do portal Azure, a aplicação das ligações TLS é ativada por padrão. 

Da mesma forma, as cadeias de ligação pré-definidas nas definições de "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor de base de dados utilizando o TLS. O parâmetro TLS varia em base no conector, por exemplo "ssl=true" ou "sslmode=requir" ou "sslmode=required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta ligações TLS
Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o TLS por padrão durante a instalação. Se o seu servidor PostgreSQL impor ligações TLS, mas a aplicação não estiver configurada para TLS, a aplicação poderá não conseguir ligar-se ao servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicações que requerem verificação de certificados para conectividade TLS
Em alguns casos, os pedidos requerem um arquivo de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectar de forma segura. O certificado de ligação a uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

### <a name="connect-using-psql"></a>Conecte-se usando psql
O exemplo a seguir mostra como ligar-se ao nó coordenador da Hiperescala (Citus) utilizando o utilitário da linha de comando psql. Utilize a `sslmode=verify-full` definição de cadeia de ligação para impor a verificação do certificado TLS. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

Abaixo está um exemplo da cadeia de ligação psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme que o valor passado corresponde `sslrootcert` ao caminho do ficheiro para o certificado que guardou.

## <a name="next-steps"></a>Passos seguintes
Aumentar ainda mais a segurança com [as regras de Firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](concepts-hyperscale-firewall-rules.md).
