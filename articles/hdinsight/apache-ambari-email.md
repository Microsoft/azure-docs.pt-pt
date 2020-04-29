---
title: 'Tutorial: Configure Notificações de email apache Ambari no Azure HDInsight'
description: Este artigo descreve como usar sendGrid com Apache Ambari para notificações de e-mail.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082315"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Tutorial: Configure Notificações de email apache Ambari no Azure HDInsight

Neste tutorial, irá configurar notificações de e-mail da Apache Ambari utilizando o SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) simplifica a gestão e monitorização de um cluster HDInsight, fornecendo uma UI web e API REST de fácil utilização. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração. [SendGrid](https://sendgrid.com/solutions/) é um serviço de e-mail baseado na nuvem gratuito que fornece entrega de e-mail transacional fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os clientes do Azure podem desbloquear 25 000 mensagens de e-mail gratuitas por mês.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obtenha o nome de utilizador da Sendgrid
> * Configure notificações de e-mail apache Ambari

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail SendGrid. Consulte [como enviar e-mail utilizando sendGrid com Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) para obter instruções.

* Um cluster HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Obtenha o nome de utilizador da SendGrid

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao seu recurso SendGrid.

1. A partir da página 'Overview', selecione **Manage**, para ir à página web do SendGrid para a sua conta.

    ![Visão geral da SendGrid no portal azure](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. A partir do menu esquerdo, navegue até ao nome da sua conta e, em seguida, **detalhes da conta.**

    ![Navegação no painel de instrumentos SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Na página Dados da **Conta,** grave o nome de **utilizador**.

    ![Dados da conta SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Configure notificação de e-mail ambari

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir **da** lista de ações, selecione **'Gerir Notificações'.**

1. A partir da janela **'Gerir notificações de alerta',** selecione o **+** ícone.

    ![Ambari cria notificação de alerta](./media/apache-ambari-email/azure-portal-create-notification.png)

1. A partir do diálogo **De Alerta Criar,** forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome|Forneça um nome para a notificação.|
    |Grupos|Configure como desejado.|
    |Gravidade|Configure como desejado.|
    |Descrição|Opcional.|
    |Método|Deixe no **EMAIL**.|
    |E-mail para|Forneça e-mails para receber notificações, separadas por uma vírina.|
    |Servidor SMTP|`smtp.sendgrid.net`|
    |Porto SMTP|25 ou 587 (para ligações não encriptadas/TLS).|
    |E-mail de|Forneça um endereço de e-mail. O endereço não precisa de ser autêntico.|
    |Utilizar a autenticação|Selecione esta caixa de verificação.|
    |Nome de utilizador|Forneça o nome de utilizador SendGrid.|
    |Palavra-passe|Forneça a palavra-passe que usou quando criou o recurso SendGrid em Azure.|
    |Confirmação de Palavra-passe|Reintroduza a senha.|
    |Iniciar TLS|Selecione esta caixa de verificação|

    ![Ambari cria notificação de alerta](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selecione **Guardar**. Voltará à janela **'Gerir notificações de alerta'.**

1. A partir da janela **'Gerir notificações de alerta',** selecione **Fechar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar notificações de email da Apache Ambari utilizando o SendGrid. Use o seguinte para saber mais sobre Apache Ambari:

* [Gerir clusters do HDInsight através da IU da Web do Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Criar uma notificação de alerta](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
