---
title: Instalar o IBM zD & o ambiente de desenvolvimento/teste T no Azure | Documentos da Microsoft
description: Desenvolvimento do Ddeploy IBM Z e o ambiente de teste (zD & T) na infraestrutura de Máquina Virtual (VM) do Azure como um serviço (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 5de5f2f48504a711d4b7cff614abdd4214d0ba09
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739727"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalar o IBM zD & o ambiente de desenvolvimento/teste T no Azure

Para criar um ambiente de dev/test para cargas de trabalho de mainframe IBM Z sistemas, pode implementar IBM Z desenvolvimento e de ambiente de teste (zD & T) na infraestrutura de Máquina Virtual (VM) do Azure como um serviço (IaaS).

Com zD & T, pode tirar partido das reduções de custos de x86 plataforma para ambientes de desenvolvimento e teste menos críticos e, em seguida, as atualizações de volta para um ambiente de produção do sistema Z de push. Para obter mais informações, consulte a [instruções de instalação do IBM ZD & T](http://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e o Azure Stack suportam as seguintes versões:

- zD & T Personal Edition
- zD & Sysplex paralela de T
- zD & T Enterprise Edition

Todas as edições do zD & T ser executado apenas no x86 sistemas Linux, não o Windows Server. Enterprise Edition é suportado no Red Hat Enterprise Linux (RHEL) ou no Ubuntu/Debian. Imagens RHEL e Debian VM estão disponíveis para o Azure.

Este artigo mostra-lhe como configurar a zD & T Enterprise Edition no Azure, pelo que pode utilizar o zD & servidor de web de T Enterprise Edition para a criação e gestão de ambientes. Instalar zD & T não instala quaisquer ambientes. Tem de criar separadamente como pacotes de instalação. Por exemplo, os desenvolvedores controlado distribuições (ADCD) da aplicação são imagens de volume de ambientes de teste. Estão incluídos nas imagens de zip sobre a distribuição de suporte de dados disponível da IBM. Veja como [configurar um ambiente de ADCD no Azure](demo.md).

Para obter mais informações, consulte a [zD & Descrição geral de T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no Centro de dados de conhecimento da IBM.

Este artigo mostra-lhe como configurar o desenvolvimento de Z e de ambiente de teste (zD & T) Enterprise Edition no Azure. Em seguida, pode utilizar o zD & servidor de web de T Enterprise Edition para criar e gerir ambientes baseados em Z no Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> IBM permite zD & T Enterprise Edition para ser instalado em ambientes de dev/test apenas —*não* ambientes de produção.

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Precisa de acesso ao suporte de dados, o que está disponível apenas para clientes da IBM e parceiros. Para obter mais informações, contacte o seu representante da IBM ou consulte as informações de contacto sobre o [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) Web site.

- R [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isto é necessário para o acesso aos ambientes. A forma como criá-la depende de como licenciamento de software da IBM:

     - **Servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contém os Tokens Rational necessárias ao acesso a todas as partes do software. Tem de obter isso da IBM.

     - **Servidor de licenciamento baseado em software** requer que defina um servidor centralizado para a gestão das chaves de licenciamento. Esse método é preferível e tem de configurar as chaves que recebe do IBM no servidor de gestão.

## <a name="create-the-base-image-and-connect"></a>Criar a imagem base e ligue-se

1. No portal do Azure, [criar uma VM](/azure/virtual-machines/linux/quick-create-portal) com a configuração do sistema operativo que pretende. Este artigo pressupõe que uma VM de B4ms (com 4 e 16 GB de memória de vCPUs) a executar o Ubuntu 16.04.

2. Depois da VM é criada, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor web.

3. Obter as credenciais SSH mostradas no **descrição geral** painel da VM através do **Connect** botão. Selecione o **SSH** separador e copie o comando de início de sessão SSH para a área de transferência.

4. Inicie sessão num [shell de Bash](/azure/cloud-shell/quickstart) do seu computador local e a colar o comando. É o formato **ssh\<id de utilizador\>\@\<endereço IP\>**. Quando lhe forem pedidas as suas credenciais, introduza-os para estabelecer uma ligação para o seu diretório de Home.

## <a name="copy-the-installation-file-to-the-server"></a>Copie o ficheiro de instalação para o servidor

O ficheiro de instalação para o servidor web está **ZDT\_instale\_EE\_V12.0.0.1.tgz**. Está incluído no suporte de dados fornecida pela IBM. Tem de carregar este ficheiro para a VM do Ubuntu.

1. A linha de comandos, introduza o seguinte comando para se certificar de que tudo o que é atualizado na imagem recentemente criada:

    ```
    sudo apt-get update
    ```

2. Crie o diretório deve ser instalado:

    ```
    mkdir ZDT
    ```

3. Copie o ficheiro a partir do seu computador local para a VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Este comando copia o ficheiro de instalação para o diretório ZDT no seu diretório inicial, que varia consoante se o cliente executa o Windows ou Linux.

## <a name="install-the-enterprise-edition"></a>Instalar a edição Enterprise

1. Ir para o diretório ZDT e descomprimir o ZDT\_instale\_EE\_ficheiro V12.0.0.1.tgz através dos seguintes comandos:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Execute o instalador:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o servidor da empresa.

4. Prima **Enter** e leia atentamente os contratos de licença. No final da licença, introduza **Sim** para continuar.

5. Quando lhe for pedido para alterar a palavra-passe para o utilizador recém-criado, **ibmsys1**, utilize o comando **sudo passwd ibmsys1** e introduza a palavra-passe nova.

6. Para verificar se a instalação foi bem-sucedida introduza

    ```
    dpkg -l | grep zdtapp
    ```

7. Certifique-se de que a saída contém a cadeia de caracteres **zdtapp 12.0.0.0**, indicando que a gasolina do pacote foi instalado com êxito

### <a name="starting-enterprise-edition"></a>Starting Enterprise Edition

Tenha em atenção que quando o servidor web é iniciado, ele é executado sob o ID de utilizador zD & T que foi criado durante o processo de instalação.

1. Para iniciar o servidor web, utilize a ID de utilizador de raiz para executar o seguinte comando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a saída de URL pelo script, que é semelhante a:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Cole o URL de um navegador da web para abrir o componente de gerenciamento para sua zD & instalação de T.

## <a name="next-steps"></a>Passos Seguintes

[Definir se um aplicativo desenvolvedores controlado distribuição (ADCD) na IBM zD & T v1](./demo.md)
