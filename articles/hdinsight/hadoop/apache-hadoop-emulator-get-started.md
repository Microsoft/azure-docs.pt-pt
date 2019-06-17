---
title: Aprenda a utilizar uma área de segurança do Apache Hadoop - emulador - Azure HDInsight
description: 'Para começar a aprender sobre a utilização do ecossistema do Apache Hadoop, pode configurar um sandbox do Hadoop da Hortonworks numa máquina virtual do Azure. '
keywords: emulador do hadoop, sandbox do hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393141"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introdução à área de segurança do Apache Hadoop, um emulador numa máquina virtual

Saiba como instalar o Apache Hadoop sandbox da Hortonworks numa máquina virtual para saber mais sobre o ecossistema Hadoop. A área de segurança fornece um ambiente de desenvolvimento local para saber mais sobre o Hadoop, Hadoop Distributed File System (HDFS) e submissão de tarefas. Depois de se familiarizar com o Hadoop, pode começar a utilizar o Hadoop no Azure através da criação de um cluster do HDInsight. Para obter mais informações sobre como começar, consulte [introdução ao Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Oracle VirtualBox](https://www.virtualbox.org/). Transfira e instale-o a partir [aqui](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Transfira e instale a máquina virtual
1. Navegue para o [Cloudera transfere](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Clique em **VIRTUALBOX** sob **Escolher tipo de instalação** para transferir a Sandbox da Hortonworks mais recente numa VM. Inicie sessão ou preencher o formulário de interesse do produto.

1. Clique no botão **SANDBOX HDP (versão mais RECENTE)** para iniciar o download.

Para obter instruções sobre como configurar a proteção de segurança, consulte [instalar o guia de implantação de área restrita e](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Para transferir um sandbox de versão mais antiga do HDP, consulte os links em **versões mais antigas**.

## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Abra o Oracle VM VirtualBox.
2. Do **arquivo** menu, clique em **importar aplicação**e, em seguida, especifique a imagem de Sandbox da Hortonworks.
1. Selecione a Sandbox da Hortonworks, clique em **começar**e, em seguida **Normal iniciar**. Depois da máquina virtual tem de terminar o processo de inicialização, apresenta instruções de início de sessão.

    ![Início normal](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Abra um browser e navegue para o URL apresentado (normalmente `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Definir palavras-passe de Sandbox

1. Do **começar** passo da página de Sandbox da Hortonworks, selecione **opções avançadas de vista**. Utilize as informações desta página para iniciar sessão para a área de segurança através de SSH. Utilize o nome e a palavra-passe fornecida.

   > [!NOTE]
   > Se não tiver um cliente SSH instalado, pode utilizar o SSH baseada na web, fornecido pela máquina virtual em **http://localhost:4200/** .

    Na primeira vez que se liga através de SSH, lhe for pedido para alterar a palavra-passe para a conta raiz. Introduza uma palavra-passe nova, o que utilizar quando iniciar sessão através de SSH.

2. Depois de iniciar sessão, introduza o seguinte comando:

        ambari-admin-password-reset

    Quando lhe for pedido, forneça uma palavra-passe para a conta de administrador do Ambari. Isto é utilizado ao aceder à IU Web do Ambari.

## <a name="use-hive-commands"></a>Utilizar comandos do Hive

1. A partir de uma ligação de SSH para a área de segurança, utilize o seguinte comando para iniciar a Hive shell:

        hive
2. Assim que tiver iniciado o shell, utilize o seguinte para ver as tabelas que são fornecidas com a área de segurança:

        show tables;
3. Utilize o seguinte para obter 10 linhas do `sample_07` tabela:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como utilizar o Visual Studio com a Sandbox da Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Aprender tudo de que a Sandbox da Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Hadoop - guia de introdução HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

