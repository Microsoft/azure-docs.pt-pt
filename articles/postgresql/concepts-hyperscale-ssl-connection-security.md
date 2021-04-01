---
title: Segurança da Camada de Transporte (TLS) - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Instruções e informações para configurar a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) e aplicações associadas para utilizar corretamente as ligações TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87071462"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure TLS na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
O nó coordenador de Hiperescala (Citus) requer que as aplicações do cliente se conectem com a Segurança da Camada de Transporte (TLS). A aplicação do TLS entre o servidor de base de dados e as aplicações do cliente ajuda a manter os dados confidenciais em trânsito. As definições de verificação extra descritas abaixo também protegem contra ataques "homem-no-meio".

## <a name="enforcing-tls-connections"></a>Aplicação das ligações TLS
As aplicações utilizam uma "cadeia de ligação" para identificar a base de dados de destino e as definições para uma ligação. Clientes diferentes requerem configurações diferentes. Para ver uma lista de cadeias de ligação utilizadas por clientes comuns, consulte a secção **'Cordas de Ligação'** para o seu grupo de servidores no portal Azure.

Os parâmetros TLS `ssl` variam `sslmode` em função das capacidades do conector, por exemplo `ssl=true` ou `sslmode=require` . `sslmode=required`

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta ligações TLS
Algumas estruturas de aplicação não permitem o TLS por padrão para ligações PostgreSQL. No entanto, sem uma ligação segura, uma aplicação não pode ligar-se a um nó coordenador de Hiperescala (Citus). Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicações que requerem verificação de certificados para conectividade TLS
Em alguns casos, os pedidos requerem um arquivo de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) (.cer) para se conectarem de forma segura. O certificado de ligação a uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus) está localizado em https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Faça o download do ficheiro de certificado e guarde-o para a sua localização preferida.

> [!NOTE]
>
> Para verificar a autenticidade do certificado, pode verificar a sua impressão digital SHA-256 utilizando a ferramenta da linha de comando OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

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
