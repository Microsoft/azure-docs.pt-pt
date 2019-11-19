---
title: Instalar o cliente chefe do portal do Azure
description: Saiba como implantar e configurar o cliente chefe no portal do Azure
keywords: Azure, chefe, DevOps, cliente, instalar, portal
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: f8707c2fe39fb794381af298c24d27704b1ec255
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158271"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalar o cliente chefe do portal do Azure
Você pode adicionar a extensão de cliente chefe diretamente em um computador Linux ou Windows da portal do Azure. Este artigo orienta você pelo processo usando uma nova máquina virtual do Linux.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Chefe**: se você não tiver uma conta do chefe ativo, Inscreva-se para uma [avaliação gratuita do chefe hospedado](https://manage.chef.io/signup). Para acompanhar as instruções neste artigo, você precisará dos seguintes valores de sua conta do chefe:
  - chave de organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalar a extensão chefe em uma nova máquina virtual do Linux
Nesta seção, você primeiro usará o portal do Azure para criar um computador Linux. Durante o processo, você também verá como instalar a extensão chefe na nova máquina virtual.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione a opção **máquinas virtuais** . Se a opção **máquinas virtuais** não estiver presente, selecione **todos os serviços** e, em seguida, selecione **máquinas virtuais**.

1. Na guia **máquinas virtuais** , selecione **Adicionar**.

    ![Adicionar uma nova máquina virtual na portal do Azure](./media/chef-extension-portal/add-vm.png)

1. Na guia **computação** , selecione o sistema operacional desejado. Para esta demonstração, o **Ubuntu Server** está selecionado.

1. Na guia **servidor do Ubuntu** , selecione **Ubuntu Server 16, 4 LTS**.

    ![Ao criar uma máquina virtual do Ubuntu, especifique a versão de que você precisa](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na guia **LTS do Ubuntu Server 16, 4** , selecione **criar**.

    ![O Ubuntu fornece informações adicionais sobre seus produtos](./media/chef-extension-portal/create-vm.png)

1. Na guia **criar máquina virtual** , selecione **básico**.

1. Na guia **noções básicas** , especifique os valores a seguir e selecione **OK**.

   - **Nome** – Insira um nome para a nova máquina virtual.
   - **Tipo de disco da VM** -especifique **SSD** ou **HDD** para o tipo de disco de armazenamento. Para obter mais informações sobre os tipos de disco de máquina virtual no Azure, consulte o artigo [selecionar um tipo de disco](../virtual-machines/windows/disks-types.md).
   - **Nome de usuário** – Insira um nome de usuário que receba privilégios de administrador na máquina virtual.
   - **Tipo de autenticação** – selecione **senha**. Você também pode selecionar a **chave pública SSH**e fornecer um valor de chave pública SSH. Para fins desta demonstração (e nas capturas de tela), a **senha** está selecionada.
   - **Senha** e **Confirmar senha** – Insira uma senha para o usuário.
   - **Faça logon com Azure Active Directory** -selecione **desabilitado**.
   - **Assinatura** – selecione a assinatura do Azure desejada, se você tiver mais de uma.
   - **Grupo de recursos** – Insira um nome para seu grupo de recursos.
   - **Local** -selecione **leste dos EUA**.

     ![Guia básico para criar uma máquina virtual](./media/chef-extension-portal/add-vm-basics.png)

1. Na guia **escolher um tamanho** , selecione o tamanho da máquina virtual e selecione **selecionar**.

1. Na guia **configurações** , a maioria dos valores será preenchida com base nos valores selecionados nas guias anteriores. Selecione **Extensions** (Extensões).

     ![As extensões são adicionadas às máquinas virtuais por meio da guia Configurações](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na guia **extensões** , selecione **adicionar extensão**.

     ![Selecione Adicionar extensão para adicionar uma extensão a uma máquina virtual](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na guia **novo recurso** , selecione **extensão chefe do Linux (1.2.3)** .

     ![Chefe tem extensões para máquinas virtuais Linux e Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na guia **extensão do chefe do Linux** , selecione **criar**.

1. Na guia **extensão de instalação** , especifique os valores a seguir e selecione **OK**.

    - **URL do servidor chefe** -Insira a URL do servidor chefe que inclui o nome da organização, por exemplo, *https://api.chef.io/organization/mycompany* .
    - **Nome do nó chefe** -Insira o nome do nó chefe. Pode ser qualquer valor.
    - **Executar lista** – Insira a lista de execuções do chefe que é adicionada ao computador. Isso pode ser deixado em branco.
    - **Nome do cliente de validação** – Insira o nome do cliente de validação do chefe. por exemplo, *Tarcher-Validator*.
    - **Chave de validação** – selecione um arquivo que contém a chave de validação usada ao inicializar seus computadores.
    - **Arquivo de configuração do cliente** – selecione um arquivo de configuração para o chefe-Client. Isso pode ser deixado em branco.
    - **Versão do cliente chefe** – Insira a versão do cliente chefe a ser instalada. Isso pode ser deixado em branco. Um valor em branco instala a versão mais recente.
    - **Modo de verificação de SSL** – selecione **nenhum** ou **par**. *Nenhum* foi selecionado para a demonstração.
    - **Ambiente chefe** – Insira o ambiente chefe do qual este nó deve ser membro. Isso pode ser deixado em branco.
    - **Segredo Databag criptografado** – selecione um arquivo que contenha o segredo para o Databag criptografado ao qual este computador deve ter acesso. Isso pode ser deixado em branco.
    - **Certificado SSL do servidor chefe** -selecione o certificado SSL atribuído ao servidor chefe. Isso pode ser deixado em branco.

      ![Instalando o servidor chefe em uma máquina virtual Linux](./media/chef-extension-portal/install-extension.png)

1. Ao retornar para a guia **extensões** , selecione **OK**.

1. Ao retornar para a guia **configurações** , selecione **OK**.

1. Ao retornar para a guia **criar** (representa um resumo das opções que você selecionou e inseriu), verifique as informações, bem como a **termos de uso**e selecione **criar**.

Quando o processo de criação e implantação da máquina virtual com a extensão chefe estiver concluído, uma notificação indicará o êxito ou a falha da operação. Além disso, a página de recursos para a nova máquina virtual é aberta automaticamente no portal do Azure assim que ela é criada.

![Instalando o servidor chefe em uma máquina virtual Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma máquina virtual do Windows no Azure usando o chefe](/azure/virtual-machines/windows/chef-automation)
