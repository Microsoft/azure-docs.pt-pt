---
title: SSL - Base de Dados Azure para PostgreSQL - Servidor Único
description: Instruções e informações sobre como configurar a conectividade SSL para a Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371669"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configure conectividade SSL na Base de Dados Azure para PostgreSQL - Servidor Único

A Base de Dados Azure para PostgreSQL prefere ligar as suas aplicações ao cliente ao serviço PostgreSQL utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o seu servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir a ligação SSL. Pode optar por desativar a necessidade de SSL se a sua aplicação de cliente não suportar a conectividade SSL.

## <a name="enforcing-ssl-connections"></a>Aplicação de ligações SSL

Para toda a Base de Dados Azure para servidores PostgreSQL aprovisionados através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão. 

Da mesma forma, as cordas de ligação que são pré-definidas nas definições "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor de base de dados utilizando o SSL. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a execução da SSL

Pode desativar opcionalmente a aplicação da conectividade SSL. O Microsoft Azure recomenda ativar sempre a definição de **ligação SSL de aplicação** para uma maior segurança.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Visite a sua Base de Dados Azure para o servidor PostgreSQL e clique em **segurança de ligação**. Utilize o botão de alternância para ativar ou desativar a definição de **ligação SSL de aplicação.** Em seguida, clique em **Guardar**.

![Segurança de Ligação - Desativar impor ssl](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Pode confirmar a definição visualizando a página **'Overview'** para ver o indicador de estado de aplicação do **SSL.**

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Pode ativar ou desativar o parâmetro **de execução ssl** utilizando valores `Enabled` ou `Disabled` respectivamente no Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta as ligações SSL

Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o SSL por defeito durante a instalação. Se o seu servidor PostgreSQL impor ligações SSL mas a aplicação não estiver configurada para o SSL, a aplicação pode não conseguir ligar-se ao servidor da base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Pedidos que requerem verificação de certificado para conectividade SSL

Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado de certificado confiável da Autoridade de Certificados (CA) para se ligar em segurança. O certificado de ligação a uma Base de Dados Azure para servidor PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

### <a name="connect-using-psql"></a>Conecte-se usando psql

O exemplo seguinte mostra como se conectar ao seu servidor PostgreSQL utilizando o utilitário de linha de comando psql. Utilize a definição de cadeia de ligação `sslmode=verify-full` para impor a verificação do certificado SSL. Passe o caminho do ficheiro de certificado local para o parâmetro `sslrootcert`.

O seguinte comando é um exemplo da cadeia de ligação psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme que o valor passado para `sslrootcert` corresponde ao caminho do ficheiro para o certificado que guardou.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>Conectividade TLS na Base de Dados Azure para o servidor Single PostgreSQL

Base de dados Azure para PostgreSQL - Servidor único suporta encriptação para clientes que se conectam ao seu servidor de base de dados utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede seguras entre o seu servidor de base de dados e aplicações de clientes, permitindo-lhe cumprir os requisitos de conformidade.

### <a name="tls-settings"></a>Definições tls

Os clientes têm agora a capacidade de impor a versão TLS para o cliente que se conecta à sua Base de Dados Azure para o servidor Single PostgreSQL. Para utilizar a opção TLS, utilize a definição mínima de opções de **versão TLS.** São permitidos os seguintes valores para esta definição de opção:

|  Definição mínima de TLS             | Versão TLS suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário tLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior          |
| TLS1_2                           | Versão TLS 1.2 e superior           |


Por exemplo, definir esta versão de definição Mínima de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes utilizando TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> Base de dados Azure para servidor único PostgreSQL falha no TLS sendo desativado para todos os novos servidores.
>
> Atualmente, as versões TLS suportadas pela Base de Dados Azure para PostgreSQL são TLS 1.0, 1.1 e 1.2.

Para saber como definir a definição de TLS para a sua Base de Dados Azure para o servidor Single PostgreSQL, consulte [como configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Passos seguintes

Reveja várias opções de conectividade de aplicações nas [bibliotecas de Ligação para base de dados Azure para PostgreSQL](concepts-connection-libraries.md).
