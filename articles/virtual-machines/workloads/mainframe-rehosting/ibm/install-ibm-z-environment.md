---
title: Instale o ambiente IBM zD&T dev/test em Azure | Microsoft Docs
description: Implementar o Ambiente de Desenvolvimento e Teste IBM Z (zD&T) na infraestrutura da Máquina Virtual Azure (VM) como serviço (IaaS).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: abcaff5979aed38ce47df08cc953829f3003f7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561556"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instale o ambiente ibm zD&T dev/test em Azure

Para criar um ambiente dev/teste para cargas de trabalho de computador principal em Sistemas IBM Z, pode implementar o AMBIENTE de Desenvolvimento e Teste IBM Z (zD&T) na infraestrutura Azure Virtual Machine (VM) como um serviço (IaaS).

Com o zD&T, pode aproveitar as economias de custos da plataforma x86 para os seus ambientes de desenvolvimento e teste menos críticos e, em seguida, empurrar as atualizações de volta para um ambiente de produção do Z System. Para mais informações, consulte as [instruções de instalação da IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e Azure Stack suportam as seguintes versões:

- zD&T Edição Pessoal
- ZD&T Sysplex Paralelo
- zD&T Enterprise Edition

Todas as edições do ZD&T funcionam apenas em sistemas X86 Linux, e não Windows Server. A Enterprise Edition é suportada em Red Hat Enterprise Linux (RHEL) ou Ubuntu/Debian. As imagens RHEL e Debian VM estão disponíveis para Azure.

Este artigo mostra-lhe como configurar o ZD&T Enterprise Edition em Azure para que possa utilizar o servidor web ZD&T Enterprise Edition para a criação e gestão de ambientes. A instalação do zD&T não instala nenhum ambiente. Deve criá-las separadamente como pacotes de instalação. Por exemplo, os Desenvolvedores de Aplicações Distribuídas Controladas (ADCD) são imagens de volume de ambientes de teste. Estão contidas em imagens zip na distribuição dos meios de comunicação disponíveis na IBM. Veja como [configurar um ambiente ADCD em Azure](demo.md).

Para mais informações, consulte a [visão geral do zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no Centro de Conhecimento da IBM.

Este artigo mostra-lhe como configurar a Z Development and Test Environment (zD&T) Enterprise Edition on Azure. Em seguida, você pode usar o zD&servidor web T Enterprise Edition para criar e gerir ambientes baseados em Z em Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A IBM permite que o ZD&T Enterprise Edition seja instalado apenas em ambientes dev/teste,*não* em ambientes de produção.

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Precisa de acesso aos meios de comunicação, que estão disponíveis apenas para clientes e parceiros da IBM. Para mais informações, contacte o seu representante da IBM ou consulte as informações de contacto no website [do ZD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isto é necessário para o acesso aos ambientes. A forma como o cria depende da forma como licencia o software da IBM:

     - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para aceder a todas as partes do software. Tem de o obter da IBM.

     - **O servidor de licenciamento baseado em software** requer que você crie um servidor centralizado para a gestão das chaves de licenciamento. Este método é preferido e requer que você configura as chaves que recebe da IBM no servidor de gestão.

## <a name="create-the-base-image-and-connect"></a>Crie a imagem base e conecte-se

1. No portal Azure, [crie um VM](../../../linux/quick-create-portal.md) com a configuração do sistema operativo que pretende. Este artigo assume um VM B4ms (com 4 vCPUs e 16 GB de memória) executando Ubuntu 16.04.

2. Após a criação do VM, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor web.

3. Obtenha as credenciais SSH mostradas na lâmina **de visão geral** do VM através do botão **Connect.** Selecione o separador **SSH** e copie o comando de início de ssh para a área de transferência.

4. Inicie sessão numa [casca bash](../../../../cloud-shell/quickstart.md) do seu PC local e cole o comando. Será na forma **ssh. \<user id\> \@ \<IP Address\>** Quando solicitado para as suas credenciais, insira-as para estabelecer uma ligação com o seu diretório home.

## <a name="copy-the-installation-file-to-the-server"></a>Copie o ficheiro de instalação para o servidor

O ficheiro de instalação do servidor web é **ZDT \_ Install \_ EE \_ V12.0.0.1.tgz**. Está incluído nos meios de comunicação fornecidos pela IBM. Tem de enviar este ficheiro para o seu Ubuntu VM.

1. A partir da linha de comando, insira o seguinte comando para se certificar de que tudo está atualizado na imagem recém-criada:

    ```
    sudo apt-get update
    ```

2. Crie o diretório para instalar para:

    ```
    mkdir ZDT
    ```

3. Copie o ficheiro da sua máquina local para o VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Este comando copia o ficheiro de instalação para o diretório ZDT no seu diretório Home, que varia consoante o seu cliente executa o Windows ou o Linux.

## <a name="install-the-enterprise-edition"></a>Instalar a Enterprise Edition

1. Vá ao diretório ZDT e descomprimir o ficheiro ZDT \_ Install \_ EE \_ V12.0.0.1.tgz utilizando os seguintes comandos:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Executar o instalador:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o Enterprise Server.

4. Pressione **Insira** e leia atentamente os contratos de licença. No final da licença, insira **Sim** para prosseguir.

5. Quando solicitado para alterar a palavra-passe para o utilizador recém-criado, **o ibmsys1**, use o comando **sudo passwd ibmsys1** e introduza a nova senha.

6. Para verificar se a instalação foi bem sucedida, insira

    ```
    dpkg -l | grep zdtapp
    ```

7. Verifique se a saída contém o zdtapp de corda **12.0.0.0**, indicando que o gás de embalagem foi instalado com sucesso

### <a name="starting-enterprise-edition"></a>Edição Empresarial Inicial

Tenha em mente que, quando o servidor web começa, funciona sob o ID do utilizador ZD&que foi criado durante o processo de instalação.

1. Para iniciar o servidor web, utilize o ID do utilizador raiz para executar o seguinte comando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a saída de URL pelo script, que parece:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Cole o URL num navegador web para abrir o componente de gestão para a instalação do zD&T.

## <a name="next-steps"></a>Passos seguintes

[Configurar uma Distribuição Controlada de Desenvolvedores de Aplicações (ADCD) em IBM zD&T v1](./demo.md)
