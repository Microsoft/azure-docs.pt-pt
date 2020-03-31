---
title: Instale o cliente Chef do portal Azure
description: Saiba como implementar e configurar o seu cliente Chef a partir do portal Azure
keywords: azure, chef, devops, cliente, instalação, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586364"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instale o cliente Chef do portal Azure
Você pode adicionar a extensão do cliente Chef diretamente em uma máquina Linux ou Windows a partir do portal Azure. Este artigo acompanha-o através do processo usando uma nova máquina virtual Linux.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Chef**: Se você não tem uma conta de Chef ativa, inscreva-se para um [teste gratuito de Chef Hospedado.](https://manage.chef.io/signup) Para acompanhar as instruções deste artigo, você precisará dos seguintes valores da sua conta chef:
  - chave organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instale a extensão do Chef numa nova máquina virtual Linux
Nesta secção, você primeiro usará o portal Azure para criar uma máquina Linux. Durante o processo, você também vai ver como instalar a extensão chef na nova máquina virtual.

1. Navegue pelo [portal Azure.](https://portal.azure.com)

1. A partir do menu à esquerda, selecione a opção **máquinas Virtuais.** Se a opção **máquinas Virtuais** não estiver presente, selecione Todos os **serviços** e, em seguida, selecione **máquinas Virtuais**.

1. No separador **máquinas Virtuais,** selecione **Adicionar**.

    ![Adicione uma nova máquina virtual no portal Azure](./media/chef-extension-portal/add-vm.png)

1. No separador **Compute,** selecione o sistema operativo pretendido. Para esta demonstração, **o Ubuntu Server** é selecionado.

1. No separador **Ubuntu Server,** selecione **Ubuntu Server 16.04 LTS**.

    ![Ao criar uma máquina virtual Ubuntu, especifique a versão de que necessita](./media/chef-extension-portal/ubuntu-server-version.png)

1. No separador **Ubuntu Server 16.04 LTS,** **selecione Criar**.

    ![Ubuntu fornece informações adicionais sobre o seu produto](./media/chef-extension-portal/create-vm.png)

1. No separador **Criar máquina virtual,** selecione **Basics**.

1. No separador Basics, especifique os **seguintes valores** e, em seguida, selecione **OK**.

   - **Nome** - Introduza um nome para a nova máquina virtual.
   - Tipo de **disco VM** - Especifique o **SSD** ou **o HDD** para o tipo de disco de armazenamento. Para obter mais informações sobre os tipos de discos de máquinas virtuais no Azure, consulte o artigo [Selecione um tipo](../virtual-machines/windows/disks-types.md)de disco .
   - **Nome** do utilizador - Introduza um nome de utilizador que lhe seja concedido privilégios de administrador na máquina virtual.
   - **Tipo de autenticação** - Selecione **Password**. Também pode selecionar **a chave pública SSH**e fornecer um valor-chave público ssh. Para efeitos desta demonstração (e nas imagens), a **palavra-passe** é selecionada.
   - **Palavra-passe** e **palavra-passe Confirme** - Introduza uma palavra-passe para o utilizador.
   - **Inicie sessão com o Diretório Ativo Azure** - Selecione **Desativado**.
   - **Subscrição** - Selecione a subscrição desejada do Azure, se tiver mais de um.
   - **Grupo de recursos** - Insira um nome para o seu grupo de recursos.
   - **Localização** - Selecione **East US**.

     ![Separador básico para criar uma máquina virtual](./media/chef-extension-portal/add-vm-basics.png)

1. No separador **Escolha um separador de tamanho,** selecione o tamanho da máquina virtual e, em seguida, selecione **Selecione**.

1. No separador **Definições,** a maioria dos valores serão povoados para si com base nos valores selecionados nos separadores anteriores. Selecione **Extensions** (Extensões).

     ![As extensões são adicionadas às máquinas virtuais através do separador Definições](./media/chef-extension-portal/add-vm-select-extensions.png)

1. No separador **Extensões,** selecione **Adicionar extensão**.

     ![Selecione Adicionar extensão para adicionar uma extensão a uma máquina virtual](./media/chef-extension-portal/add-vm-add-extension.png)

1. No **separador de recursos Novo,** selecione **Extensão do Chef Linux (1.2.3)**.

     ![Chef tem extensões para máquinas virtuais Linux e Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. No separador de extensão do **Chef Linux,** selecione **Criar**.

1. No separador **de extensão Instalar,** especifique os seguintes valores e, em seguida, selecione **OK**.

    - **Chef Server URL** - Introduza o URL do Chef *https://api.chef.io/organization/mycompany*Server que inclui o nome da organização, por exemplo, .
    - **Nome do Chef Node** - Insira o nome do Chef Node.
    - Lista de **corridas** - Insira a lista de corridas do Chef que é adicionada à máquina. Este valor pode ser deixado em branco.
    - **Validação nome do cliente** - Insira o Nome do Cliente de Validação do Chef. por exemplo, `tarcher-validator`.
    - **Chave de validação** - Selecione um ficheiro que contenha a chave de validação utilizada ao amarrar as suas máquinas.
    - Arquivo de **configuração do cliente** - Selecione um ficheiro de configuração para chef-cliente. Este valor pode ser deixado em branco.
    - **Versão chef Cliente** - Insira a versão do cliente chef para instalar. Este valor pode ser deixado em branco, que instala a versão mais recente.
    - **Modo de Verificação SSL** - Selecione **nenhum** ou **par**. *Nenhum* foi selecionado para a demonstração.
    - **Chef Ambiente** - Insira o ambiente chef este nó deve ser um membro de. Este valor pode ser deixado em branco.
    - Segredo de saco de **dados encriptado** - Selecione um ficheiro que contenha o segredo para o saco de dados encriptado que esta máquina precisa de aceder. Este valor pode ser deixado em branco.
    - **Certificado SSL chef Server** - Selecione o certificado SSL atribuído ao seu Chef Server. Este valor pode ser deixado em branco.

      ![Instalação do Chef Server numa máquina virtual Linux](./media/chef-extension-portal/install-extension.png)

1. Quando o separador **Extensões** se mostrar, selecione **OK**.

1. Quando o separador **Definições** se leciona **OK**.

1. Quando o separador **Criar** aparece, vê um resumo das opções selecionadas e inseridas. Verifique as informações, bem como os **Termos de Utilização,** e selecione **Criar**.

Quando o processo de criação e implantação da máquina virtual com a Extensão do Chef estiver concluído, uma notificação indica o sucesso ou falha da operação. Além disso, a página de recursos da nova máquina virtual abre automaticamente no portal Azure uma vez criada.

![Instalação do Chef Server numa máquina virtual Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Crie uma máquina virtual Windows em Azure usando chef](chef-automation.md)