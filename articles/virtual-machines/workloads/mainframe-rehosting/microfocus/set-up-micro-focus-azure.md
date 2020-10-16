---
title: Instalar Micro Focus Enterprise Server 5.0 e Enterprise Developer 5.0 em Azure / Microsoft Docs
description: Neste artigo, aprenda a instalar o Micro Focus Enterprise Server 5.0 e o Enterprise Developer 5.0 no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 11fde4480f4e2ed9feda00fa2af0e5937a77b9ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986883"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Instalar Micro Focus Enterprise Server 5.0 e Enterprise Developer 5.0 em Azure

Este artigo mostra como configurar [o Micro Focus Enterprise Server 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) e o Micro Focus Enterprise Developer [5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) no Microsoft Azure.

Uma carga de trabalho comum em Azure é um ambiente de desenvolvimento e teste. Este cenário é comum porque é tão rentável e fácil de implementar e derrubar. Com o Enterprise Server, a Micro Focus criou uma das maiores plataformas de rehosting do mainframe disponíveis. Pode executar cargas de trabalho z/OS numa plataforma x86 mais barata no Azure utilizando máquinas virtuais Windows ou Linux (VMs).

Esta configuração utiliza VMs Azure que executam a imagem do Windows Server 2016 a partir do Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Esta configuração também se aplica ao Azure Stack.

O ambiente de desenvolvimento correspondente para o Enterprise Server é o Enterprise Developer, que funciona no Microsoft Visual Studio 2017 ou mais tarde, Visual Studio Community (gratuito para download), ou Eclipse. Este artigo mostra como implementá-lo usando uma máquina virtual Do Windows Server 2016 que vem com o Visual Studio 2017 ou posteriormente instalada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

-   Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

-   O software Micro Focus e uma licença válida (ou licença de julgamento). Se for um cliente micro focus existente, contacte o seu representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

-   Obtenha a documentação para [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/).

    > [!Note]
    > Existem algumas opções para controlar o acesso aos seus VMs:
    > -   Uma boa prática é criar [o Azure Bastion.](https://azure.microsoft.com/services/azure-bastion/)
    > -   Um túnel [de rede privada virtual local (VPN).](../../../../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md)
    > -   Um VM de caixa de salto.

## <a name="install-enterprise-server"></a>Instalar o Enterprise Server

1.  Para uma melhor segurança e gerenciabilidade, considere criar um novo grupo de recursos apenas para este projeto - por exemplo, **RGMicroFocusEntServer**. Utilize a primeira parte do nome em Azure para escolher o tipo de recurso para facilitar a sua posição numa lista.

2.  Cria uma máquina virtual. A partir do Azure Marketplace, selecione a máquina virtual e o sistema operativo que pretende. Aqui está uma configuração recomendada:

    -   **Servidor da Empresa:** Selecione **ES2 v3 VM** (com 2 vCPUs e 16 GB de memória) com o Windows Server 2016 e SQL Server 2017 instalados. Esta imagem está disponível no Mercado Azure. O Enterprise Server também pode utilizar a Base de Dados Azure SQL.

    -   **Desenvolvedor de empresas:** Selecione **B2ms VM** (com 2 vCPUs e 8 GB de memória) com o Windows 10 e o Visual Studio instalados. Esta imagem está disponível no Mercado Azure.

3.  Na secção **Básico, insira** o seu nome de utilizador e senha. Selecione a **Subscrição** e **Localização/Região** que pretende utilizar para os VMs. Selecione **RGMicroFocusEntServer** para o grupo de recursos.

4.  Coloque ambos os VMs na mesma rede virtual para que possam comunicar uns com os outros.

5.  Aceite os predefinidos para o resto das definições. Lembre-se do nome de utilizador e palavra-passe que cria para o administrador destes VMs.

6.  Quando as máquinas virtuais tiverem sido criadas, abra as portas de entrada **9003, 86** e **80** para HTTP e **3389** para o Protocolo de Ambiente de Trabalho Remoto (RDP) na máquina do Servidor Empresarial e **3389** na máquina do Desenvolvedor.

7.  Para iniciar seduca na máquina virtual do Enterprise Server, no portal Azure, selecione o ES2 v3 VM. Vá à secção **'Vista Geral'** e selecione **Connect** para lançar uma sessão RDP. Inscreva-se usando as credenciais que criou para o VM.

8.  A partir da sessão rdp, carregue os dois ficheiros seguintes. Como está a usar o Windows, pode arrastar e largar os ficheiros na sessão RDP:

    -   `es\_50.exe`, o ficheiro de instalação do Enterprise Server.

    -   `mflic`, o ficheiro de licença correspondente — O Enterprise Server não carregará sem ele.

9.  Clique duas vezes no ficheiro para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença do utilizador final.

    ![Micro Focus Enterprise Server Setup Screen](media/install-image-1.png)

    Quando a configuração estiver concluída, aparece a seguinte mensagem:

    ![Micro Focus Enterprise Server Setup Screen](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Procurar atualizações

Após a instalação, certifique-se de verificar se existem atualizações adicionais, uma vez que são instaladas algumas pré-requisitos, como o Microsoft C++ Redistributable e o .NET Framework, juntamente com o Enterprise Server.

### <a name="upload-the-license"></a>Faça o upload da licença

1.  Inicie a Micro Focus License Administration.

2.  Selecione **Iniciar** \> **Micro Focus License Manager** License \> **Administration**e, em seguida, clique no separador **Instalar.** Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 caracteres. Por exemplo, para um ficheiro, no **ficheiro Licença,** navegue para o ficheiro * `mflic` carregado previamente para o VM e selecione **Licenças de Instalação**.

    ![Caixa de diálogo de administração de licença de micro foco](media/install-image-3.png)

3.  Verifique se o Servidor da Empresa está carregado. Tente lançar o site Enterprise Server Administration a partir de um navegador utilizando este URL: `http://localhost:86/` . A página de Administração do Servidor da Empresa é apresentada como mostrado.

    ![Página de Administração do Servidor da Empresa](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instale o Enterprise Developer na máquina de programação

1.  Selecione o grupo de recursos criado anteriormente (por exemplo, **RGMicroFocusEntServer),** em seguida, selecione a imagem do desenvolvedor.

2.  Para iniciar sôms na máquina virtual, vá à secção **'Vista Geral'** e selecione **Connect**. Este início de sessão lança uma sessão de PDR. Inscreva-se usando as credenciais que criou para o VM.

3.  A partir da sessão RDP, carregue os dois ficheiros seguintes (arraste e deixe cair se quiser):

    -   `edvs2017.exe`, o ficheiro de instalação do Enterprise Server.

    -   `mflic`, o ficheiro de licença correspondente (Enterprise Developer não carregará sem ele).

4.  Clique duas vezes no ficheiro **edvs2017.exe** para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença do utilizador final. Se quiser, escolha **instalar a Rumba 9.5** para instalar este emulador de terminal, que provavelmente necessitará.

    ![Micro Focus Enterprise Developer para Visual Studio 2017 Caixa de diálogo de configuração](media/install-image-5.png)

5.  Após a configuração estar concluída, aparece a seguinte mensagem:

    ![Configurar mensagem de sucesso](media/install-image-6.png)

6.  Inicie o Gestor de Licenças Micro Focus tal como fez para o Enterprise Server. Escolha **Iniciar** \> **Micro Focus Licença Manager** Licença \> **Administração**, e clique no **separador Instalar.**

7.  Escolha o tipo de formato de licença para carregar: um ficheiro de licença ou um código de licença de 16 caracteres. Por exemplo, para um ficheiro, no **ficheiro Licença,** navegue no `mflic` ficheiro carregado previamente para o VM e selecione  **Licenças de Instalação**.

    ![Caixa de diálogo de administração de licença de micro foco](media/install-image-7.png)

Quando o Enterprise Developer carregar, a sua implantação de um ambiente de desenvolvimento e teste de Micro Focus no Azure está completa!

**Próximos passos**

-   [Configurar o pedido BankDemo](./demo.md)

-   [Executar servidor de empresa em contentores Docker](./run-enterprise-server-container.md)

-   [Migração de aplicações de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
