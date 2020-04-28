---
title: Aprenda a usar uma caixa de areia Apache Hadoop, emulador - Azure HDInsight
description: 'Para começar a aprender sobre a utilização do ecossistema Apache Hadoop, você pode configurar uma caixa de areia Hadoop a partir de Hortonworks em uma máquina virtual Azure. '
keywords: emulador de hadoop,hadoop sandbox
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73044760"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Começar com uma caixa de areia Apache Hadoop, um emulador numa máquina virtual

Aprenda a instalar a caixa de areia Apache Hadoop da Hortonworks numa máquina virtual para aprender sobre o ecossistema Hadoop. A caixa de areia proporciona um ambiente de desenvolvimento local para aprender sobre Hadoop, Hadoop Distributed File System (HDFS) e submissão de emprego. Uma vez familiarizado com Hadoop, pode começar a usar Hadoop no Azure criando um cluster HDInsight. Para obter mais informações sobre como começar, consulte [Get started with Hadoop on HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/). Descarregue e instale a partir [daqui](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Descarregue e instale a máquina virtual

1. Navegue nos downloads da [Cloudera.](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)

1. Clique em **VIRTUALBOX** em **Tipo de Instalação Escolha** para descarregar a mais recente Caixa de Areia Hortonworks num VM. Inscreva-se ou preencha o formulário de interesse do produto.

1. Clique no botão **HDP SANDBOX (MAIS RECENTE)** para iniciar o download.

Para obter instruções sobre a instalação da caixa de areia, consulte a [Instalação e o Guia](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)de Instalação da Caixa de Areia .

Para descarregar uma caixa de areia mais antiga da versão HDP, consulte os links em **Versões Mais Antigas**.

## <a name="start-the-virtual-machine"></a>Inicie a máquina virtual

1. Abra o Oracle VM VirtualBox.
1. No menu **'Ficheiro',** clique em **Importar Aparelho**, e depois especifique a imagem Hortonworks Sandbox.
1. Selecione a Caixa de Areia Hortonworks, clique em **Iniciar**, e depois **Arranque Normal**. Uma vez terminada a máquina virtual, o processo de arranque, apresenta instruções de login.

    ![início normal do gestor virtualbox](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Abra um navegador web e navegue `http://127.0.0.1:8888`para o URL apresentado (normalmente ).

## <a name="set-sandbox-passwords"></a>Definir palavras-passe de Sandbox

1. A partir do passo **inicial** da página Hortonworks Sandbox, selecione **Ver Opções Avançadas**. Utilize as informações desta página para iniciar sessão na caixa de areia utilizando o SSH. Utilize o nome e a senha fornecidos.

   > [!NOTE]
   > Se não tiver um cliente SSH instalado, pode utilizar o SSH baseado na **http://localhost:4200/** web fornecido pela máquina virtual em .

    A primeira vez que se conecta usando o SSH, é-lhe pedido que altere a palavra-passe para a conta raiz. Introduza uma nova senha, que utiliza quando inicia sessão utilizando o SSH.

2. Uma vez iniciado o sessão, insira o seguinte comando:

        ambari-admin-password-reset

    Quando solicitado, forneça uma senha para a conta de administração Ambari. Isto é usado quando acede ao Ambari Web UI.

## <a name="use-hive-commands"></a>Use comandos da Colmeia

1. A partir de uma ligação SSH à caixa de areia, utilize o seguinte comando para iniciar a concha da Colmeia:

        hive
2. Uma vez iniciada a concha, utilize o seguinte para visualizar as tabelas fornecidas com a caixa de areia:

        show tables;
3. Utilize o seguinte para recuperar 10 linhas da `sample_07` mesa:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passos seguintes

* [Saiba como usar o Estúdio Visual com a Caixa de Areia Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)

* [Aprender as cordas da Caixa de Areia Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Tutorial de Hadoop - Começar com HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
