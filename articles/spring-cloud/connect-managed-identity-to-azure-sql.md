---
title: Use identidade gerida para ligar Azure SQL à app Azure Spring Cloud
description: Configurar identidade gerida para ligar o Azure SQL a uma aplicação Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378793"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Utilize uma identidade gerida para ligar a Base de Dados Azure SQL a uma aplicação Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo mostra-lhe como criar uma identidade gerida para uma aplicação Azure Spring Cloud e usá-la para aceder à Base de Dados Azure SQL.

[A Azure SQL Database](https://azure.microsoft.com/services/sql-database/) é o serviço de base de dados inteligente, escalável e relacional construído para a nuvem. Está sempre atualizado, com funcionalidades automatizadas e alimentadas por IA que otimizam o desempenho e a durabilidade. Opções de armazenamento sem servidor e hiperescala escala automaticamente os recursos a pedido, para que possa focar-se na construção de novas aplicações sem se preocupar com o tamanho do armazenamento ou a gestão de recursos.

## <a name="prerequisites"></a>Pré-requisitos
Este exemplo utiliza os seguintes recursos.
* Siga o [tutorial de Dados de primavera JPA](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) para providenciar uma Base de Dados Azure SQL e faça com que funcione com uma aplicação Java localmente
* Siga o tutorial de identidade gerido atribuído pelo [sistema Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) para a disponibilização de uma aplicação Azure Spring Cloud com MI ativada

## <a name="grant-permission-to-the-managed-identity"></a>Conceder permissão à Identidade Gerida
Ligue ao seu servidor SQL e execute a seguinte consulta SQL:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

<MIName>Segue-se a regra: `<service instance name>/apps/<app name>` , por exemplo, myspringcloud/apps/sqldemo. Também pode consultar o MIName com Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Configure a sua app Java para usar identidade gerida
Abra o `src/main/resources/application.properties` ficheiro e adicione no final da seguinte `Authentication=ActiveDirectoryMSI;` linha. Certifique-se de que utiliza o valor correto para $ variável $AZ_DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Construa e implemente a app para Azure Spring Cloud
Reconstrua a app e implemente-a na aplicação Azure Spring Cloud, prevista no segundo ponto de bala em Pré-Requisitos. Agora tem uma aplicação Boot de mola, autenticada por uma Identidade Gerida, que utiliza o JPA para armazenar e recuperar dados de uma Base de Dados Azure SQL em Azure Spring Cloud.

## <a name="next-steps"></a>Passos seguintes

* [Como aceder ao blob de armazenamento com identidade gerida em Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Saiba mais sobre identidades geridas para recursos Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticar nuvem de primavera azure com cofre chave em ações do GitHub](./spring-cloud-github-actions-key-vault.md)