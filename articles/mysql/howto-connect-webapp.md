---
title: Conectar-se ao serviço de Azure App-banco de dados do Azure para MySQL
description: Instruções sobre como conectar corretamente um serviço de Azure App existente ao banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 63ef9ac55fcfaebfd58ae1ccdb34107d41900be5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770548"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Conectar um serviço de Azure App existente ao banco de dados do Azure para servidor MySQL
Este tópico explica como conectar um serviço de Azure App existente ao banco de dados do Azure para o servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [portal do Azure](https://portal.azure.com). Crie um servidor de banco de dados do Azure para MySQL. Para obter detalhes, consulte [como criar um banco de dados do Azure para servidor MySQL do portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [como criar um banco de dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, há duas soluções para habilitar o acesso de um serviço de Azure App para um banco de dados do Azure para MySQL. Ambas as soluções envolvem a configuração de regras de firewall no nível de servidor.

## <a name="solution-1---allow-azure-services"></a>Solução 1-permitir serviços do Azure
O banco de dados do Azure para MySQL fornece segurança de acesso usando um firewall para proteger seus dados. Ao se conectar de um serviço de Azure App ao banco de dados do Azure para servidor MySQL, tenha em mente que os IPs de saída do serviço de aplicativo são dinâmicos por natureza. A escolha da opção "permitir acesso aos serviços do Azure" permitirá que o serviço de aplicativo se conecte ao servidor MySQL.

1. Na folha do servidor MySQL, no título configurações, clique em **segurança de conexão** para abrir a folha segurança de conexão para o banco de dados do Azure para MySQL.

   ![portal do Azure clique em segurança de conexão](./media/howto-connect-webapp/1-connection-security.png)

2. Selecione **ativado** em **permitir acesso aos serviços do Azure**e, em seguida, **salve**.
   ![portal do Azure-permitir o acesso do Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2-criar uma regra de firewall para permitir explicitamente os IPs de saída
Você pode adicionar explicitamente todos os IPs de saída do seu serviço de Azure App.

1. Na folha de propriedades do serviço de aplicativo, exiba seu **endereço IP de saída**.

   ![Portal do Azure-exibir IPs de saída](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Na folha segurança de conexão do MySQL, adicione IPs de saída um por um.

   ![Portal do Azure-adicionar IPs explícitos](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Lembre-se de **salvar** suas regras de firewall.

Embora o serviço de Azure App tente manter os endereços IP constantes ao longo do tempo, há casos em que os endereços IP podem mudar. Por exemplo, isso pode ocorrer quando o aplicativo é reciclado ou uma operação de escala ocorre, ou quando novos computadores são adicionados aos data centers regionais do Azure para aumentar a capacidade. Quando os endereços IP são alterados, o aplicativo pode sofrer tempo de inatividade caso ele não possa mais se conectar ao servidor MySQL. Tenha essa consideração em mente ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração de SSL
O banco de dados do Azure para MySQL tem SSL habilitado por padrão. Se seu aplicativo não estiver usando SSL para se conectar ao banco de dados, você precisará desabilitar o SSL no servidor MySQL. Para obter detalhes sobre como configurar o SSL, consulte [usando o SSL com o banco de dados do Azure para MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre cadeias de conexão, consulte [cadeias de conexão](howto-connection-string.md).
