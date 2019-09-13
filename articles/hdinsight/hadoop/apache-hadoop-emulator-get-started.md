---
title: Aprenda a usar um Apache Hadoop sandbox-Emulator-Azure HDInsight
description: 'Para começar a aprender a usar o ecossistema Apache Hadoop, você pode configurar uma área restrita do Hadoop em Hortonworks em uma máquina virtual do Azure. '
keywords: emulador do Hadoop, área restrita do Hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: a1d31eb3fa6e37302e1afa5ebd36d04f75830dea
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918181"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introdução a uma área restrita do Apache Hadoop, um emulador em uma máquina virtual

Saiba como instalar o Apache Hadoop área restrita do Hortonworks em uma máquina virtual para saber mais sobre o ecossistema do Hadoop. A área restrita fornece um ambiente de desenvolvimento local para saber mais sobre o Hadoop, o Sistema de Arquivos Distribuído do Hadoop (HDFS) e o envio de trabalhos. Quando estiver familiarizado com o Hadoop, você poderá começar a usar o Hadoop no Azure criando um cluster HDInsight. Para obter mais informações sobre como começar, consulte Introdução [ao Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/). Baixe e instale-o [aqui](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Baixar e instalar a máquina virtual

1. Navegue até os [downloads do Cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Clique em **VIRTUALBOX** em **escolher tipo de instalação** para baixar a área restrita do Hortonworks mais recente em uma VM. Entre ou preencha o formulário de interesse do produto.

1. Clique no botão **HDP sandbox (mais recente)** para iniciar o download.

Para obter instruções sobre como configurar a área restrita, consulte [Guia de implantação e instalação do Sandbox](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Para baixar uma área restrita da versão mais antiga do HDP, consulte os links em **versões mais antigas**.

## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Abra o Oracle VM VirtualBox.
1. No menu **arquivo** , clique em **importar dispositivo**e, em seguida, especifique a imagem de área restrita Hortonworks.
1. Selecione a área restrita Hortonworks, clique em **Iniciar**e em **início normal**. Depois que a máquina virtual tiver concluído o processo de inicialização, ela exibirá as instruções de logon.

    ![Início normal](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Abra um navegador da Web e navegue até a URL exibida ( `http://127.0.0.1:8888`geralmente).

## <a name="set-sandbox-passwords"></a>Definir senhas de área restrita

1. Na etapa de **introdução** da página área restrita do Hortonworks, selecione **Exibir opções avançadas**. Use as informações desta página para fazer logon na área restrita usando SSH. Use o nome e a senha fornecidos.

   > [!NOTE]
   > Se você não tiver um cliente SSH instalado, poderá usar o SSH baseado na Web fornecido pelo pela máquina virtual em **http://localhost:4200/** .

    Na primeira vez que você se conectar usando o SSH, será solicitado que você altere a senha da conta raiz. Insira uma nova senha, que você usa ao fazer logon usando SSH.

2. Depois de conectado, insira o seguinte comando:

        ambari-admin-password-reset

    Quando solicitado, forneça uma senha para a conta de administrador do Ambari. Isso é usado quando você acessa a interface do usuário da Web do amAmbari.

## <a name="use-hive-commands"></a>Usar comandos do hive

1. Em uma conexão SSH com a área restrita, use o seguinte comando para iniciar o Shell do hive:

        hive
2. Depois que o Shell for iniciado, use o seguinte para exibir as tabelas que são fornecidas com a área restrita:

        show tables;
3. Use o seguinte para recuperar 10 linhas da `sample_07` tabela:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como usar o Visual Studio com a área restrita do Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)

* [Aprendendo a ropes da área restrita do Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Tutorial do Hadoop – introdução ao HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
