---
title: TLS - Base de Dados Azure para PostgreSQL - Servidor Único
description: Instruções e informações sobre como configurar a conectividade TLS para a Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141735"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configure conectividade TLS na Base de Dados Azure para PostgreSQL - Servidor Único

A Base de Dados Azure para PostgreSQL prefere ligar as suas aplicações ao cliente ao serviço PostgreSQL utilizando a Transport Layer Security (TLS), anteriormente conhecida como Secure Sockets Layer (SSL). Impor as ligações TLS entre o seu servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir a ligação TLS. Pode optar por desativar a necessidade de TLS se a sua aplicação cliente não suportar a conectividade TLS.

## <a name="enforcing-tls-connections"></a>Impor ligações TLS

Para toda a Base de Dados Azure para servidores PostgreSQL aprovisionados através do portal Azure e CLI, a aplicação das ligações TLS é ativada por padrão. 

Da mesma forma, as cordas de ligação que são pré-definidas nas definições "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao seu servidor de base de dados utilizando TLS. O parâmetro TLS varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-tls"></a>Configurar a execução do TLS

Pode desativar opcionalmente a aplicação da conectividade TLS. O Microsoft Azure recomenda ativar sempre a definição de **ligação SSL de aplicação** para uma maior segurança.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Visite a sua Base de Dados Azure para o servidor PostgreSQL e clique em **segurança de ligação**. Utilize o botão de alternância para ativar ou desativar a definição de **ligação SSL de aplicação.** Em seguida, clique em **Guardar**.

![Segurança de Ligação - Desativar a aplicação de TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Pode confirmar a definição visualizando a página **'Overview'** para ver o indicador de estado de aplicação do **SSL.**

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Pode ativar ou desativar o `Enabled` parâmetro `Disabled` **de execução ssl** utilizando ou valores respectivamente no Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta as ligações TLS

Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o TLS por defeito durante a instalação. Se o seu servidor PostgreSQL impor ligações TLS mas a aplicação não estiver configurada para TLS, a aplicação pode não conseguir ligar-se ao servidor da base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Pedidos que requerem verificação de certificado para conectividade TLS

Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado de certificado confiável da Autoridade de Certificados (CA) para se ligar em segurança. O certificado de ligação a uma Base de Dados https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemAzure para servidor PostgreSQL está localizado em . Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

### <a name="connect-using-psql"></a>Conecte-se usando psql

O exemplo seguinte mostra como se conectar ao seu servidor PostgreSQL utilizando o utilitário de linha de comando psql. Utilize `sslmode=verify-full` a definição de corda de ligação para impor a verificação do certificado TLS/SSL. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

O seguinte comando é um exemplo da cadeia de ligação psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme que o `sslrootcert` valor passado corresponde ao caminho do ficheiro para o certificado que guardou.

## <a name="next-steps"></a>Passos seguintes

Reveja várias opções de conectividade de aplicações nas [bibliotecas de Ligação para base de dados Azure para PostgreSQL](concepts-connection-libraries.md).
