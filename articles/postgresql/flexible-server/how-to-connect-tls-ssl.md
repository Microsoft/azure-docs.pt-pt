---
title: Conectividade encriptada utilizando TLS/SSL na Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Instruções e informações sobre como ligar utilizando o TLS/SSL na Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d47c7c6b980df46ee44a44758313571506e79e44
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551208"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Conectividade encriptada usando segurança da camada de transporte na base de dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Azure Database for PostgreSQL - Flexible Server suporta ligar as aplicações do seu cliente ao serviço PostgreSQL utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

A Azure Database for PostgreSQL - Flexible Server suporta ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2+) e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão negadas. Para todos os servidores flexíveis, a aplicação das ligações TLS está ativada e não é possível desativar o TLS/SSL para a ligação ao servidor flexível.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplicações que requerem verificação de certificados para conectividade TLS/SSL
Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Base de Dados Azure para PostgreSQL - Servidor Flexível utiliza *DigiCert Global Root CA*. Faça o download deste certificado necessário para comunicar através do SSL da [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e guarde o ficheiro de certificado para a sua localização preferida. Por exemplo, este tutorial `c:\ssl` utiliza.


### <a name="connect-using-psql"></a>Conecte-se usando psql
Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível.

Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor.

O exemplo a seguir mostra como ligar-se ao seu servidor utilizando a interface psql de linha de comando. Utilize a `sslmode=verify-full` definição de cadeia de ligação para impor a verificação do certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Confirme que o valor passado corresponde `sslrootcert` ao caminho do ficheiro para o certificado que guardou.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta ligações TLS

Alguns quadros de aplicação que utilizam o PostgreSQL para os seus serviços de base de dados não permitem o TLS por padrão durante a instalação. O seu servidor PostgreSQL aplica ligações TLS, mas se a aplicação não estiver configurada para TLS, a aplicação poderá não conseguir ligar-se ao servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="next-steps"></a>Passos seguintes
- [Criar e gerir a Base de Dados Azure para postgreSQL - rede virtual de servidor flexível utilizando O Azure CLI](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [networking em Azure Database for PostgreSQL - Servidor Flexível](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para regras de firewall pósgresQL - Flexível Servidor](./concepts-networking.md#public-access-allowed-ip-addresses)
