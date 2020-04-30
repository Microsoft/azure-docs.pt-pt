---
title: Quickstart - Criar uma máquina virtual de computação confidencial Azure no portal Azure
description: Inicie-se com as suas implementações aprendendo a criar rapidamente uma máquina virtual de computação confidencial no portal Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 49824b324df044ae2883ab6844dd321be27cbcbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82149243"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Quickstart: Implemente um VM de computação confidencial Azure no portal Azure

Inicie-se com a computação confidencial azure utilizando o portal Azure para criar uma máquina virtual (VM) apoiada pela Intel SGX. Em seguida, instalará o Kit de Desenvolvimento de Software Open Enclave (SDK) para configurar o seu ambiente de desenvolvimento. 

Este tutorial é recomendado para si se estiver interessado em implementar uma máquina virtual de computação confidencial com configuração personalizada. Caso contrário, recomendamos seguir as [etapas confidenciais de implementação de máquinas virtuais computorizados para o mercado comercial da Microsoft](quick-create-marketplace.md).


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No topo, selecione **Criar um recurso**.

1. No painel **marketplace,** **selecione Compute** à esquerda.

1. Encontre e selecione **máquina Virtual**.

    ![Implementar uma VM](media/quick-create-portal/compute-virtual-machine.png)

1. Na página de aterragem da máquina Virtual, selecione **Criar**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Configure uma máquina virtual de computação confidencial

1. No separador **Basics,** selecione o seu Grupo **de Subscrição** e **Recursos**.

1. Para o nome da **máquina virtual,** introduza um nome para o seu novo VM.

1. Escreva ou selecione os seguintes valores:

   * **Região**: Selecione a região azure que é a certa para si.

        > [!NOTE]
        > As máquinas virtuais de computação confidencial funcionam apenas com hardware especializado disponível em regiões específicas. Para as regiões mais recentes disponíveis para VMs série DCsv2, consulte [as regiões disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configure a imagem do sistema operativo que gostaria de utilizar para a sua máquina virtual.

    * **Escolha imagem**: Para este tutorial, selecione Ubuntu 18.04 LTS. Também pode selecionar o Windows Server 2019, o Windows Server 2016 ou o Ubuntu 16.04 LTS. Se optar por fazê-lo, será redirecionado para este tutorial em conformidade.
    
    * **Alternar a imagem para a Gen 2**: Máquinas virtuais de computação confidencial apenas funcionam em imagens da [Geração 2.](../virtual-machines/linux/generation-2.md) Certifique-se de que a imagem selecionada é uma imagem gen 2. Clique no separador **Avançado** acima, onde está a configurar a máquina virtual. Desça até encontrar a secção com a etiqueta "Geração VM". Selecione gen 2 e, em seguida, volte ao separador **Basics.**
    

        ![Separador Avançado](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Geração de VMs](media/quick-create-portal/gen2-virtual-machine.png)

    * **Voltar à configuração básica:** Volte ao separador **Basics** utilizando a navegação na parte superior.

1. Escolha uma máquina virtual com capacidades confidenciais de computação no seletor de tamanho, escolhendo o tamanho da **mudança**. No seletor de tamanho da máquina virtual, clique em **limpar todos os filtros**. Escolha **adicionar filtro,** selecione **Família** para o tipo de filtro e, em seguida, selecione apenas **computação Confidencial**.

    ![VMs série DCsv2](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Deve ver tamanhos **DC1s_v2,** **DC2s_v2,** **DC4s_V2**e **DC8_v2.** Estes são os únicos tamanhos de máquinas virtuais que atualmente suportam computação confidencial. [Saiba mais](virtual-machine-solutions.md).

1. Preencha as seguintes informações:

   * Tipo de **autenticação**: Selecione **a chave pública SSH** se estiver a criar um VM Linux. 

        > [!NOTE]
         > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

    * **Nome de utilizador**: Introduza o nome do Administrador para o VM.

    * **Chave pública SSH**: Se aplicável, introduza a sua chave pública RSA.
    
    * **Palavra-passe**: Se aplicável, introduza a sua palavra-passe para autenticação.

    * **Portas**de entrada pública : Escolha **permitir portas selecionadas** e selecionar **SSH (22)** e **HTTP (80)** na lista de portas de **entrada pública Select.** Se estiver a implementar um Windows VM, selecione **HTTP (80)** e **RDP (3389)**. Neste arranque rápido, este passo é necessário para ligar ao VM e completar a configuração Open Enclave SDK. 

     ![Portas de entrada](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Faça alterações no separador **Discos.**

   * Se escolheu um **DC1s_v2**, **DC2s_v2**, **DC4s_V2** máquina virtual, escolha um tipo de disco que seja **SSD Standard** ou **SSD Premium**. 
   * Se escolheu uma máquina virtual **DC8_v2,** escolha **o Standard SSD** como o seu tipo de disco.

1. Faça quaisquer alterações que pretenda para as definições nos seguintes separadores ou mantenha as definições predefinidas.

    * **Redes**
    * **Gestão**
    * **Config convidado**
    * **Etiquetas**

1. Selecione **Rever + criar**.

1. Na **Análise + criar** painel, selecione **Criar**.

> [!NOTE]
> Dirija-se à secção seguinte e continue com este tutorial se tiver implantado um VM Linux. Se implementou um VM do Windows, [siga estes passos para se ligar ao seu Windows VM](../virtual-machines/windows/connect-logon.md) e, em seguida, [instale o OE SDK no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP público do seu VM no portal Azure, sob a secção de visão geral da sua máquina virtual.

[!div class="mx-imgBorder"]
![Endereço IP no portal do Azure](media/quick-create-portal/public-ip-virtual-machine.png)

Se estiver a correr no Windows e não tiver uma concha BASH, instale um cliente SSH, como o PuTTY.

1. [Transferir e instalar PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Selecione **Abrir** e introduza o seu nome de utilizador e palavra-passe nas instruções.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Se vir um alerta de segurança PuTTY sobre a chave hospedeira do servidor não estar em cache no registo, escolha entre as seguintes opções. Se confia neste anfitrião, selecione **Sim** para adicionar a chave à cache de PuTTy e continuar a ligar. Se quiser continuar a ligar apenas uma vez, sem adicionar a chave à cache, selecione **No**. Se não confia neste hospedeiro, selecione **Cancelar** para abandonar a ligação.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instale o Open Enclave SDK (OE SDK)<a id="Install"></a>

Siga as instruções passo a passo para instalar o [OE SDK](https://github.com/openenclave/openenclave) na sua máquina virtual dCsv2-Series executando uma imagem Ubuntu 18.04 LTS Gen 2. 

Se a sua máquina virtual funciona ré em Ubuntu 16.04 LTS Gen 2, terá de seguir as instruções de [instalação para Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md).

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Configure os Repositórios Intel e Microsoft APT

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Instale o controlador Intel SGX DCAP

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Utilize o mais recente controlador Intel SGX DCAP a partir [do site SGX da Intel](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Instale os pacotes e dependências da Intel e do Enclave Aberto

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Este passo também instala o pacote [az-dcap-cliente](https://github.com/microsoft/azure-dcap-client) que é necessário para realizar atestado remoto em Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Verifique a instalação Open Enclave SDK**

Consulte [a utilização do Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) no GitHub para verificar e utilizar o SDK instalado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. 

Selecione o grupo de recursos para a máquina virtual e, em seguida, selecione **Eliminar**. Confirme o nome do grupo de recursos para terminar a adesão dos recursos.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou uma máquina virtual de computação confidencial e instalou o Open Enclave SDK. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, consulte [Soluções em Máquinas Virtuais](virtual-machine-solutions.md). 

Se implementou um VM do Windows, aprenda a construir aplicações com as [amostras OE SDK para Windows](https://github.com/openenclave/openenclave/blob/master/samples/README_Windows.md) no GitHub. 

Descubra como pode construir aplicações confidenciais de computação no Linux, continuando com as amostras Open Enclave SDK Linux no GitHub. 

> [!div class="nextstepaction"]
> [Construção de amostras sdk enclave aberto em Linux](https://github.com/openenclave/openenclave/blob/master/samples/README_Linux.md)
