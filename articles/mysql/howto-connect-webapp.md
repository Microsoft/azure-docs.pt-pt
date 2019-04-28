---
title: Ligar o serviço de aplicações do Azure existente para a base de dados do Azure para MySQL
description: Instruções sobre como ligar corretamente um serviço de aplicações do Azure existente para a base de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: eb2fee7c76bcf29aee2dcd70d7975d7631bb23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459305"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ligar um serviço de aplicações do Azure existente para a base de dados do Azure para o servidor MySQL
Este tópico explica como ligar um serviço de aplicações do Azure existente para a base de dados do Azure para o servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [Portal do Azure](https://portal.azure.com). Crie uma base de dados do Azure para o servidor MySQL. Para obter detalhes, consulte [como criar a base de dados do Azure para servidor MySQL a partir do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [como criar a base de dados do Azure para o servidor MySQL com a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, existem duas soluções para ativar o acesso a partir de um serviço de aplicações do Azure para uma base de dados do Azure para MySQL. As duas soluções envolvem como configurar regras de firewall ao nível do servidor.

## <a name="solution-1---allow-azure-services"></a>Solução 1 - permitir que os serviços do Azure
Base de dados do Azure para MySQL fornece segurança de acesso ao utilizar uma firewall para proteger os seus dados. Ao ligar de um serviço de aplicações do Azure, a base de dados do Azure, para o servidor MySQL, tenha em atenção que os IPs saída do serviço de aplicações são dinâmicos, por natureza. Escolher a opção "Permitir acesso aos serviços do Azure" permitirá que o serviço de aplicações para ligar ao servidor MySQL.

1. No painel do servidor MySQL, sob as configurações, clique em **segurança de ligação** para abrir o painel de segurança da ligação da base de dados do Azure para MySQL.

   ![Portal do Azure - clique em segurança de ligação](./media/howto-connect-webapp/1-connection-security.png)

2. Selecione **ON** na **permitir o acesso aos serviços do Azure**, em seguida, **guardar**.
   ![Portal do Azure – acesso de permitir que o Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2 - criar uma regra de firewall para permitir explicitamente os IPs de saída
Pode adicionar explicitamente todos os IPs de saída do seu serviço de aplicações do Azure.

1. No painel de propriedades do serviço de aplicação, ver seus **endereço IP de saída**.

   ![Portal do Azure – IPs de saída do modo de exibição](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. No painel de segurança da ligação ao MySQL, adicione IPs de saída individualmente.

   ![Portal do Azure – adicionar IPs explícita](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Lembre-se **guardar** suas regras de firewall.

Embora o serviço de aplicações do Azure tenta manter os endereços IP constante ao longo do tempo, há casos em que podem alterar os endereços IP. Por exemplo, isto pode ocorrer quando ocorre a causam a reciclagem das aplicações ou de uma operação de dimensionamento, ou quando novos computadores forem adicionados dados regionais do Azure centra-se para aumentar a capacidade. Quando os endereços IP mudam, a aplicação pode sofrer períodos de inatividade no caso de que já não consegue estabelecer ligação ao servidor MySQL. Tenha essa consideração em mente ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração de SSL
Base de dados do Azure para MySQL tem SSL ativado por predefinição. Se seu aplicativo não estiver a utilizar SSL para ligar à base de dados, terá de desativar o SSL no servidor MySQL. Para obter detalhes sobre como configurar o SSL, consulte [através de SSL com a base de dados do Azure para MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre cadeias de ligação, consulte [cadeias de ligação](howto-connection-string.md).
