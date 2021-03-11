---
title: Quickstart - Criar uma máquina virtual de computação confidencial Azure com marketplace
description: Começa com as tuas implementações aprendendo a criar rapidamente uma máquina virtual de computação confidencial com o Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: aba23b67574fb74b7cd571dc5d4642bb8b991b93
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566639"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Quickstart: Implementar um VM de Computação Confidencial Azure no Mercado

Começa com a computação confidencial da Azure utilizando o Azure Marketplace para criar uma máquina virtual (VM) apoiada pela Intel SGX. Em seguida, instalará o Kit de Desenvolvimento de Software Open Enclave (SDK) para configurar o seu ambiente de desenvolvimento. 

Este tutorial é recomendado se quiser começar rapidamente a implementar uma máquina virtual de computação confidencial. Os VMs são executados em hardware de especialidade e requerem entradas de configuração específicas para funcionar como pretendido. A oferta de mercado descrita neste quickstart facilita a sua implementação, limitando a entrada do utilizador.

Se estiver interessado em implementar uma máquina virtual de computação confidencial com uma configuração mais personalizada, siga os [passos de implementação da máquina virtual Azure Portal Confidential Compute](quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No topo, **digite Azure computação confidencial** na barra de pesquisa.

1. Selecione **Azure computação confidencial (Máquina Virtual)** na secção **Marketplace.** 

    ![Selecione Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Na página de aterragem de implementação de computação confidencial Azure, selecione **Criar**.
 

## <a name="configure-your-virtual-machine"></a>Configure a sua máquina virtual

1. No **separador Básicos,** selecione o seu Grupo **de Subscrição** e **Recursos.** O seu grupo de recursos deve estar vazio para colocar uma máquina virtual a partir deste modelo.

1. Escreva ou selecione os seguintes valores:

   * **Região**: Selecione a região de Azure que é a certa para si.

        > [!NOTE]
        > Máquinas virtuais de computação confidencial só funcionam em hardware especializado disponível em regiões específicas. Para as últimas regiões disponíveis para DCsv2-Series VMs, consulte [as regiões disponíveis.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)
    
    * **Escolha imagem**: Selecione qualquer imagem. Se quiser completar este tutorial específico, selecione Ubuntu 18.04 (Gen 2). Caso contrário, será redirecionado nos passos apropriados abaixo. 

    * **Nome da máquina virtual,** insira um nome para o seu novo VM.

    * **Tipo de autenticação**: Selecione **a chave pública SSH** se estiver a criar um Linux VM. 

         > [!NOTE]
         > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nome de utilizador**: Introduza o nome do administrador para o VM.

    * **Chave pública SSH**: Se aplicável, insira a sua chave pública RSA.
    
    * **Senha**: Se aplicável, introduza a sua palavra-passe para autenticação.
 
1. Selecione o **seguinte: Botão de definição de máquina virtual** na parte inferior do ecrã.

    > [!IMPORTANT]
    > Aguarde a atualização da página. *Não deve* ver uma mensagem que diga "Os VMs da série DCsv2 da Computação Confidencial estão disponíveis num número limitado de regiões." Se esta mensagem persistir, volte à página anterior e selecione uma região DCsv2-Series disponível.

1. Para **alterar o tamanho,** escolha um VM com capacidades confidenciais de computação no seletor de tamanhos. 

    > [!TIP]
    > Você deve ver tamanhos **DC1s_v2,** **DC2s_v2,** **DC4s_V2** e **DC8_v2.** Estes são os únicos tamanhos de máquinas virtuais que suportam atualmente a computação confidencial. [Saiba mais](virtual-machine-solutions.md).

1. Para **o tipo de disco OS,** selecione um tipo de disco.

1. Para **Rede Virtual,** crie um novo ou escolha entre um recurso existente.

1. Para **a Subnet,** crie uma nova ou escolha a partir de um recurso existente.

1. Para **selecionar portas de entrada pública,** escolha **SSH(Linux)/RDP(Windows)**. Neste arranque rápido, este passo é necessário para ligar ao VM e completar a configuração SDK do Enclave Aberto. 

1. Para **o Boot Diagnostics,** deixe-o desativado para este arranque rápido. 

1. Selecione **Rever + criar**.

1. No **painel 'Rever + criar',** selecione **Criar.**

> [!NOTE]
> Siga para a secção seguinte e continue com este tutorial se tiver implantado um Linux VM. Se tiver implantado um VM do Windows, [siga estes passos para ligar ao seu VM do Windows](../virtual-machines/windows/connect-logon.md) e, em seguida, [instale o OE SDK no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP público do seu VM no portal Azure, na secção Visão Geral da sua máquina virtual.

![Endereço IP no portal do Azure](media/quick-create-portal/public-ip-virtual-machine.png)

Se estiver a correr no Windows e não tiver uma concha BASH, instale um cliente SSH, como o PuTTY.

1. [Transferir e instalar PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Selecione **Abrir** e insira o seu nome de utilizador e palavra-passe nas indicações.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Se vir um alerta de segurança PuTTY sobre a chave de anfitrião do servidor não estar em cache no registo, escolha entre as seguintes opções. Se confiar neste anfitrião, selecione **Sim** para adicionar a chave à cache do PuTTy e continue a ligar. Se pretender continuar a ligar apenas uma vez, sem adicionar a chave à cache, selecione **Nº**. Se não confia neste anfitrião, **selecione Cancelar** para abandonar a ligação.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instale o SDK do Enclave Aberto (OE SDK) <a id="Install"></a>

Siga as instruções passo a passo para instalar o [OE SDK](https://github.com/openenclave/openenclave) na sua DCsv2-Series máquina virtual que executa uma imagem Ubuntu 18.04 LTS Gen 2. 

Se a sua máquina virtual funciona no Ubuntu 16.04 LTS Gen 2, terá de seguir [as instruções de instalação para Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md). 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Configurar os repositórios Intel e Microsoft APT

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
> Utilize o mais recente controlador Intel SGX DCAP do [site SGX da Intel.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Instalar os pacotes e dependências do Intel e do Enclave Aberto

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Este passo também instala o pacote [az-dcap-cliente](https://github.com/microsoft/azure-dcap-client) que é necessário para a realização de atestado remoto em Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Verifique a instalação Open Enclave SDK**

Consulte [a Utilização do SDK do Enclave Aberto](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) no GitHub para verificar e utilizar o SDK instalado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. 

Selecione o grupo de recursos para a máquina virtual e, em seguida, **selecione Delete**. Confirme o nome do grupo de recursos para terminar a eliminação dos recursos.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, implementou uma máquina virtual de computação confidencial e instalou o Open Enclave SDK. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, consulte [Soluções em Máquinas Virtuais.](virtual-machine-solutions.md) 

Descubra como pode construir aplicações de computação confidencial, continuando com as amostras SDK do Open Enclave no GitHub. 

> [!div class="nextstepaction"]
> [Edifício open enclave amostras SDK](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
