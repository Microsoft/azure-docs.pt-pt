---
title: SSL/TLS - Base de Dados Azure para PostgreSQL - Servidor Único
description: Instruções e informações sobre como configurar a conectividade TLS para a Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 6660c5d40ffb8ecb338dd9cdf53f24cfe2911713
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043840"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configure a conectividade TLS na Base de Dados Azure para PostgreSQL - Servidor Único

A Azure Database for PostgreSQL prefere ligar as aplicações do seu cliente ao serviço PostgreSQL utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). A aplicação das ligações TLS entre o servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir a ligação TLS. Pode optar por desativar o TLS requerendo se a sua aplicação de cliente não suportar a conectividade TLS.

## <a name="enforcing-tls-connections"></a>Aplicação das ligações TLS

Para todas as bases de dados Azure para servidores PostgreSQL a provisionadas através do portal Azure e CLI, a aplicação das ligações TLS é ativada por padrão. 

Da mesma forma, as cadeias de ligação pré-definidas nas definições de "Cordas de Ligação" sob o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor de base de dados utilizando o TLS. O parâmetro TLS varia em base no conector, por exemplo "ssl=true" ou "sslmode=requir" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-tls"></a>Aplicação de configuração do TLS

Pode desativar opcionalmente a conectividade TLS. O Microsoft Azure recomenda ativar sempre a **definição de ligação Enforce SSL** para uma maior segurança.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Visite a sua Base de Dados de Azure para o servidor PostgreSQL e clique na **segurança de Ligação**. Utilize o botão de alternar para ativar ou desativar a **definição de ligação SSL De acionar.** Em seguida, clique em **Guardar**.

![Garantia de ligação - Desative a aplicação TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Pode confirmar a definição visualizando a página **'Vista Geral'** para ver o indicador **de estado de execução SSL.**

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Pode ativar ou desativar o parâmetro **de execução do ssl** utilizando `Enabled` ou `Disabled` valores respectivamente no Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta ligações TLS

Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o TLS por padrão durante a instalação. Se o seu servidor PostgreSQL impor ligações TLS, mas a aplicação não estiver configurada para TLS, a aplicação poderá não conseguir ligar-se ao servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicações que requerem verificação de certificados para conectividade TLS

Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. O certificado para ligar a uma base de dados Azure para o servidor PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida. (Ver os seguintes links para certificados para servidores em nuvens soberanas: [Azure Government](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).) 

### <a name="connect-using-psql"></a>Conecte-se usando psql

O exemplo a seguir mostra como ligar ao seu servidor PostgreSQL utilizando o utilitário psql da linha de comando. Utilize a `sslmode=verify-full` definição de cadeia de ligação para impor a verificação do certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

O seguinte comando é um exemplo da cadeia de ligação psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme que o valor passado corresponde `sslrootcert` ao caminho do ficheiro para o certificado que guardou.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Aplicação de TLS na Base de Dados Azure para servidor único postgresQL

A azure Database for PostgreSQL - O servidor único suporta a encriptação para clientes que se conectam ao seu servidor de base de dados utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações seguras de rede entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

### <a name="tls-settings"></a>Definições de TLS

A Azure Database for PostgreSQL single server fornece a capacidade de impor a versão TLS para as ligações do cliente. Para fazer cumprir a versão TLS, utilize a definição de option de **versão TLS mínima.** São permitidos os seguintes valores para a definição desta opção:

|  Definição mínima de TLS             | Versão TLS do Cliente suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário nenhum TLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior          |
| TLS1_2                           | Versão TLS 1.2 e superior           |


Por exemplo, definir esta versão de definição de TLS mínimo para TLS 1.0 significa que o seu servidor permitirá ligações de clientes que utilizem TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2+ e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> Por predefinição, a Base de Dados Azure para PostgreSQL não impõe uma versão TLS mínima (a definição `TLSEnforcementDisabled` ).
>
> Uma vez executada uma versão TLS mínima, não pode desativar a aplicação mínima da versão.

Para saber como configurar a definição de TLS para o seu Azure Database para o servidor Single PostgreSQL, consulte como [configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Próximos passos

Reveja várias opções de conectividade de aplicações nas [bibliotecas connection para Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Saiba como [configurar o TLS](howto-tls-configurations.md)