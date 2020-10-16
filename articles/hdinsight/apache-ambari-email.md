---
title: 'Tutorial: Configurar notificações de email apache Ambari em Azure HDInsight'
description: Este artigo descreve como usar a SendGrid com Apache Ambari para notificações por e-mail.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 40f8c36b197b0c68b9f04a02dc7731877b27ddd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541667"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Tutorial: Configurar notificações de email apache Ambari em Azure HDInsight

Neste tutorial, irá configurar notificações de e-mail apache Ambari usando a SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) simplifica a gestão e monitorização de um cluster HDInsight, fornecendo uma UI web fácil de usar e REST API. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração. [SendGrid](https://sendgrid.com/solutions/) é um serviço de e-mail baseado na nuvem gratuito que fornece entrega de e-mail transacional fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os clientes do Azure podem desbloquear 25 000 mensagens de e-mail gratuitas por mês.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obter nome de utilizador Sendgrid
> * Configurar notificações de email apache Ambari

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail SendGrid. Consulte [como enviar e-mail usando SendGrid com Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) para obter instruções.

* Um aglomerado HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Obter nome de utilizador SendGrid

1. A partir do [portal Azure,](https://portal.azure.com)navegue para o seu recurso SendGrid.

1. A partir da página 'Vista Geral', **selecione Gerir**, para ir à página do SendGrid para a sua conta.

    ![Visão geral do SendGrid no portal Azure](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. A partir do menu esquerdo, navegue para o nome da sua conta e, em seguida, **detalhes da conta.**

    ![Navegação do painel sendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Na página Detalhes da **Conta,** registem o **nome de utilizador.**

    ![Detalhes da conta SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Configure a notificação de e-mail de Ambari

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir da lista de **ações** drop-down, **selecione ''Gestão de Notificações' ('Gestão)**

1. A partir da janela **'Notificações de Alerta' De Gestão,** selecione o **+** ícone.

    ![A screenshot mostra a caixa de diálogo de notificações de alerta de gestão.](./media/apache-ambari-email/azure-portal-create-notification.png)

1. A partir do diálogo **de Notificação de Alerta,** forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome|Forneça um nome para a notificação.|
    |Grupos|Configurar como desejado.|
    |Gravidade|Configurar como desejado.|
    |Descrição|Opcional.|
    |Método|Deixe no **EMAIL**.|
    |E-mail para|Forneça e-mails para receber notificações separadas por uma vírgula.|
    |Servidor SMTP|`smtp.sendgrid.net`|
    |Porto SMTP|25 ou 587 (para ligações não encriptadas/TLS).|
    |E-mail de|Forneça um endereço de e-mail. O endereço não precisa de ser autêntico.|
    |Utilizar a autenticação|Selecione esta caixa de verificação.|
    |Nome de utilizador|Forneça o nome de utilizador SendGrid.|
    |Palavra-passe|Forneça a palavra-passe que usou quando criou o recurso SendGrid em Azure.|
    |Confirmação de senha|Reentre na senha.|
    |Iniciar TLS|Selecione esta caixa de verificação|

    ![A screenshot mostra a caixa de diálogo de aviso de criar.](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selecione **Guardar**. Regressará à janela **de Notificações de Alerta de Gestão.**

1. A partir da janela **'Notificações de Alerta de Gestão',** selecione **Fechar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar notificações de e-mail apache Ambari usando a SendGrid. Use o seguinte para saber mais sobre Apache Ambari:

* [Manage HDInsight clusters by using the Apache Ambari Web UI](./hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)

* [Criar uma notificação de alerta](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
