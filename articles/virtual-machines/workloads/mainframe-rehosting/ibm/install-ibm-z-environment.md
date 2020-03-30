---
title: Instale o ambiente IBM zD&T dev/test no Azure Microsoft Docs
description: Implementar o IBM Z Development and Test Environment (zD&T) na infraestrutura da Máquina Virtual Azure (VM) como serviço (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025933"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instale ibm zD&T dev/test environment no Azure

Para criar um ambiente de dev/teste para cargas de trabalho principais nos Sistemas IBM Z, pode implementar o IBM Z Development and Test Environment (zD&T) na infraestrutura da Máquina Virtual Azure (VM) como serviço (IaaS).

Com o zD&T, pode aproveitar as economias de custos da plataforma x86 para os seus ambientes de desenvolvimento e teste menos críticos, e depois empurrar as atualizações para um ambiente de produção do Sistema Z. Para mais informações, consulte as instruções de [instalação IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

O Suporte Azure e Azure Stack suportam as seguintes versões:

- zD&T Edição Pessoal
- zD&T Sysplex Paralelo
- zD&T Enterprise Edition

Todas as edições do zD&T funcionam apenas em sistemas X86 Linux, e não no Windows Server. A Enterprise Edition é suportada em Red Hat Enterprise Linux (RHEL) ou Ubuntu/Debian. As imagens VM RHEL e Debian estão disponíveis para o Azure.

Este artigo mostra-lhe como configurar o zD&T Enterprise Edition no Azure para que possa utilizar o servidor web zD&T Enterprise Edition para criação e gestão de ambientes. Instalar o zD&T não instala ambientes. Deve criá-los separadamente como pacotes de instalação. Por exemplo, as distribuições controladas por desenvolvedores de aplicações (ADCD) são imagens de volume de ambientes de teste. Estão contidas em imagens zip na distribuição mediática disponível pela IBM. Veja como [configurar um ambiente ADCD em Azure](demo.md).

Para mais informações, consulte o [zD&T Visão Geral](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no Centro de Conhecimento da IBM.

Este artigo mostra-lhe como configurar z desenvolvimento e ambiente de teste (zD&T) Enterprise Edition no Azure. Em seguida, você pode usar o zD&t Enterprise Edition web servidor para criar e gerir ambientes baseados em Z em Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A IBM permite que a edição da Empresa zD&seja instalada apenas em ambientes de dev/teste,*e não* em ambientes de produção.

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Precisa de acesso aos meios de comunicação, que só estão disponíveis para clientes e parceiros da IBM. Para mais informações, contacte o seu representante da IBM ou consulte as informações de contacto no website [do zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- Um [servidor de licenciamento.](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html) Isto é necessário para o acesso aos ambientes. A forma como o cria depende da forma como licencia o software da IBM:

     - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para aceder a todas as partes do software. Deve obter isto da IBM.

     - **O servidor de licenciamento baseado em software** requer que você configurar um servidor centralizado para a gestão das chaves de licenciamento. Este método é preferido e requer que configurar as chaves que recebe da IBM no servidor de gestão.

## <a name="create-the-base-image-and-connect"></a>Criar a imagem base e ligar

1. No portal Azure, [crie um VM](/azure/virtual-machines/linux/quick-create-portal) com a configuração do sistema operativo que deseja. Este artigo assume um B4ms VM (com 4 vCPUs e 16 GB de memória) executando Ubuntu 16.04.

2. Após a criação do VM, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor web.

3. Obtenha as credenciais SSH mostradas na lâmina **de visão geral** do VM através do botão **Connect.** Selecione o separador **SSH** e copie o comando de logon SSH para a área de reparação.

4. Aceda a uma [concha](/azure/cloud-shell/quickstart) bash do seu PC local e colhe o comando. Será no formulário **ssh\<user\>\@\<id\>IP Address**. Quando solicitado para as suas credenciais, insira-as para estabelecer uma ligação com o seu diretório Home.

## <a name="copy-the-installation-file-to-the-server"></a>Copiar o ficheiro de instalação para o servidor

O ficheiro de instalação do servidor web é **ZDT\_Instalar\_EE\_V12.0.1.1.tgz**. Está incluído nos meios de comunicação fornecidos pela IBM. Tem de enviar este ficheiro para o seu Ubuntu VM.

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
> Este comando copia o ficheiro de instalação para o diretório ZDT no seu diretório Home, que varia dependendo se o seu cliente executa windows ou Linux.

## <a name="install-the-enterprise-edition"></a>Instale a Edição Enterprise

1. Vá ao diretório ZDT e descomprima\_o\_ficheiro ZDT\_Install EE V12.0.1.tgz utilizando os seguintes comandos:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Executar o instalador:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o Enterprise Server.

4. Pressione **Entrar** e ler atentamente os contratos de licença. No final da licença, insira **Sim** para prosseguir.

5. Quando solicitado a alterar a senha para o recém-criado utilizador, **o ibmsys1,** utilize o comando **sudo passwd ibmsys1** e introduza a nova senha.

6. Para verificar se a instalação foi bem sucedida entrar

    ```
    dpkg -l | grep zdtapp
    ```

7. Verifique se a saída contém o **zdtapp de cadeia 12.0.0.0,** indicando que o gás da embalagem foi instalado com sucesso

### <a name="starting-enterprise-edition"></a>Edição Enterprise Inicial

Tenha em mente que, quando o servidor web começa, funciona sob o ID do utilizador zD&T que foi criado durante o processo de instalação.

1. Para iniciar o servidor web, utilize o ID do utilizador raiz para executar o seguinte comando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a saída de URL pelo script, que se parece com:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Colhe o URL num navegador web para abrir o componente de gestão para a sua instalação zD&T.

## <a name="next-steps"></a>Passos seguintes

[Criar uma distribuição controlada por desenvolvedores de aplicações (ADCD) em IBM zD&T v1](./demo.md)
