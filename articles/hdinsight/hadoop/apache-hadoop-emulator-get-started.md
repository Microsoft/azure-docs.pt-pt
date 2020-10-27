---
title: Aprenda a usar uma caixa de areia Apache Hadoop, emulador - Azure HDInsight
description: 'Para começar a aprender sobre a utilização do ecossistema Apache Hadoop, você pode configurar uma caixa de areia Hadoop da Hortonworks em uma máquina virtual Azure. '
keywords: emulador hadoop,hadoop sandbox
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 4f19797fa1c79261dc3459e2b924ed45d0b7e36e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547967"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Começa com uma caixa de areia Apache Hadoop, um emulador numa máquina virtual

Saiba como instalar a caixa de areia Apache Hadoop da Hortonworks numa máquina virtual para aprender sobre o ecossistema Hadoop. A caixa de areia fornece um ambiente de desenvolvimento local para aprender sobre Hadoop, Hadoop Distributed File System (HDFS) e submissão de emprego. Uma vez familiarizado com Hadoop, pode começar a usar Hadoop em Azure criando um cluster HDInsight. Para obter mais informações sobre como começar, consulte [Começar com Hadoop em HDInsight.](apache-hadoop-linux-tutorial-get-started.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/). Faça o download e instale a partir [daqui.](https://www.virtualbox.org/wiki/Downloads)

## <a name="download-and-install-the-virtual-machine"></a>Descarregue e instale a máquina virtual

1. Navegue pelos downloads da [Cloudera.](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)

1. Clique **em VIRTUALBOX** em **Escolha Tipo de Instalação** para descarregar a mais recente Caixa de Areia Hortonworks num VM. Inscreva-se ou preencha o formulário de juros do produto.

1. Clique no botão **HDP SANDBOX (MAIS RECENTE)** para iniciar o download.

Para obter instruções sobre a configuração da caixa de areia, consulte o [Guia de Implantação e Instalação da Caixa](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)de Areia .

Para descarregar uma caixa de areia de versão HDP mais antiga, consulte os links em **Versão Mais Antiga** .

## <a name="start-the-virtual-machine"></a>Inicie a máquina virtual

1. Abrir Oracle VM VirtualBox.
1. A partir do menu **Ficheiro,** clique em **"Import Appliance"** e, em seguida, especifique a imagem da Caixa de Areia Hortonworks.
1. Selecione a Caixa de Areia Hortonworks, clique **em Iniciar** e, em seguida, **Início Normal** . Uma vez terminada a máquina virtual, apresenta instruções de início de sessão.

    ![início normal do gestor de caixa virtual](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Abra um navegador web e navegue para o URL exibido `http://127.0.0.1:8888` (normalmente).

## <a name="set-sandbox-passwords"></a>Definir palavras-passe sandbox

1. A partir do passo **de arranque** da página De Areia Hortonworks, selecione **Ver Opções Avançadas** . Utilize as informações desta página para iniciar sessão na caixa de areia utilizando sSH. Use o nome e a senha fornecidas.

   > [!NOTE]
   > Se não tiver um cliente SSH instalado, pode utilizar o SSH baseado na web fornecido pela máquina virtual em **http://localhost:4200/** .

    A primeira vez que se conecta usando SSH, é solicitado que altere a palavra-passe para a conta raiz. Introduza uma nova palavra-passe, que utiliza quando iniciar sessão utilizando o SSH.

2. Uma vez iniciado o login, insira o seguinte comando:

    ```bash
    ambari-admin-password-reset
    ```

    Quando solicitado, forneça uma senha para a conta administrador ambari. Isto é usado quando acede à UI web Ambari.

## <a name="use-hive-commands"></a>Use comandos hive

1. De uma ligação SSH à caixa de areia, utilize o seguinte comando para iniciar a casca da Colmeia:

    ```bash
    hive
    ```

2. Uma vez iniciada a concha, utilize o seguinte para visualizar as tabelas que estão fornecidas com a caixa de areia:

    ```hiveql
    show tables;
    ```

3. Utilize o seguinte para recuperar 10 linhas da `sample_07` tabela:

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Passos seguintes

* [Saiba como usar o Visual Studio com a Caixa de Areia Hortonworks](./apache-hadoop-visual-studio-tools-get-started.md)

* [Aprender as cordas da Caixa de Areia de Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop tutorial - Começar com HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)