---
title: Configurar a conectividade SSL na base de dados do Azure para o PostgreSQL – servidor único
description: Instruções e informações para configurar a base de dados do Azure para PostgreSQL - único servidor e os aplicativos associados corretamente utilizar ligações SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461846"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar a conectividade SSL na base de dados do Azure para o PostgreSQL – servidor único
Base de dados do Azure para PostgreSQL prefere ligar as suas aplicações de cliente para o serviço de PostgreSQL utilizando Secure Sockets Layer (SSL). A imposição de ligações SSL entre o servidor de base de dados e as suas aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir conexão SSL. Pode optar por desativar a exigência de SSL se a aplicação de cliente não suporta a conectividade SSL. 

## <a name="enforcing-ssl-connections"></a>A imposição de ligações de SSL
Para todas as base de dados para servidores PostgreSQL aprovisionados através do portal do Azure e CLI, imposição de ligações SSL está ativada por predefinição. 

Da mesma forma, as cadeias de ligação previamente definidas nas definições de "Cadeias de ligação" no seu servidor no portal do Azure incluem os parâmetros necessários para idiomas comuns ao ligar ao seu servidor de base de dados através de SSL. O parâmetro SSL varia com base no conector, por exemplo "ssl = true" ou "sslmode = exigir" ou "sslmode = necessária" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL
Opcionalmente, pode desativar a conectividade a imposição de SSL. Microsoft Azure recomenda a ativação da sempre **ligação impor SSL** definição para uma maior segurança.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Visite a sua base de dados do Azure para o servidor PostgreSQL e clique em **segurança de ligação**. Utilize o botão de alternar para ativar ou desativar a **ligação impor SSL** definição. Em seguida, clique em **guardar**. 

![Segurança da ligação - desativar impor SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Pode confirmar a definição visualizando os **descrição geral** página para ver a **estado de imposição do SSL** indicador.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar ou desativar a **imposição de ssl** usando o parâmetro `Enabled` ou `Disabled` valores respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Certifique-se seu aplicativo ou framework suporta ligações SSL
Algumas estruturas de aplicações que utilizam o PostgreSQL para os serviços de base de dados não habilitar o SSL por padrão durante a instalação. Se o servidor PostgreSQL impõe ligações SSL, mas a aplicação não está configurada para SSL, a aplicação não poderá ligar ao seu servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar ligações SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicações que necessitam de verificação de certificado para a conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificados local gerado a partir de um ficheiro de certificado autoridade de certificado (AC) de fidedigna (. cer) para ligar em segurança. O certificado para ligar a uma base de dados do Azure para PostgreSQL server está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Transfira o ficheiro de certificado e guarde-o à sua localização preferencial. 

### <a name="connect-using-psql"></a>Ligar com psql
O exemplo seguinte mostra como ligar ao servidor PostgreSQL com o utilitário de linha de comandos psql. Utilize o `sslmode=verify-full` definição da cadeia de ligação para impor a verificação de certificado SSL. Passe o caminho de ficheiro de certificados local para o `sslrootcert` parâmetro.

Segue-se um exemplo de cadeia de ligação de psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme que o valor passado para `sslrootcert` corresponde ao caminho de ficheiro para o certificado guardado.


## <a name="next-steps"></a>Passos Seguintes
Reveja as várias opções de conectividade de aplicativo no [bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
