---
title: SSL-banco de dados do Azure para PostgreSQL-servidor único
description: Instruções e informações sobre como configurar a conectividade SSL para o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4f7fd3ea1c83e1bf5183aedf4fe894809884414c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903513"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar a conectividade SSL no banco de dados do Azure para PostgreSQL-servidor único
O banco de dados do Azure para PostgreSQL prefere conectar seus aplicativos cliente ao serviço PostgreSQL usando protocolo SSL (SSL). A imposição de conexões SSL entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados PostgreSQL está configurado para exigir conexão SSL. Você pode optar por desabilitar a exigência de SSL se o aplicativo cliente não oferecer suporte à conectividade SSL. 

## <a name="enforcing-ssl-connections"></a>Impondo conexões SSL
Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure e da CLI, a imposição de conexões SSL é habilitada por padrão. 

Da mesma forma, cadeias de conexão predefinidas nas configurações de "cadeias de conexão" em seu servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL
Opcionalmente, você pode desabilitar a imposição de conectividade SSL. Microsoft Azure recomenda sempre habilitar a configuração **impor conexão SSL** para aumentar a segurança.

> [!NOTE]
> Atualmente, a versão do TLS com suporte para o banco de dados do Azure para PostgreSQL é TLS 1,0, TLS 1,1, TLS 1,2.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Visite o servidor do banco de dados do Azure para PostgreSQL e clique em **segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **impor conexão SSL** . Em seguida, clique em **Guardar**. 

![Segurança da conexão-desabilitar impor SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Você pode confirmar a configuração exibindo a página **visão geral** para ver o indicador de **status SSL impor** .

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Você pode habilitar ou desabilitar o parâmetro de **imposição de SSL** usando valores `Enabled` ou `Disabled` respectivamente em CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões SSL
Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o SSL por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões SSL, mas o aplicativo não está configurado para SSL, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável (. cer) para se conectar com segurança. O certificado para se conectar a um servidor de banco de dados do Azure para PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Baixe o arquivo de certificado e salve-o em seu local preferido. 

### <a name="connect-using-psql"></a>Conectar usando psql
O exemplo a seguir mostra como se conectar ao seu servidor PostgreSQL usando o utilitário de linha de comando psql. Use a configuração `sslmode=verify-full` cadeia de conexão para impor a verificação de certificado SSL. Passe o caminho do arquivo de certificado local para o parâmetro `sslrootcert`.

Abaixo está um exemplo da cadeia de conexão do psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.


## <a name="next-steps"></a>Passos seguintes
Examine várias opções de conectividade de aplicativo em [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
