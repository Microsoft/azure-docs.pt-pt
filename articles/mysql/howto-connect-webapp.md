---
title: Ligue ao Serviço de Aplicações Azure - Base de Dados Azure para MySQL
description: Instruções para como ligar adequadamente um serviço de aplicações Azure existente à Base de Dados Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062466"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ligue um serviço de aplicações Azure existente à Base de Dados Azure para servidor MySQL
Este tópico explica como ligar um serviço de aplicações Azure existente à sua Base de Dados Azure para servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [portal do Azure](https://portal.azure.com). Crie uma Base de Dados Azure para o servidor MySQL. Para mais detalhes, consulte como criar base de [dados Azure para servidor MySQL a partir do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou como criar base de dados [Azure para servidor MySQL utilizando cli](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente existem duas soluções que permitem o acesso de um Serviço de Aplicações Azure a uma Base de Dados Azure para mySQL. Ambas as soluções envolvem a criação de regras de firewall ao nível do servidor.

## <a name="solution-1---allow-azure-services"></a>Solução 1 - Permitir serviços Azure
A Base de Dados Azure para o MySQL fornece segurança de acesso utilizando uma firewall para proteger os seus dados. Ao ligar-se de um Serviço de Aplicações Azure à Base de Dados Azure para o servidor MySQL, lembre-se que os IPs de saída do Serviço de Aplicações são de natureza dinâmica. A escolha da opção "Permitir o acesso aos serviços Azure" permitirá que o serviço de aplicações se conecte ao servidor MySQL.

1. Na lâmina do servidor MySQL, sob a rubrica Definições, clique em **Segurança de Ligação** para abrir a lâmina de segurança de ligação para a Base de Dados Azure para MySQL.

   ![Portal Azure - clique em Segurança de Ligação](./media/howto-connect-webapp/1-connection-security.png)

2. Selecione **ON** em **Permitir o acesso aos serviços Azure,** em **seguida, guardar**.
   ![Portal Azure - Permitir o acesso ao Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2 - Criar uma regra de firewall para permitir explicitamente iPs de saída
Pode adicionar explicitamente todos os IPs de saída do seu Serviço de Aplicações Azure.

1. Na lâmina do Serviço de Aplicações, veja o seu **ENDEREÇO IP outbound**.

   ![Portal Azure - Ver IPs de saída](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Na lâmina de segurança MySQL Connection, adicione iPs de saída um a um.

   ![Portal Azure - Adicionar IPs explícitos](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Lembre-se de **guardar** as suas regras de firewall.

Embora o serviço de Aplicações Azure tente manter os endereços IP constantes ao longo do tempo, existem casos em que os endereços IP podem mudar. Por exemplo, isto pode ocorrer quando a aplicação recicla ou ocorre uma operação de escala, ou quando novos computadores são adicionados nos centros de dados regionais do Azure para aumentar a capacidade. Quando os endereços IP mudam, a aplicação pode experimentar tempo de inatividade caso não possa mais ligar-se ao servidor MySQL. Tenha em mente esta consideração ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração SSL
A Base de Dados Azure para MySQL tem O SSL ativado por padrão. Se a sua aplicação não estiver a utilizar o SSL para se ligar à base de dados, então precisa de desativar o SSL no servidor MySQL. Para mais detalhes sobre como configurar o SSL, consulte [A Utilização de SSL com Base de Dados Azure para MySQL](howto-configure-ssl.md).

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
Para obter mais informações sobre as cordas de ligação, consulte as Cordas de [Ligação](howto-connection-string.md).
