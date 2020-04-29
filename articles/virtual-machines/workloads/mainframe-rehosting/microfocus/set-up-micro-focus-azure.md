---
title: Instale micro Focus Enterprise Server 4.0 e Enterprise Developer 4.0 no Azure Microsoft Docs
description: Realoja as suas cargas de trabalho ibm z/OS utilizando o ambiente de desenvolvimento e teste micro Focus em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411201"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instale micro Focus Enterprise Server 4.0 e Enterprise Developer 4.0 no Azure

Este artigo mostra como configurar micro [Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e [Micro Focus Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) no Azure.

Uma carga de trabalho comum em Azure é um ambiente de desenvolvimento e teste. Este cenário é comum porque é tão rentável e fácil de implementar e derrubar. Com o Enterprise Server, a Micro Focus criou uma das maiores plataformas de realojamento do mainframe disponíveis. Pode executar cargas de trabalho z/OS numa plataforma x86 menos dispendiosa no Azure utilizando máquinas virtuais Windows ou Linux (VMs).

> [!NOTE]
> Em breve: Instruções para a instalação do [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) em VMs Azure.

Esta configuração utiliza VMs Azure que executam a imagem do Windows Server 2016 do Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Esta configuração também se aplica ao Azure Stack.

O ambiente de desenvolvimento correspondente para enterprise server é Enterprise Developer, que funciona no Microsoft Visual Studio 2017 ou mais tarde, Visual Studio Community (gratuito para descarregar) ou Eclipse. Este artigo mostra como implementá-lo usando uma máquina virtual Do Windows Server 2016 que vem com o Visual Studio 2017 ou posteriormente instalado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software Micro Focus e uma licença válida (ou licença de teste). Se for um cliente micro focus existente, contacte o seu representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

- Obtenha a documentação para [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> A melhor prática é criar um túnel de rede privada virtual (VPN) ou uma caixa de salto para que possa controlar o acesso aos VMs Azure.

## <a name="install-enterprise-server"></a>Instalar o Enterprise Server

1. Para uma melhor segurança e gestão, considere criar um novo grupo de recursos apenas para este projeto - por exemplo, **RGMicroFocusEntServer**. Utilize a primeira parte do nome em Azure para escolher o tipo de recurso para facilitar a sua identificação numa lista.

2. Cria uma máquina virtual. A partir do Azure Marketplace, selecione a máquina virtual e o sistema operativo que deseja. Aqui está uma configuração recomendada:

    - **Enterprise Server**: Selecione ES2 v3 VM (com 2 vCPUs e 16 GB de memória) com o Windows Server 2016 e o SQL Server 2017 instalados. Esta imagem está disponível no Azure Marketplace. O Enterprise Server também pode utilizar a Base de Dados Azure SQL.

    - **Desenvolvedor da empresa**: Selecione B2ms VM (com 2 vCPUs e 8 GB de memória) com o Windows 10 e o Visual Studio instalados. Esta imagem está disponível no Azure Marketplace.

3. Na secção **Basics,** introduza o seu nome de utilizador e palavra-passe. Selecione a **Subscrição** e **Localização/Região** que deseja utilizar para os VMs. Selecione **RGMicroFocusEntServer** para o Grupo de Recursos.

4. Coloque ambos os VMs na mesma rede virtual para que possam comunicar uns com os outros.

5. Aceite as predefinições para o resto das definições. Lembre-se do nome de utilizador e da palavra-passe que cria para o administrador destes VMs.

6. Quando as máquinas virtuais tiverem sido criadas, abra as portas de entrada 9003, 86 e 80 para HTTP e 3389 para RDP na máquina Do Servidor Empresarial e 3389 na máquina Developer.

7. Para iniciar sessão na máquina virtual do Enterprise Server, no portal Azure, selecione o ES2 v3 VM. Vá à secção **'Visão Geral'** e selecione **Connect** para lançar uma sessão de RDP. Inscreva-se utilizando as credenciais que criou para o VM.

8. A partir da sessão de PDR, carregue os dois seguintes ficheiros. Como está a usar o Windows, pode arrastar e deixar cair os ficheiros na sessão de RDP:

    - **es\_40.exe,** o ficheiro de instalação do Enterprise Server.

    - **mflic**, o ficheiro de licença correspondente — O Enterprise Server não carregará sem ele.

9. Clique duas vezes no ficheiro para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de utilizador final.

     ![Ecrã de configuração do servidor de empresa sintetiza](media/01-enterprise-server.png)

     Quando a configuração estiver completa, aparece a seguinte mensagem:

     ![Ecrã de configuração do servidor de empresa sintetiza](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Procurar atualizações

Após a instalação, certifique-se de verificar quaisquer atualizações adicionais, uma vez que vários pré-requisitos, tais como o Microsoft C++ Redistribuable e o .NET Framework são instalados juntamente com o Enterprise Server.

### <a name="upload-the-license"></a>Faça upload da licença

1. Inicie a Administração de Licenças de Micro Focus.

2. Clique em **Iniciar** \> **A Administração**de Licença seletiva \> do Gestor de **Licenças de Foco Micro Focus** e, em seguida, clique no separador **Instalar.** Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 caracteres. Por exemplo, para um ficheiro, no **ficheiro Licença,** navegue para o ficheiro **mflic** enviado anteriormente para o VM e selecione Licenças de **Instalação**.

     ![Caixa de diálogo da Administração de Licenças de Micro Focus](media/03-enterprise-server.png)

3. Verifique se o Servidor Da Empresa carrega. Tente lançar o site da Administração <http://localhost:86/> do Servidor empresarial a partir de um browser usando este URL . A página da Administração do Servidor da Empresa é apresentada como mostrado.

     ![Página de Administração do Servidor da Empresa](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instale o Desenvolvedor da Empresa na máquina de desenvolvimento

1. Selecione o grupo de recursos criado anteriormente (por exemplo, **RGMicroFocusEntServer),** e selecione a imagem do desenvolvedor.

2. Para iniciar sessão na máquina virtual, vá à secção **'Visão Geral'** e selecione **Connect**. Este sinal em lança uma sessão de RDP. Inscreva-se utilizando as credenciais que criou para o VM.

3. A partir da sessão de PDR, carregue os seguintes dois ficheiros (arrastar e largar se quiser):

    - **edvs2017.exe,** o ficheiro de instalação do Enterprise Server.

    - **mflic**, o ficheiro de licença correspondente (Enterprise Developer não carregará sem ele).

4. Clique duas vezes no ficheiro **edvs2017.exe** para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de utilizador final. Se desejar, escolha **instalar rumba 9.5** para instalar este emulador terminal, que provavelmente necessitará.

     ![Micro Focus Enterprise Developer para visual studio 2017 Setup log box](media/04-enterprise-server.png)

5. Após a configuração estar completa, aparece a seguinte mensagem:

     ![Configurar mensagem de sucesso](media/05-enterprise-server.png)

6. Inicie o Gestor de Licenças de Micro Focus, tal como fez para o Enterprise Server. Escolha **Iniciar** \> **a Administração de Licença**de Gestor \> de Licença sinuosa de **Micro Focus** e clique no separador **Instalar.**

7. Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 caracteres. Por exemplo, para um ficheiro, no **ficheiro Licença,** navegue para o ficheiro **mflic** enviado anteriormente para o VM e selecione Licenças de **Instalação**.

     ![Caixa de diálogo da Administração de Licenças de Micro Focus](media/07-enterprise-server.png)

Quando o Desenvolvedor da Empresa carrega, a sua implementação de um ambiente de desenvolvimento e teste de Micro Focus no Azure está completa!

## <a name="next-steps"></a>Passos seguintes

- [Configurar o pedido de demonstração de banco](./demo.md)
- [Executar o Servidor Empresarial em contentores Docker](./run-enterprise-server-container.md)
- [Migração de aplicações de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
