---
title: Instalar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure | Documentos da Microsoft
description: Realojar IBM z/OS mainframe cargas de trabalho com o desenvolvimento do Micro Focus e testar o ambiente em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 46b135aabaefb3a94e4470927297fb696f216b7a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192535"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure

Este artigo mostra como configurar [Micro foco Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e [Micro foco Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) no Azure.

Uma carga de trabalho comuns no Azure é um ambiente de desenvolvimento e teste, porque é por isso, rentável e fácil de implementar e fechar. Com o Enterprise Server, Micro Focus criou uma das maiores mainframe rehosting plataformas disponíveis. Pode executar cargas de trabalho de z/OS num x86 mais barato plataforma no Azure através de máquinas virtuais Windows ou Linux (VMs).

Esta configuração utiliza VMs do Azure a executar a imagem do Windows Server 2016 no Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Esta configuração também se aplica ao Azure Stack.

O ambiente de desenvolvimento correspondente para Enterprise Server é programador empresarial, que é executado em qualquer um dos Microsoft Visual Studio 2017, Visual Studio Community (gratuito baixar), ou Eclipse. Este artigo mostra como implantá-la utilizando uma máquina de virtual do Windows Server 2016 que acompanha o Visual Studio 2017 instalado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique estes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software do Micro Focus e uma licença válida (ou licença de avaliação). Se for um cliente existente do Micro Focus, contacte o seu representante do Micro Focus. Caso contrário, [peça uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Obtenha a documentação para [Enterprise Server e o Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> É recomendado configurar um túnel de rede privada virtual (VPN) site a site ou uma jumpbox, pelo que pode controlar o acesso para as VMs do Azure.

## <a name="install-enterprise-server"></a>Instalar o servidor de Enterprise

1. Para obter uma melhor segurança e capacidade de gerenciamento, considere a criação de um novo grupo de recursos apenas para este projeto — por exemplo, **RGMicroFocusEntServer**. Utilize a primeira parte do nome no Azure para designar o tipo de recurso para que seja mais fácil encontrá-las numa lista.

2. Cria uma máquina virtual. No Azure Marketplace, selecione a máquina virtual e o sistema operacional desejado. Esta é uma configuração recomendada:

    - **Enterprise Server**: Selecione a VM de v3 ES2 (com 2 vCPUs e 16 GB de memória) com o Windows Server 2016 e o SQL Server 2017 instalado. Esta imagem está disponível no Azure Marketplace. Enterprise Server pode utilizar a base de dados do Azure SQL.

    - **Enterprise Developer**: Selecione a VM de B2ms (com 2 vCPUs e 8 GB de memória) com o Windows 10 e o Visual Studio instalado. Esta imagem está disponível no Azure Marketplace.

3. Sobre o **Noções básicas** painel, introduza o seu nome de utilizador e palavra-passe. Selecione o **subscrição** e **região/localização** que pretende utilizar para as VMs. Selecione **RGMicroFocusEntServer** para o grupo de recursos.

4. Coloque ambas as VMs na mesma rede virtual, para que possam comunicar entre si.

5. Aceite as predefinições para o resto das definições. Lembre-se de que o nome de utilizador e palavra-passe que criar para o administrador estas VMS.

6. Quando as máquinas virtuais tiverem sido criadas, abrir portas de entrada 9003, x86 e 80 para HTTP e a 3389 para RDP na máquina do servidor de Enterprise e 3389 na máquina do desenvolvedor.

7. Para iniciar sessão na máquina de virtual Enterprise Server, no portal do Azure, selecione a VM de v3 ES2. Vá para o **descrição geral** painel e selecione **Connect** para iniciar uma sessão RDP. Inicie sessão com as credenciais que criou para a VM.

8. A partir da sessão do RDP, carregar os seguintes dois ficheiros. Uma vez que este é o Windows, por isso, pode arrastar e largar os ficheiros para a sessão do RDP:

    - **es\_40. exe**, o ficheiro de instalação de servidor empresarial.

    - **mflic**, o ficheiro de licença correspondente — servidor empresarial não será carregado sem ele.

9. Faça duplo clique no ficheiro para iniciar a instalação. Na janela do primeiro, selecione a localização de instalação e aceite o contrato de licença de utilizador final.

     ![Ecrã de configuração de servidor de Enterprise de foco micro](media/01-enterprise-server.png)

     Quando a configuração estiver concluída, é apresentada a seguinte mensagem:

     ![Ecrã de configuração de servidor de Enterprise de foco micro](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Procurar atualizações

Após a instalação, certifique-se de que verifique a existência de quaisquer atualizações adicionais desde um número de pré-requisitos, tais como o Microsoft C++ Redistributable e o .NET Framework são instalados juntamente com o Enterprise Server.

### <a name="upload-the-license"></a>Carregar a licença

1. Inicie a administração de licença do Micro Focus.

2. Clique em **começar** \> **Gestor de licenciamento do Micro foco** \> **administração do licenciamento**e, em seguida, clique no **instalar** separador. Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 carateres. Por exemplo, para um arquivo, na **ficheiro de licença**, navegue para o **mflic** ficheiro carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo de administração do licenciamento de foco micro](media/03-enterprise-server.png)

3. Certifique-se de que carrega os Enterprise Server. Tente iniciar o site de administração do servidor de empresa a partir de um browser utilizando este URL <http://localhost:86/> . A página de administração de servidor empresarial é apresentada, conforme mostrado.

     ![Página de administração de servidor empresarial](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instalar o desenvolvedor empresariais no computador do desenvolvedor

1. Selecione o grupo de recursos que criou anteriormente (por exemplo, **RGMicroFocusEntServer**), em seguida, selecione a imagem de desenvolvedor.

2. Para iniciar sessão na máquina virtual, vá para o **descrição geral** painel e selecione **Connect**. Esta ação inicia uma sessão RDP. Inicie sessão com as credenciais que criou para a VM.

3. A partir da sessão do RDP, carregar os seguintes dois ficheiros (arrastar e soltar, se preferir):

    - **edvs2017.exe**, o ficheiro de instalação de servidor empresarial.

    - **mflic**, o ficheiro de licença correspondente (Enterprise Developer não será carregado sem ele).

4. Clique duas vezes o **edvs2017.exe** arquivo para iniciar a instalação. Na janela do primeiro, selecione a localização de instalação e aceite o contrato de licença de utilizador final. Se desejar, escolha **instalar o Rumba 9,5** para instalar este emulador do terminal, que provavelmente será necessário.

     ![Foco Enterprise Developer micro para caixa de diálogo de instalação do Visual Studio 2017](media/04-enterprise-server.png)

5. Após a configuração estiver concluída, é apresentada a seguinte mensagem:

     ![Mensagem de êxito de configuração](media/05-enterprise-server.png)

6. Inicie o Gestor de licença de foco de Micro, tal como fez para Enterprise Server. Escolher **começar** \> **Gestor de licenciamento do Micro foco** \> **administração do licenciamento**e clique no **instalar**separador.

7. Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 carateres. Por exemplo, para um arquivo, na **ficheiro de licença**, navegue para o **mflic** ficheiro carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo de administração do licenciamento de foco micro](/edia/07-enterprise-server.png)

Quando carrega o programador empresarial, a implementação de um ambiente de desenvolvimento e teste do Micro Focus no Azure está concluída!

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a aplicação de demonstração de banco](./demo.md)
