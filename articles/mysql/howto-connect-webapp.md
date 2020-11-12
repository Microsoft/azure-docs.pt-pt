---
title: Ligue-se ao Azure App Service - Azure Database for MySQL
description: Instruções para como ligar corretamente um Serviço de Aplicações Azure existente à Base de Dados Azure para o MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 6355afe6ce5decbed029db4536b1b1b19f5a876c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541509"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ligue um serviço de aplicações Azure existente à Base de Dados Azure para o servidor MySQL
Este tópico explica como ligar um Serviço de Aplicações Azure existente à sua Base de Dados Azure para o servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [portal do Azure](https://portal.azure.com). Crie uma Base de Dados Azure para o servidor MySQL. Para obter mais informações, consulte [como criar a Base de Dados Azure para o servidor MySQL a partir do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou Como criar a Base de [Dados Azure para o servidor MySQL utilizando o CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente existem duas soluções para permitir o acesso de um Serviço de Aplicações Azure a uma Base de Dados Azure para o MySQL. Ambas as soluções envolvem a criação de regras de firewall ao nível do servidor.

## <a name="solution-1---allow-azure-services"></a>Solução 1 - Permitir serviços Azure
A Azure Database for MySQL fornece segurança de acesso usando uma firewall para proteger os seus dados. Ao ligar de um Serviço de Aplicações Azure à Base de Dados Azure para o servidor MySQL, tenha em mente que os IPs de saída do Serviço de Aplicações são de natureza dinâmica. A escolha da opção "Permitir o acesso aos serviços Azure" permitirá ao serviço de aplicações ligar-se ao servidor MySQL.

1. Na lâmina do servidor MySQL, sob o título Definições, clique em **'Connection Security'** para abrir a lâmina de segurança de ligação para a base de dados Azure para o MySQL.

   :::image type="content" source="./media/howto-connect-webapp/1-connection-security.png" alt-text="Portal Azure - clique em Connection Security":::

2. Selecione **ON** in **Permitir o acesso aos serviços Azure** e, em seguida, **guardar**.
   :::image type="content" source="./media/howto-connect-webapp/allow-azure.png" alt-text="Portal Azure - Permitir acesso ao Azure":::

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2 - Criar uma regra de firewall para permitir explicitamente iPs de saída
Pode adicionar explicitamente todos os IPs de saída do seu Serviço de Aplicações Azure.

1. Na lâmina 'Propriedades de Serviço de Aplicações', consulte o seu **ENDEREÇO IP OUTBOUND**.

   :::image type="content" source="./media/howto-connect-webapp/2_1-outbound-ip-address.png" alt-text="Portal Azure - Ver IPs de saída":::

2. Na lâmina de segurança MySQL Connection, adicione IPs de saída um a um.

   :::image type="content" source="./media/howto-connect-webapp/2_2-add-explicit-ips.png" alt-text="Portal Azure - Adicionar IPs explícitos":::

3. Lembre-se de **guardar** as suas regras de firewall.

Embora o serviço Azure App tente manter os endereços IP constantes ao longo do tempo, existem casos em que os endereços IP podem ser alterados. Por exemplo, isto pode ocorrer quando a aplicação recicla ou ocorre uma operação de escala, ou quando novos computadores são adicionados em centros de dados regionais do Azure para aumentar a capacidade. Quando os endereços IP mudam, a aplicação pode experimentar tempo de inatividade no caso de já não poder ligar-se ao servidor MySQL. Tenha em mente esta consideração ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração SSL
A Azure Database for MySQL tem SSL ativado por padrão. Se a sua aplicação não estiver a utilizar o SSL para ligar à base de dados, então tem de desativar o SSL no servidor MySQL. Para obter mais informações sobre como configurar o SSL, consulte [utilizar o SSL com a Base de Dados Azure para o MySQL](howto-configure-ssl.md).

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
Para obter mais informações sobre as cordas de ligação, consulte [as Cordas de Ligação](howto-connection-string.md).
