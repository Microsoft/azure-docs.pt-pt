---
title: Instalar o micro Focus Enterprise Server 4,0 e o desenvolvedor Enterprise 4,0 no Azure | Microsoft Docs
description: Rehospede suas cargas de trabalho de mainframe do IBM z/OS usando o ambiente de desenvolvimento e teste micro Focus em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834565"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalar o micro Focus Enterprise Server 4,0 e o desenvolvedor Enterprise 4,0 no Azure

Este artigo mostra como configurar o [micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e o [micro Focus enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) no Azure.

Uma carga de trabalho comum no Azure é um ambiente de desenvolvimento e teste. Esse cenário é comum porque é tão econômico e fácil de implantar e desmontar. Com o Enterprise Server, o micro Focus criou uma das maiores plataformas de Hospedagem de mainframe disponíveis. Você pode executar cargas de trabalho z/OS em uma plataforma x86 menos dispendiosa no Azure usando VMs (máquinas virtuais) do Windows ou Linux.

Essa configuração usa VMs do Azure que executam a imagem do Windows Server 2016 do Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Essa configuração também se aplica a Azure Stack.

O ambiente de desenvolvimento correspondente para o Enterprise Server é o desenvolvedor corporativo, que é executado no Microsoft Visual Studio 2017 ou posterior, na Comunidade do Visual Studio (gratuito para download) ou eclipse. Este artigo mostra como implantá-lo usando uma máquina virtual do Windows Server 2016 que vem com o Visual Studio 2017 ou posterior instalado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software micro Focus e uma licença válida (ou licença de avaliação). Se você for um cliente do micro Focus existente, entre em contato com seu representante do micro Focus. Caso contrário, [solicite uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Obtenha a documentação do [Enterprise Server e do Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Uma prática recomendada é configurar um túnel de VPN (rede privada virtual) site a site ou um Jumpbox para que você possa controlar o acesso às VMs do Azure.

## <a name="install-enterprise-server"></a>Instalar o Enterprise Server

1. Para maior segurança e capacidade de gerenciamento, considere criar um novo grupo de recursos apenas para este projeto — por exemplo, **RGMicroFocusEntServer**. Use a primeira parte do nome no Azure para escolher o tipo de recurso para facilitar a localização de uma lista.

2. Cria uma máquina virtual. No Azure Marketplace, selecione a máquina virtual e o sistema operacional que você deseja. Aqui está uma configuração recomendada:

    - **Servidor corporativo**: Selecione VM ES2 v3 (com 2 vCPUs e 16 GB de memória) com o Windows Server 2016 e o SQL Server 2017 instalados. Essa imagem está disponível no Azure Marketplace. O servidor corporativo também pode usar o banco de dados SQL do Azure.

    - **Desenvolvedor empresarial**: Selecione VM B2ms (com 2 vCPUs e 8 GB de memória) com o Windows 10 e o Visual Studio instalados. Essa imagem está disponível no Azure Marketplace.

3. Na seção **noções básicas** , insira seu nome de usuário e senha. Selecione a **assinatura** e o **local/região** que você deseja usar para as VMs. Selecione **RGMicroFocusEntServer** para o grupo de recursos.

4. Coloque ambas as VMs na mesma rede virtual para que elas possam se comunicar entre si.

5. Aceite os padrões para o restante das configurações. Lembre-se do nome de usuário e da senha que você cria para o administrador dessas VMs.

6. Quando as máquinas virtuais tiverem sido criadas, abra as portas de entrada 9003, 86 e 80 para HTTP e 3389 para RDP no computador do servidor corporativo e 3389 no computador do desenvolvedor.

7. Para entrar na máquina virtual do servidor corporativo, em portal do Azure, selecione a VM ES2 v3. Vá para a seção **visão geral** e selecione **conectar** para iniciar uma sessão RDP. Entre usando as credenciais que você criou para a VM.

8. Na sessão RDP, carregue os dois arquivos a seguir. Como você está usando o Windows, é possível arrastar e soltar os arquivos na sessão RDP:

    - **es\_40. exe**, o arquivo de instalação do servidor corporativo.

    - **mflic**, o arquivo de licença correspondente — o servidor corporativo não será carregado sem ele.

9. Clique duas vezes no arquivo para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de usuário final.

     ![Tela de instalação do micro Focus Enterprise Server](media/01-enterprise-server.png)

     Quando a instalação for concluída, a seguinte mensagem será exibida:

     ![Tela de instalação do micro Focus Enterprise Server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Procurar atualizações

Após a instalação, certifique-se de verificar se há atualizações adicionais, já que vários pré-requisitos, como o Microsoft C++ redistribuível e o .NET Framework, são instalados juntamente com o Enterprise Server.

### <a name="upload-the-license"></a>Carregar a licença

1. Inicie a administração da licença do micro Focus.

2. Clique em **Iniciar** \> **Gerenciador de licenças do micro Focus gerenciamento** \> de **licenças**e, em seguida, clique na guia **instalar** . Escolha o tipo de formato de licença a ser carregado: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, em **arquivo de licença**, navegue até o arquivo **mflic** carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo administração da licença micro Focus](media/03-enterprise-server.png)

3. Verifique se o servidor corporativo é carregado. Tente iniciar o site de administração do servidor corporativo em um navegador usando <http://localhost:86/>esta URL.  A página de administração do servidor corporativo é exibida conforme mostrado.

     ![Página de administração do servidor corporativo](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instalar o Enterprise Developer no computador do desenvolvedor

1. Selecione o grupo de recursos criado anteriormente (por exemplo, **RGMicroFocusEntServer**) e, em seguida, selecione a imagem do desenvolvedor.

2. Para entrar na máquina virtual, vá para a seção **visão geral** e selecione **conectar**. Essa entrada inicia uma sessão RDP. Entre usando as credenciais que você criou para a VM.

3. Na sessão RDP, carregue os dois arquivos a seguir (arraste e solte, se desejar):

    - **edvs2017. exe**, o arquivo de instalação do servidor corporativo.

    - **mflic**, o arquivo de licença correspondente (o desenvolvedor corporativo não será carregado sem ele).

4. Clique duas vezes no arquivo **edvs2017. exe** para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de usuário final. Se desejar, escolha **instalar Rumba 9,5** para instalar este emulador de terminal, que provavelmente será necessário.

     ![Caixa de diálogo configuração do micro Focus Enterprise Developer para Visual Studio 2017](media/04-enterprise-server.png)

5. Após a conclusão da instalação, a seguinte mensagem será exibida:

     ![Mensagem de instalação bem-sucedida](media/05-enterprise-server.png)

6. Inicie o Gerenciador de licenças do micro Focus da mesma forma que fazia para o Enterprise Server. Escolha **Iniciar** \> a **Administração de licenças**do **Gerenciador** \> de licenças do micro Focus e clique na guia **instalar** .

7. Escolha o tipo de formato de licença a ser carregado: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, em **arquivo de licença**, navegue até o arquivo **mflic** carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo administração da licença micro Focus](media/07-enterprise-server.png)

Quando o Enterprise Developer é carregado, sua implantação de um ambiente de desenvolvimento e teste micro Focus no Azure é concluída!

## <a name="next-steps"></a>Passos Seguintes

- [Configurar o aplicativo de demonstração bancária](./demo.md)
- [Executar o servidor corporativo em contêineres do Docker](./run-enterprise-server-container.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
