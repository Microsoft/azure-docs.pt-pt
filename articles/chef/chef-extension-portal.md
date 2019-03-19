---
title: Instalar o cliente do Chef no portal do Azure
description: Saiba como implementar e configurar o cliente do Chef no portal do Azure
keywords: azure, chef, devops, client, install, portal
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: cf7afb50006fb273b4d685f9e4259be1cb60fe4e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084747"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalar o cliente do Chef no portal do Azure
Pode adicionar a extensão de cliente do Chef diretamente numa máquina Linux ou Windows a partir do portal do Azure. Este artigo orienta-o processo usando uma nova máquina virtual de Linux.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Chef**: Se não tiver uma conta ativa do Chef, inscreva-se para uma [versão de avaliação do Chef alojado gratuita](https://manage.chef.io/signup). Para seguir as instruções neste artigo, terá os seguintes valores da sua conta do Chef:
  - chave de organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalar a extensão do Chef na nova máquina virtual do Linux
Nesta secção, primeiro usará o portal do Azure para criar uma máquina Linux. Durante o processo, também verá como instalar a extensão da Chef sobre a nova máquina virtual.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione o **máquinas virtuais** opção. Se o **máquinas virtuais** opção não está presente, selecione **todos os serviços** e, em seguida, selecione **máquinas virtuais**.

1. Sobre o **máquinas virtuais** separador, selecione **Add**.

    ![Adicionar uma nova máquina virtual no portal do Azure](./media/chef-extension-portal/add-vm.png)

1. Sobre o **computação** separador, selecione o sistema operativo pretendido. Para esta demonstração **Ubuntu Server** está selecionada.

1. Sobre o **Ubuntu Server** separador, selecione **Ubuntu Server 16.04 LTS**.

    ![Ao criar uma máquina virtual do Ubuntu, especifique a versão que precisa](./media/chef-extension-portal/ubuntu-server-version.png)

1. Sobre o **Ubuntu Server 16.04 LTS** separador, selecione **criar**.

    ![Ubuntu fornece informações adicionais sobre seus produtos](./media/chef-extension-portal/create-vm.png)

1. Sobre o **criar máquina virtual** separador, selecione **Noções básicas**.

1. Sobre o **Noções básicas** separador, especifique os seguintes valores e, em seguida, selecione **OK**.

   - **Nome** -introduza um nome para a nova máquina virtual.
   - **Tipo de disco VM** -especifique **SSD** ou **HDD** para o tipo de disco de armazenamento. Para obter mais informações sobre os tipos de disco da máquina virtual no Azure, consulte o artigo [selecionar um tipo de disco](../virtual-machines/windows/disks-types.md).
   - **Nome de utilizador** -introduza um nome de utilizador que é concedido privilégios de administrador na máquina virtual.
   - **Tipo de autenticação** - selecione **palavra-passe**. Também pode selecionar **chave pública SSH**e fornecer um valor de chave público SSH. Para fins desta demonstração (e nas capturas de ecrã), **palavra-passe** está selecionada.
   - **Palavra-passe** e **Confirmar palavra-passe** -introduza uma palavra-passe do utilizador.
   - **Inicie sessão com o Azure Active Directory** - selecione **desativado**.
   - **Subscrição** -selecione a subscrição do Azure pretendida, se tiver mais do que um.
   - **Grupo de recursos** -introduza um nome para o grupo de recursos.
   - **Localização** - selecione **E.U.A. Leste**.

     ![Guia de conceitos básicos para criar uma máquina virtual](./media/chef-extension-portal/add-vm-basics.png)

1. Sobre o **escolher um tamanho** separador, selecione o tamanho da máquina virtual e, em seguida, selecione **selecione**.

1. Sobre o **definições** guia, a maioria dos valores será preenchido para com base nos valores que selecionou no separadores anteriores. Selecione **Extensions** (Extensões).

     ![As extensões são adicionadas a máquinas virtuais através do separador de definições](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Sobre o **extensões** separador, selecione **Adicionar extensão**.

     ![Selecione Adicionar extensão para adicionar uma extensão para uma máquina virtual](./media/chef-extension-portal/add-vm-add-extension.png)

1. Sobre o **novo recurso** separador, selecione **Linux Chef extensão (1.2.3)**.

     ![Chef tem extensões para máquinas virtuais do Linux e Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Sobre o **extensão de Linux Chef** separador, selecione **criar**.

1. Sobre o **instalar a extensão** separador, especifique os seguintes valores e, em seguida, selecione **OK**.

    - **URL do servidor chef** -introduza o URL do servidor Chef que inclui o nome da organização, por exemplo, *https://api.chef.io/organization/mycompany*.
    - **Nome do nó chef** -introduza o nome do nó da Chef. Isso pode ser qualquer valor.
    - **Lista de execuções** -introduza a lista de Chef run é adicionada à máquina. Isso pode ser deixado em branco.
    - **Nome do cliente de validação** -introduza o nome do cliente de validação da Chef. Por exemplo, *tarcher validador*.
    - **Chave de validação** -selecione um ficheiro que contém a chave de validação utilizada quando as máquinas de inicialização.
    - **Ficheiro de configuração de cliente** -selecione um ficheiro de configuração para o cliente do chef. Isso pode ser deixado em branco.
    - **Versão do cliente do chef** -introduza a versão do cliente do chef para instalar. Isso pode ser deixado em branco. Um valor em branco instala a versão mais recente.
    - **Modo de verificação de SSL** -selecione **None** ou **ponto a ponto**. *Nenhum* tiver sido selecionada para a demonstração.
    - **Ambiente do chef** -introduza o ambiente do Chef este nó deve ser um membro do. Isso pode ser deixado em branco.
    - **Encriptados Databag segredo** -selecione um ficheiro que contém o segredo para a Databag encriptados nesta máquina devem ter acesso a. Isso pode ser deixado em branco.
    - **Certificado SSL de servidor do chef** -selecione o certificado de SSL atribuído ao servidor da Chef. Isso pode ser deixado em branco.

      ![Instalar o servidor de Chef numa máquina virtual Linux](./media/chef-extension-portal/install-extension.png)

1. Ao retornar para o **extensões** separador, selecione **OK**.

1. Ao retornar para o **configurações** separador, selecione **OK**.

1. Ao retornar para o **Create** separador (esta representa um resumo das opções selecionadas e inserido), verifique as informações, bem como a **termos de utilização**e selecione **criar**.

Quando o processo de criação e implementação da máquina virtual com a extensão do Chef estiver concluído, uma notificação indica o êxito ou falha da operação. Além disso, a página de recursos para a nova máquina virtual é aberta automaticamente no portal do Azure depois de ser criado.

![Instalar o servidor de Chef numa máquina virtual Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma máquina virtual do Windows no Azure utilizando o Chef](/azure/virtual-machines/windows/chef-automation)
