---
title: SSL-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Instruções e informações para configurar o banco de dados do Azure para PostgreSQL-Citus (hiperescala) e aplicativos associados para usar corretamente as conexões SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973990"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurar SSL no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
As conexões de aplicativo cliente com o nó de coordenador de hiperescala (Citus) exigem protocolo SSL (SSL). A imposição de conexões SSL entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="enforcing-ssl-connections"></a>Impondo conexões SSL
Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure, a imposição de conexões SSL é habilitada por padrão. 

Da mesma forma, cadeias de conexão predefinidas nas configurações de "cadeias de conexão" em seu servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões SSL
Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o SSL por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões SSL, mas o aplicativo não está configurado para SSL, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável (. cer) para se conectar com segurança. O certificado para se conectar a um banco de dados do Azure para PostgreSQL-Citus (hiperescala) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Baixe o arquivo de certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conectar usando psql
O exemplo a seguir mostra como se conectar ao seu nó de coordenador de hiperescala (Citus) usando o utilitário de linha de comando psql. Use a configuração `sslmode=verify-full` cadeia de conexão para impor a verificação de certificado SSL. Passe o caminho do arquivo de certificado local para o parâmetro `sslrootcert`.

Abaixo está um exemplo da cadeia de conexão do psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Passos seguintes
Aumente ainda mais a segurança com [as regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](concepts-hyperscale-firewall-rules.md).
