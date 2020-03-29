---
title: SSL - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Instruções e informações para configurar a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) e aplicações associadas para utilizar corretamente as ligações SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973990"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure SSL em Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
As ligações de aplicação do cliente ao nó coordenador de Hiperescala (Citus) requerem camada de tomadas seguras (SSL). A aplicação de ligações SSL entre o seu servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

## <a name="enforcing-ssl-connections"></a>Aplicação de ligações SSL
Para toda a Base de Dados Azure para servidores PostgreSQL aprovisionados através do portal Azure, a aplicação das ligações SSL é ativada por padrão. 

Da mesma forma, as cordas de ligação que são pré-definidas nas definições "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor de base de dados utilizando o SSL. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta as ligações SSL
Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o SSL por defeito durante a instalação. Se o seu servidor PostgreSQL impor ligações SSL mas a aplicação não estiver configurada para o SSL, a aplicação pode não conseguir ligar-se ao servidor da base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Pedidos que requerem verificação de certificado para conectividade SSL
Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado de certificado confiável da Autoridade de Certificados (CA) para se ligar em segurança. O certificado de ligação a uma Base de Dados Azure para PostgreSQL - Hyperscale (Citus) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

### <a name="connect-using-psql"></a>Conecte-se usando psql
O exemplo seguinte mostra como ligar-se ao seu nó coordenador de Hiperescala (Citus) utilizando o utilitário de linha de comando psql. Utilize `sslmode=verify-full` a definição de corda de ligação para impor a verificação do certificado SSL. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

Abaixo está um exemplo da cadeia de ligação psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme que o `sslrootcert` valor passado corresponde ao caminho do ficheiro para o certificado que guardou.

## <a name="next-steps"></a>Passos seguintes
Aumente ainda mais a segurança com as regras da Firewall na Base de [Dados Azure para PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
